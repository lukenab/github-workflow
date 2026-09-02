# Quy trình làm việc nhóm 

Repository này cung cấp các quy ước và template dùng chung để team quản lý công việc,
phát triển trên GitHub, review code, kiểm thử và thiết lập CI cho các dự án trong học kỳ.

## Mục tiêu

- Công việc rõ mục tiêu, người phụ trách và tiêu chí hoàn thành.
- Không push trực tiếp vào `main` hoặc `develop`; mọi thay đổi được review qua Pull Request.
- Phát hiện lỗi sớm bằng test và GitHub Actions khi project đã có source code.
- Các thay đổi đối với quy trình được review và lưu lịch sử qua Pull Request.

## Quy trình mặc định

```text
Issue → Ready → feature/fix/docs branch
      → Pull Request vào develop
      → Review + CI
      → Merge vào develop → Done

Khi chốt phiên bản demo/nộp bài:
develop → Pull Request vào main → CI → Merge
```

1. Tạo Issue bằng template phù hợp.
2. Chỉ bắt đầu khi Issue đạt Definition of Ready.
3. Tạo branch ngắn hạn từ `develop` theo Git workflow.
4. Mở Pull Request, tự kiểm tra diff và cung cấp cách xác minh.
5. Merge khi review đã approve, comment đã xử lý và CI thành công.
6. Đóng Issue khi đạt Definition of Done.

## Thiết lập mặc định cho team

| Nội dung | Quy ước |
|---|---|
| Quản lý công việc | GitHub Issues và GitHub Projects |
| Branch ổn định | `main` — phiên bản dùng để demo/nộp bài |
| Branch tích hợp | `develop` — nhận feature/fix/docs đã review |
| Branch công việc | `feature/*`, `fix/*`, `docs/*`, `test/*`, `chore/*` |
| Merge feature → develop | Squash merge |
| Merge develop → main | Merge commit qua Pull Request |
| Review | Ít nhất 1 approval |
| CI | Chạy trên Pull Request vào `develop` và `main` |

## Hướng dẫn chi tiết

Toàn bộ quy trình được trình bày trong [Quy trình phát triển và cộng tác](CONTRIBUTING.md), bao gồm:

- Quản lý Issue và Project Board.
- Definition of Ready và Definition of Done.
- Git workflow và commit convention.
- Pull Request và code review.
- Testing và Continuous Integration.

## Templates

- `.github/ISSUE_TEMPLATE/`: mẫu Markdown để tạo công việc hoặc báo lỗi.
- `.github/PULL_REQUEST_TEMPLATE.md`: checklist chung cho Pull Request.
- `workflow-templates/node-ci.yml`: CI mẫu cho Node.js, SDN302 và React Native/MMA301.

Repository hiện tên `github-workflow`, vì vậy workflow trong `workflow-templates/` là file mẫu.
Khi bắt đầu project, copy file phù hợp vào `.github/workflows/ci.yml` của repository project và
điều chỉnh Node version cùng các npm scripts.

## Phạm vi thông tin

Repository là public nên không lưu họ tên, MSSV, lịch cá nhân, thông tin liên hệ, link nhóm
private, credential, token hoặc dữ liệu nhạy cảm. Việc phân công cụ thể được quản lý bằng
GitHub Issues/Projects của từng project.
