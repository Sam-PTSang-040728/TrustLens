# 14. Hướng dẫn Đóng góp

## 1. Submodule workflow

Root chứa frontend/backend submodule. Thay đổi repository con phải commit/push trước, sau đó cập nhật pointer ở root.

```bash
git submodule update --init --recursive
```

Không commit root pointer trỏ tới commit con chưa được push.

## 2. Branch

```text
main
develop
feature/<scope>
fix/<scope>
docs/<scope>
```

Một pull request nên có một mục tiêu.

## 3. Commit

```text
feat(api): unify submission analysis pipeline
fix(auth): reject default secret in production
test(scoring): add C1-C7 boundary vectors
docs(api): align endpoint inventory with router
```

## 4. Pull request checklist

- [ ] Scope rõ.
- [ ] Không lẫn refactor không liên quan.
- [ ] Unit/integration test.
- [ ] Negative authorization test.
- [ ] Migration nếu đổi model.
- [ ] OpenAPI/docs.
- [ ] Loading/error UI.
- [ ] Không mock fallback production.
- [ ] Không secret.
- [ ] Scoring version review.
- [ ] Rollback/compatibility.

## 5. API rules

- `/api/v1`.
- Typed response.
- Error schema chuẩn.
- Ownership ở backend.
- Không expose physical path.
- Pagination cho list lớn.
- Internal endpoint có permission riêng.
- Breaking change phải version.

## 6. Service rules

Router chỉ parse input, access control, gọi service và map response. Không nhồi business logic vào endpoint.

## 7. Pipeline rules

- Một orchestration implementation.
- Stage idempotent hoặc cleanup rõ.
- Persist state quanh external call.
- Provider lỗi không tạo kết luận tuyệt đối.
- Error code ổn định.
- Retry giữ lineage.
- Completed report chỉ khi dữ liệu đầy đủ.

## 8. Scoring change checklist

1. Rationale.
2. Version bump.
3. Test vectors.
4. Boundary tests.
5. Benchmark impact.
6. Frontend contract.
7. Migration/compatibility.
8. Changelog.

Không hard-code weight ở nhiều nơi mà thiếu consistency test.

## 9. Frontend rules

- API client theo domain.
- Loading/error/empty state.
- Route guard không thay backend auth.
- Mock chỉ explicit.
- Redact sensitive log.
- Không diễn giải `NOT_FOUND` là fake.
- Report hiển thị confidence và limitation.

## 10. Database rules

- Alembic migration.
- Không sửa migration đã phát hành nếu có thể tạo migration mới.
- FK/index/unique rõ.
- Soft-delete filter.
- UTC timestamps.
- Không lưu password/token rõ.
- Raw JSON có retention/size policy.

## 11. Documentation rules

- Nêu mục đích.
- Phân biệt as-is/to-be.
- Dẫn path mã nguồn.
- Không ghi “hoàn thành” thiếu bằng chứng.
- Giữ ID ổn định.
- Không biến kế hoạch thành hiện trạng.
- Cập nhật snapshot/changelog.

## 12. Reviewer tối thiểu

| Loại | Reviewer |
|---|---|
| Auth/security | Backend + security-minded reviewer |
| Scoring/NLP | BA/NLP + backend |
| API | Backend + frontend |
| Data model | Backend + migration reviewer |
| UI report | Frontend + BA/NLP |
| Provider | Backend + privacy |
| Deployment | Backend/DevOps |
| Docs | Domain owner + technical reviewer |

## 13. Không merge khi

Test fail, migration không chạy, có secret, mock che lỗi, ownership chưa test, score đổi không version, API đổi không cập nhật client/docs hoặc phát sinh critical gap chưa có quyết định.
