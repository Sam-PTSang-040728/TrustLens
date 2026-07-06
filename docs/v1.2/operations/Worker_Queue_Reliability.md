# Độ tin cậy Queue và Worker v1.2

**Kiến trúc pilot:** bảng `processing_jobs` là durable queue; worker claim job và gọi canonical analysis pipeline.

## 1. Invariants

| ID | Quy tắc |
|---|---|
| Q-INV-001 | Mỗi submission chỉ có tối đa một active job |
| Q-INV-002 | Một job chỉ có một worker owner tại một thời điểm |
| Q-INV-003 | Completed job luôn có report ID |
| Q-INV-004 | Retry tạo job mới và giữ lineage |
| Q-INV-005 | Worker restart không làm mất queued job |
| Q-INV-006 | Stale recovery không chạy trùng job còn sống |
| Q-INV-007 | Mọi state transition được persist và audit phù hợp |
| Q-INV-008 | Provider retry không làm sai idempotency của toàn pipeline |

## 2. Claim protocol

Worker claim bằng transaction và row lock `FOR UPDATE SKIP LOCKED`.

Khi claim phải ghi:

- `claimed_by`;
- `attempt_count`;
- `started_at`;
- `heartbeat_at`;
- state đầu tiên;
- correlation/job context cho log.

Không giữ database transaction mở trong toàn bộ thời gian pipeline.

## 3. Heartbeat và lease

Chỉ ghi heartbeat tại thời điểm claim là chưa đủ cho job dài.

Yêu cầu:

- heartbeat interval nhỏ hơn đáng kể `JOB_STALE_AFTER_MINUTES`;
- cập nhật heartbeat ở các stage dài và trong vòng lặp metadata;
- stale detector chỉ requeue khi lease hết hạn;
- worker hoàn thành hoặc fail phải clear/close lease theo policy;
- clock sử dụng UTC và đồng bộ.

Khuyến nghị:

```text
heartbeat interval <= stale threshold / 3
```

## 4. Retry policy

Phân biệt:

- **User retry:** tạo job mới từ terminal job, có `retry_of_job_id`.
- **Infrastructure retry:** cùng job được requeue do worker chết hoặc lỗi transient.
- **Provider retry:** retry request bên ngoài với timeout/backoff giới hạn.

Không retry tự động các lỗi deterministic như unsupported type hoặc no text layer.

Backoff nên có jitter. Khi vượt `JOB_MAX_ATTEMPTS`, job chuyển terminal failure và cần manual review/dead-letter policy.

## 5. Idempotency

Pipeline có các bước xóa/ghi lại citation và metadata. Vì vậy cần khóa execution theo job/submission và test crash recovery.

Mỗi stage phải trả lời:

- chạy lại có tạo duplicate không;
- record nào được upsert/xóa;
- report cũ có bị ghi đè không;
- audit có lặp sai không;
- provider request có thể cache không.

Nếu report được rebuild, phải bảo toàn version và xác định report history policy.

## 6. Failure handling

| Loại lỗi | Ví dụ | Hành vi |
|---|---|---|
| Validation | file chưa clean | terminal, không retry tự động |
| Extraction | malformed/no text | terminal hoặc manual review |
| Provider transient | timeout/429 | retry giới hạn + fallback |
| Scoring/config | invalid config | safe fallback hoặc terminal theo invariant |
| Worker crash | process killed | stale recovery |
| Database unavailable | connection failure | không đánh dấu completed giả |
| Report build | export/report persist fail | terminal, không để completed thiếu report |

## 7. Metrics và alerting

Tối thiểu:

- queued count;
- running count theo stage;
- oldest queued age;
- job duration p50/p95;
- failure rate theo error code;
- retry count;
- stale recovery count;
- provider latency/429/error;
- worker heartbeat age;
- completed-without-report invariant violations.

Alert khi:

- oldest queued age vượt ngưỡng;
- không có worker heartbeat;
- failure rate tăng đột biến;
- stale recovery lặp lại;
- attempt count gần giới hạn;
- disk/storage gần đầy.

## 8. Required tests

1. Hai worker claim đồng thời một queue.
2. Kill worker giữa extraction và metadata.
3. Job dài hơn stale threshold nhưng heartbeat còn sống.
4. Provider timeout rồi recovery.
5. Database mất kết nối khi persist report.
6. Retry terminal job và kiểm tra lineage.
7. Duplicate analyze request trên cùng submission.
8. Completed job thiếu report phải bị phát hiện.

## 9. Operational commands

```bash
# Worker loop
python -m app.workers.tasks

# One batch for diagnosis
python -m app.workers.tasks --once --batch-size 1
```

Không chạy `inline` mode trong staging/production trừ khi có quyết định incident tạm thời được ghi nhận.

## 10. Exit criteria

Queue được xem là đủ cho pilot khi:

- concurrency test pass;
- heartbeat/lease test pass;
- restart recovery pass;
- không có duplicate report;
- metrics đủ để phát hiện queue stuck;
- runbook có owner và escalation path.

## 11. Queue Invariant Acceptance Tests

| Invariant | Test assertion |
|---|---|
| One active job per submission | Concurrent analyze requests return one active job and one conflict/existing-job response |
| One worker owner per job | Two workers cannot both update the same job to running |
| Completed job has report | Any `COMPLETED` job without `report_id` fails invariant test |
| Retry creates lineage | Retried job has `retry_of_job_id`; original terminal job is immutable |
| Restart does not lose job | Process kill during stage leaves job recoverable by stale detector |
| Stale recovery avoids live jobs | Current heartbeat prevents requeue |
| Idempotent stage writes | Re-run does not duplicate citations, metadata records, reports or audit success events |

## 12. Claim and Lease Protocol Details

Claim must be atomic:

1. Select eligible queued/stale job with row lock and `SKIP LOCKED`.
2. Verify attempt count and active-job invariant.
3. Set `claimed_by`, `attempt_count`, `started_at`, `heartbeat_at` and first running stage.
4. Commit before doing extraction/provider/scoring work.
5. Update heartbeat during long stages.
6. Persist terminal state and close lease.

Worker must never hold a database transaction open while parsing documents or waiting
for provider/network calls.

## 13. Crash Recovery Test Plan

Required crash injection points:

- after claim before validation;
- after accepted file read before extraction result write;
- after citation delete before citation insert;
- after metadata provider call before metadata persist;
- after score calculation before report persist;
- after report persist before job terminal update.

Acceptance:

- recovery never produces duplicate terminal reports for one job;
- old partial writes are either idempotently reused or cleaned;
- job does not stay running forever after stale threshold;
- failed recovery includes stable error code and audit/log correlation ID.

## 14. Metrics and Alert Names

Recommended metric names:

```text
trustlens_queue_queued_total
trustlens_queue_running_total
trustlens_queue_oldest_queued_seconds
trustlens_job_duration_seconds
trustlens_job_failure_total{error_code}
trustlens_job_retry_total
trustlens_job_stale_recovery_total
trustlens_worker_heartbeat_age_seconds
trustlens_job_invariant_violation_total{invariant}
```

Minimum alerts:

- oldest queued age above threshold;
- no heartbeat from any worker;
- repeated stale recovery for same job;
- completed-without-report invariant violation;
- failure rate spike by error code;
- storage free space below threshold.
