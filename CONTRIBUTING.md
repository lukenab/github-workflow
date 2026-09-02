# Development Workflow
Tài liệu này mô tả quy trình thống nhất để nhóm quản lý công việc, phát triển mã nguồn, thực hiện code review, kiểm thử và tích hợp liên tục.

## Mục lục

- [Quản lý Issue và Project](#quản-lý-issue-và-project)
- [Definition of Ready](#definition-of-ready)
- [Git workflow](#git-workflow)
- [Commit convention](#commit-convention)
- [Pull Request và code review](#pull-request-và-code-review)
- [Testing](#testing)
- [Continuous Integration](#continuous-integration)
- [Definition of Done](#definition-of-done)

## Quản lý Issue và Project

GitHub Issues lưu yêu cầu và phạm vi công việc. GitHub Projects dùng để lập kế hoạch, phân công
và theo dõi trạng thái. Không tạo cùng một task ở nhiều công cụ nếu không có lý do rõ ràng.

### Loại Issue

- `feature`: tính năng hoặc giá trị mới.
- `bug`: hành vi thực tế khác kết quả mong đợi.
- `task`: refactor, test, cấu hình, hạ tầng hoặc maintenance.
- `docs`: tài liệu có kết quả bàn giao độc lập.

### Project Board đề xuất

```text
Backlog → Ready → In progress → In review → Testing → Done
```

| Field | Giá trị gợi ý | Mục đích |
|---|---|---|
| Status | Các trạng thái ở trên | Theo dõi luồng công việc |
| Priority | P0, P1, P2, P3 | Thứ tự xử lý |
| Estimate | 1, 2, 3, 5, 8 hoặc S, M, L | Ước lượng tương đối |
| Area | API, Mobile, Design, Docs, DevOps... | Lọc theo phạm vi |
| Iteration | Theo lịch project | Lập kế hoạch theo sprint/tuần |

Labels đề xuất: `type:feature`, `type:bug`, `type:task`, `type:docs`, các label `area:*`,
`blocked` và `security`. Không dùng label cho Status và Estimate nếu đã có Project fields.
Priority là thứ tự team chọn làm; Severity là mức ảnh hưởng của bug.

### Quy tắc sử dụng

- Mỗi thay đổi cần có Issue, trừ chỉnh sửa rất nhỏ được reviewer đồng ý.
- Một Issue có một người chịu trách nhiệm chính khi ở `In progress`.
- Khi bắt đầu, assign Issue cho chính mình và chuyển sang `In progress`.
- Mỗi người giới hạn số task đang làm để ưu tiên hoàn thành trước khi nhận thêm.
- Blocker phải được ghi hoặc liên kết trên Issue, không chỉ báo trong chat.
- Pull Request dùng `Closes #<id>` để tự đóng Issue sau khi merge.

## Definition of Ready

Definition of Ready (DoR) giúp team không bắt đầu một task còn thiếu thông tin quan trọng.
Một Issue được chuyển sang `Ready` khi các mục áp dụng đều đạt:

- [ ] Mục tiêu và giá trị mong đợi được mô tả rõ.
- [ ] Acceptance criteria cụ thể và có thể xác minh.
- [ ] Phạm vi đủ nhỏ để hoàn thành và review trong thời gian dự kiến.
- [ ] Dependency, blocker và Issue liên quan đã được liên kết.
- [ ] Không còn câu hỏi có thể làm thay đổi đáng kể scope, kiến trúc, bảo mật hoặc estimate.
- [ ] Đã nêu cách kiểm thử hoặc demo kết quả.
- [ ] API contract, UI reference hoặc data model đã có nếu cần làm song song.
- [ ] Priority, estimate và người phụ trách đã được xác định.

Dùng sub-issues khi cần chia phần việc cho nhiều người hoặc module. Tách Issue nếu có nhiều
acceptance criteria độc lập hoặc thay đổi không thể review trong một Pull Request. Không trì hoãn
task chỉ vì câu hỏi nhỏ không ảnh hưởng đến phạm vi hoặc giải pháp chính.

## Git workflow

Team dùng GitFlow rút gọn:

- `main`: phiên bản ổn định dùng để demo hoặc nộp bài.
- `develop`: branch tích hợp cho công việc đang phát triển.
- Branch công việc: tạo từ `develop` và merge trở lại `develop`.

```text
main       ●──────────────────────●  demo/nộp bài
            \                    /
develop     ●──────●──────●─────●
                  /      /
feature/*   ─────●      /
fix/*             ─────●
```

### Luồng làm việc hằng ngày

1. Chọn một Issue ở trạng thái `Ready`.
2. Cập nhật `develop` local từ remote.
3. Tạo branch công việc từ `develop`.
4. Commit các thay đổi nhỏ, có liên quan.
5. Push branch; có thể mở Draft Pull Request sớm để nhận feedback.
6. Mở Pull Request vào `develop`, hoàn thiện test và yêu cầu review.
7. Khi CI thành công và đủ approval, squash merge vào `develop`.
8. Xóa branch và chuyển Issue sang `Done` khi đạt Definition of Done.

```bash
git switch develop
git pull --ff-only origin develop
git switch -c feature/12-login-api
```

### Đặt tên branch

Định dạng: `<type>/<issue-id>-<short-kebab-case-description>`.

```text
feature/12-login-api
fix/24-token-expiration
docs/31-update-api-guide
test/42-add-auth-tests
chore/55-configure-ci
refactor/63-extract-user-service
```

- Không dùng khoảng trắng, tiếng Việt có dấu hoặc ký tự `#`.
- Một branch phục vụ một mục tiêu có thể review độc lập.
- Đồng bộ `develop` sớm nếu có nguy cơ conflict; tránh để branch kéo dài cả sprint.
- `fix/*` dùng cho bug trong quá trình phát triển.

### Đưa `develop` vào `main`

Thực hiện khi cần demo, nộp bài hoặc team chốt một phiên bản ổn định:

1. Đảm bảo CI trên `develop` đang xanh và không còn Issue đang block phiên bản.
2. Mở Pull Request từ `develop` vào `main`.
3. Chạy lại CI và kiểm tra phạm vi thay đổi.
4. Dùng merge commit, không squash, để giữ quan hệ lịch sử giữa hai branch.
5. Không chỉnh code trực tiếp trên `main`; thay đổi tiếp theo tiếp tục từ `develop`.

### Branch protection

Cấu hình ruleset cho cả `develop` và `main`:

- Không push hoặc force-push trực tiếp.
- Bắt buộc Pull Request, ít nhất một approval và xử lý review conversations.
- Bắt buộc CI checks khi workflow đã ổn định.
- Branch công việc vào `develop` dùng squash merge và được xóa sau khi merge.
- Pull Request từ `develop` vào `main` dùng merge commit.

## Commit convention

Dùng định dạng `<type>(<scope>): <description>` theo Conventional Commits:

```text
feat(auth): add login endpoint
fix(mobile): handle expired session
test(api): cover invalid token cases
docs(swd): update sequence diagram
ci: add pull request checks
```

Các type thường dùng: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `build`, `ci`, `chore`.
Scope nên là module hoặc domain ổn định như `auth`, `booking`, `mobile`, `api`; có thể bỏ scope
khi thay đổi áp dụng toàn repository.

- Mỗi commit tập trung vào một thay đổi có liên quan.
- Không commit secret, `.env`, credential, dữ liệu cá nhân hoặc file build không cần thiết.
- Cập nhật test và tài liệu khi hành vi hệ thống thay đổi.

## Pull Request và code review

- Branch công việc mở Pull Request vào `develop`.
- `main` chỉ nhận Pull Request từ `develop` khi team chốt phiên bản demo/nộp bài.
- Không push trực tiếp vào `develop` hoặc `main`.
- Liên kết Issue bằng `Closes #<id>` khi merge sẽ hoàn thành Issue.
- Ghi rõ thay đổi, lý do, cách kiểm thử và rủi ro.
- Screenshot dùng cho kết quả trực quan, không thay thế test.

### Trách nhiệm của tác giả

- Giữ Pull Request nhỏ và có một mục tiêu rõ.
- Tự review toàn bộ tab `Files changed` trước khi gửi.
- Báo rõ breaking change, migration, phần chưa test hoặc giới hạn hiện tại.
- Trả lời review bằng context hoặc thay đổi cụ thể trước khi resolve comment.

### Trách nhiệm của reviewer

Kiểm tra theo thứ tự ưu tiên:

1. Acceptance criteria và tính đúng của luồng chính.
2. Security, quyền truy cập và tính toàn vẹn dữ liệu.
3. Error handling, edge cases và concurrency khi liên quan.
4. Test và khả năng xác minh lỗi.
5. Kiến trúc, khả năng bảo trì và tương thích.
6. Naming/style mà lint chưa kiểm tra được.

Phân biệt mức độ comment:

- `blocking:` phải xử lý trước khi merge.
- `suggestion:` đề xuất cải thiện nhưng không chặn merge.
- `question:` cần thêm context để đánh giá.
- `nit:` chi tiết nhỏ; nếu lặp lại nên đưa vào formatter/linter.

Không yêu cầu refactor ngoài phạm vi nếu không ảnh hưởng correctness hoặc security; tạo Issue
theo dõi riêng. Không approve khi chưa hiểu phần có rủi ro chính.

### Điều kiện merge

- Có ít nhất một approval từ reviewer ngoài tác giả.
- CI thành công nếu project đã cấu hình workflow.
- Blocking comments đã được xử lý.
- Sau thay đổi lớn, reviewer kiểm tra lại phần diff mới.
- Branch công việc vào `develop` dùng squash merge.

Không bỏ qua CI hoặc review chỉ để kịp deadline. Trường hợp khẩn cấp phải ghi lý do và tạo Issue
xử lý tiếp.

## Testing

Team chọn test theo rủi ro và hành vi cần bảo vệ, không chạy theo coverage percentage đơn thuần.

### Mức kiểm thử

- **Unit test:** business rule, validation và logic có nhiều nhánh.
- **Integration test:** database, external API, messaging, filesystem hoặc framework integration quan trọng.
- **Contract/API test:** request/response schema khi frontend, mobile hoặc nhiều service cùng sử dụng.
- **UI/E2E test:** các luồng người dùng quan trọng, chọn lọc để tránh pipeline quá chậm.
- **Manual test:** dùng khi chưa thể tự động hóa; ghi steps, data và expected result.

Ưu tiên bằng chứng có thể chạy lại như CI report hoặc command, sau đó là log/API response đã loại
dữ liệu nhạy cảm. Screenshot hoặc recording phù hợp với giao diện và hành vi trực quan nhưng không
thay thế integration test cho business rule.

### Test data

- Không dùng dữ liệu thật hoặc dữ liệu cá nhân trong test.
- Không commit credential có thể sử dụng được.
- Test cần độc lập, ổn định và cleanup dữ liệu khi áp dụng.
- Flaky test phải được sửa hoặc tạo Issue theo dõi; không tắt test mà không ghi lý do.

### Áp dụng cho các môn

- **SDN302:** ưu tiên unit/integration test cho API, validation, authentication và database.
- **MMA301:** test logic/state quan trọng; manual test UI trên thiết bị/emulator và ghi kết quả.
- **SWD392:** kiểm tra tính nhất quán giữa requirement, UML, architecture, database và implementation.

## Continuous Integration

CI tự động kiểm tra thay đổi trước khi merge, giúp phát hiện sớm lỗi cài dependency, lint, test
hoặc build trên một môi trường thống nhất.

Khi bắt đầu project, copy `workflow-templates/node-ci.yml` vào `.github/workflows/ci.yml` và kiểm tra:

- Node version khớp với môi trường team sử dụng.
- Repository có `package-lock.json` để chạy `npm ci`.
- `package.json` khai báo các script `lint`, `test`, `build` phù hợp.
- Workflow chạy khi push hoặc mở Pull Request vào `develop` và `main`.

```text
checkout → npm ci → lint → test → build
```

Script chưa tồn tại được bỏ qua bằng `--if-present`. Khi project đã ổn định, bổ sung các script
cần thiết và chọn CI checks bắt buộc trước khi merge.

### Quy tắc CI

- Command trên CI cần chạy được tương tự ở local.
- Không bỏ qua workflow đỏ; sửa lỗi hoặc revert để branch trở lại trạng thái ổn định.
- CI cần đủ nhanh để chạy trên mỗi Pull Request.
- Test chậm hoặc chưa ổn định không được âm thầm tắt; tạo Issue để xử lý.
- Khi workflow đáng tin cậy, cấu hình required status checks cho `develop` và `main`.
- Đặt `permissions: contents: read` làm mặc định.
- Không ghi hoặc log secret, token, credential và dữ liệu nhạy cảm.
- Chỉ dùng third-party Actions đã được team kiểm tra.

## Definition of Done

Một Issue là `Done` khi kết quả có thể chạy, review và bàn giao; không chỉ là đã viết xong code.

### Yêu cầu và kết quả

- [ ] Tất cả acceptance criteria đã đạt.
- [ ] Không còn lỗi nghiêm trọng chưa được ghi nhận trong phạm vi thay đổi.
- [ ] Kết quả có thể demo hoặc xác minh bằng các bước đã mô tả.

### Code và review

- [ ] Pull Request liên kết đúng Issue và chỉ tập trung vào một mục tiêu.
- [ ] Tác giả đã tự review diff.
- [ ] Có ít nhất một approval và các comment đang block đã được xử lý.
- [ ] Không còn debug code, dead code hoặc commented-out code không có lý do.
- [ ] Không chứa secret, credential hoặc dữ liệu nhạy cảm.

### Kiểm thử

- [ ] CI checks thành công khi project đã cấu hình CI.
- [ ] Test phù hợp đã được thêm/cập nhật và chạy thành công.
- [ ] Manual test được ghi lại khi chưa thể tự động hóa.
- [ ] Build/package thành công nếu project tạo artifact.

### Tài liệu và cấu hình

- [ ] README, API contract, diagram hoặc hướng dẫn chạy được cập nhật khi cần.
- [ ] Thay đổi database có migration và cập nhật data model khi áp dụng.
- [ ] Cấu hình môi trường không bị hardcode vào source code.

Project có thể dùng trạng thái `Accepted` sau khi demo cho giảng viên hoặc stakeholder. Việc chờ
buổi demo không cần giữ một Pull Request đã đạt toàn bộ quality checks ở trạng thái mở.
