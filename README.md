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
Issue → Ready → working branch
      → Pull Request vào develop (default)
      → Review + CI → Testing
      → Squash merge → Done

Khi chốt phiên bản demo/nộp bài:
develop → Pull Request vào main → CI → Merge commit
```

`working branch` là branch công việc được tạo từ `develop`, bao gồm `feature/*`,
`fix/*`, `docs/*`, `test/*`, `chore/*` và `refactor/*`.

### Bắt đầu công việc

- Tạo công việc hoặc báo lỗi bằng [Issue template](.github/ISSUE_TEMPLATE/issue.md).
- Chỉ nhận Issue đã đạt [Definition of Ready](CONTRIBUTING.md#definition-of-ready).
- Tạo branch công việc từ `develop` theo [Git workflow](CONTRIBUTING.md#git-workflow).
- Mở Pull Request vào `develop` và tuân theo
  [quy trình review](CONTRIBUTING.md#pull-request-và-code-review).
- Issue hoàn thành khi đạt [Definition of Done](CONTRIBUTING.md#definition-of-done).

## Thiết lập mặc định cho team

| Nội dung | Quy ước |
|---|---|
| Quản lý công việc | GitHub Issues và GitHub Projects |
| Default branch | `develop` — nhận và tích hợp công việc đã review |
| Stable branch | `main` — phiên bản dùng để demo/nộp bài |
| Branch công việc | Tạo từ `develop`: `feature/*`, `fix/*`, `docs/*`, `test/*`, `chore/*`, `refactor/*` |
| Target mặc định | Branch công việc → `develop` |
| Merge vào `develop` | Squash merge |
| Merge vào `main` | Merge commit từ `develop` qua Pull Request |
| Review | Ít nhất 1 approval |
| CI | Chạy trên Pull Request vào `develop` và `main` |

GitHub được cấu hình với `develop` làm default branch nên Pull Request từ branch công việc sẽ
mặc định target vào `develop`. Khi chốt phiên bản ổn định, chọn `main` làm target và chỉ sử dụng
`develop` làm source branch.

## Hướng dẫn chi tiết

Toàn bộ quy trình được trình bày trong [Development Workflow](CONTRIBUTING.md), bao gồm:

- Quản lý Issue và Project Board.
- Definition of Ready và Definition of Done.
- Git workflow và commit convention.
- Pull Request và code review.
- Testing và Continuous Integration.

## Templates

- `.github/ISSUE_TEMPLATE/`: mẫu Markdown để tạo công việc hoặc báo lỗi.
- `.github/PULL_REQUEST_TEMPLATE.md`: checklist cho Pull Request.
- `workflow-templates/node-ci.yml`: CI mẫu (sẽ bổ sung chi tiết khi có đề cương và thông tin môn học cụ thể)

Repository hiện tên `github-workflow`, vì vậy workflow trong `workflow-templates/` là file mẫu.
Khi bắt đầu project, copy file phù hợp vào `.github/workflows/ci.yml` của repository project và
điều chỉnh Node version cùng các npm scripts.
