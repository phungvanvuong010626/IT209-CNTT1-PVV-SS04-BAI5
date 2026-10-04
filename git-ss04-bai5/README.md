## 1. Bối cảnh & Yêu cầu đề bài

- **Bối cảnh:** Lập trình viên đã tạo ra một commit chứa mã nguồn bị lỗi ở môi trường local. Cần thực hành hai kỹ thuật khác nhau để xử lý lỗi này:
  - **Trường hợp 1 (Sửa đổi cục bộ):** Di chuyển lịch sử cục bộ lùi lại 1 commit và giữ lại các tệp tin thay đổi ở dạng chưa chuẩn bị (Working Directory / Unstaged) để viết lại code.
  - **Trường hợp 2 (Sửa đổi công cộng):** Đảo ngược thay đổi của một commit cũ bằng cách tạo thêm một commit mới đối lập (Revert commit) để đẩy lên remote an toàn.
- **Ràng buộc:**
  - *Trường hợp 1:* Không được làm mất mã nguồn hiện hành (không dùng reset hard, chỉ dùng reset mixed mặc định).
  - *Trường hợp 2:* Bắt buộc tạo ra một commit mới để ghi lại lịch sử đảo ngược rõ ràng.
- **Kết quả mong đợi:**
  - *Trường hợp 1:* Con trỏ `HEAD` lùi lại, thay đổi nằm trong Working Directory dưới dạng `Modified` (`Changes not staged for commit`).
  - *Trường hợp 2:* Có một commit mới có tiêu đề bắt đầu bằng `Revert ...` xuất hiện trên đầu nhánh.

---

## 2. Các bước thực hiện chi tiết

### 2.1. Khởi tạo kho chứa và Commit nền tảng
```powershell
# Tạo thư mục và khởi tạo Git repository
mkdir git-ss04-bai5
cd git-ss04-bai5
git init

# Tạo file app.py ban đầu và commit
echo "print('Phien ban khoi dau')" > app.py
git add app.py
git commit -m "test bai 5"
```

---

### 2.2. Trường hợp 1: Sửa đổi cục bộ bằng `git reset`

#### A. Tạo commit lỗi ở local:
```powershell
echo "print('Loi cu phap o day')" >> app.py
git add app.py
git commit -m "Feat: Commit loi can sua"
```

#### B. Khôi phục với `git reset HEAD~1`:
Lệnh `git reset HEAD~1` (chế độ `--mixed` mặc định) di chuyển con trỏ `HEAD` lùi lại 1 commit, đưa các tệp thay đổi về trạng thái chưa chuẩn bị (Unstaged trong Working Directory) mà không làm mất mã nguồn.

```powershell
git reset HEAD~1
```

#### C. Viết lại code chuẩn và commit:
```powershell
# Sửa lại nội dung code chính xác
echo "print('Code chuan sau khi fix')" > app.py

# Stage và commit lại
git add app.py
git commit -m "Fix: Sua lai code chuan"
```

---

### 2.3. Trường hợp 2: Đảo ngược công cộng an toàn bằng `git revert`

#### A. Phát sinh commit lỗi và commit tài liệu trên nhánh:
```powershell
# Tạo commit tính năng bị lỗi
echo "print('Tinh nang loi tren nhanh chung')" >> app.py
git add app.py
git commit -m "Feat: Tinh nang bi loi can revert"

# Tạo commit cập nhật tài liệu tiếp nối
echo "# Chu thich du an" >> README.md
git add README.md
git commit -m "Docs: Cap nhat tai lieu"
```

#### B. Đảo ngược commit lỗi với `git revert`:
Sử dụng `git revert <commit-hash>` để tự động tạo một commit mới đối lập, triệt tiêu thay đổi của commit lỗi mà không làm thay đổi lịch sử của các commit xung quanh.

```powershell
# Đảo ngược commit lỗi (hash: 3f292cb)
git revert --no-edit 3f292cb
```

---

## 3. Nhật ký thực thi & Minh chứng thực tế (Verification & Logs)

### 3.1. Khởi tạo Repository và Commit đầu tiên
```powershell
PS D:\DevOps - RA\Session04> mkdir git-ss04-bai5


    Directory: D:\DevOps - RA\Session04


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/2/2026  10:27 AM                git-ss04-bai5


PS D:\DevOps - RA\Session04> cd git-ss04-bai5
PS D:\DevOps - RA\Session04\git-ss04-bai5> git init
Initialized empty Git repository in D:/DevOps - RA/Session04/git-ss04-bai5/.git/
PS D:\DevOps - RA\Session04\git-ss04-bai5> echo "print('Phien ban khoi dau')" > app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git add app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git commit -m "test bai 5"
[master (root-commit) ffe8187] test bai 5
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git log --oneline
ffe8187 (HEAD -> master) test bai 5
```

---

### 3.2. Minh chứng Trường hợp 1: Git Reset (Sửa đổi cục bộ)

#### 1. Commit lỗi phát sinh:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> echo "print('Loi cu phap o day')" >> app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git add app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git commit -m "Feat: Commit loi can sua"
[master 8694055] Feat: Commit loi can sua
 1 file changed, 0 insertions(+), 0 deletions(-)
```

#### 2. Thực hiện `git reset HEAD~1` và kiểm tra trạng thái:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> git reset HEAD~1
Unstaged changes after reset:
M       app.py

PS D:\DevOps - RA\Session04\git-ss04-bai5> git log --oneline -n 5
ffe8187 (HEAD -> master) test bai 5

PS D:\DevOps - RA\Session04\git-ss04-bai5> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   app.py

no changes added to commit (use "git add" and/or "git commit -a")
```
> **Nhận xét kết quả:**
> - Con trỏ `HEAD` đã lùi lại 1 commit về `ffe8187`. Commit lỗi `8694055` đã được bỏ khỏi lịch sử.
> - Thay đổi của commit lỗi vẫn được giữ nguyên vẹn trong Working Directory ở trạng thái `modified` (`Changes not staged for commit`), đúng ràng buộc không làm mất mã nguồn (không dùng reset hard).

#### 3. Viết lại code chuẩn và commit mới:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> echo "print('Code chuan sau khi fix')" > app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git add app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git commit -m "Fix: Sua lai code chuan"
[master 5d5cc94] Fix: Sua lai code chuan
 1 file changed, 0 insertions(+), 0 deletions(-)
```

---

### 3.3. Minh chứng Trường hợp 2: Git Revert (Sửa đổi công cộng)

#### 1. Tạo commit tính năng lỗi và commit tài liệu tiếp theo:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> echo "print('Tinh nang loi tren nhanh chung')" >> app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git add app.py
PS D:\DevOps - RA\Session04\git-ss04-bai5> git commit -m "Feat: Tinh nang bi loi can revert"
[master 3f292cb] Feat: Tinh nang bi loi can revert
 1 file changed, 0 insertions(+), 0 deletions(-)

PS D:\DevOps - RA\Session04\git-ss04-bai5> echo "# Chu thich du an" >> README.md
PS D:\DevOps - RA\Session04\git-ss04-bai5> git add README.md
PS D:\DevOps - RA\Session04\git-ss04-bai5> git commit -m "Docs: Cap nhat tai lieu"
[master cf0590e] Docs: Cap nhat tai lieu
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md
```

#### 2. Lịch sử commit trước khi Revert:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> git log --oneline -n 5
cf0590e (HEAD -> master) Docs: Cap nhat tai lieu
3f292cb Feat: Tinh nang bi loi can revert
5d5cc94 Fix: Sua lai code chuan
ffe8187 test bai 5
```

#### 3. Thực hiện Revert commit `3f292cb`:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> git revert --no-edit 3f292cb
[master 5f5bb67] Revert "Feat: Tinh nang bi loi can revert"
 Date: Fri Oct 2 10:34:56 2026 +0700
 1 file changed, 0 insertions(+), 0 deletions(-)
```

#### 4. Kiểm tra lịch sử log và nội dung file sau khi Revert:
```powershell
PS D:\DevOps - RA\Session04\git-ss04-bai5> git log --oneline -n 5
5f5bb67 (HEAD -> master) Revert "Feat: Tinh nang bi loi can revert"
cf0590e Docs: Cap nhat tai lieu
3f292cb Feat: Tinh nang bi loi can revert
5d5cc94 Fix: Sua lai code chuan
ffe8187 test bai 5

PS D:\DevOps - RA\Session04\git-ss04-bai5> Get-Content app.py
print('Code chuan sau khi fix')
```
> **Nhận xét kết quả:**
> - Một commit mới `5f5bb67` có tiền tố `Revert "Feat: Tinh nang bi loi can revert"` xuất hiện trên đầu nhánh (`HEAD`).
> - Toàn bộ lịch sử các commit trước đó (`cf0590e`, `3f292cb`) vẫn được bảo toàn nguyên vẹn.
> - Nội dung tệp `app.py` đã tự động đảo ngược loại bỏ phần code lỗi, trở về trạng thái code chuẩn `print('Code chuan sau khi fix')`.

---

## 4. Phân biệt sự khác nhau giữa `git reset` và `git revert`

### 4.1. Bảng so sánh chi tiết

| Tiêu chí | `git reset` (Khôi phục cục bộ) | `git revert` (Đảo ngược công cộng) |
|---|---|---|
| **Cơ chế hoạt động** | Di chuyển con trỏ `HEAD` và branch về quá khứ, loại bỏ các commit sau mốc đó khỏi lịch sử nhánh hiện tại. | Tạo thêm một **commit mới** có nội dung đối lập để triệt tiêu thay đổi của commit chỉ định. |
| **Bản chất lịch sử** | **Viết lại lịch sử (Rewrite History)**, làm thay đổi SHA-1 hash và cấu trúc cây commit. | **Bảo toàn lịch sử (Preserve History)**, lịch sử commit phát triển tiến về phía trước (Forward-moving). |
| **Khi làm việc cá nhân (Local)** | **Rất phù hợp và tiện lợi** khi cần dọn dẹp commit nháp, sửa lỗi code trước khi đẩy (push) lên remote. | Có thể dùng nhưng thường tạo thêm nhiều commit revert không cần thiết trên nhánh cá nhân. |
| **Khi làm việc nhóm (Remote / Shared Branch)** | **Cực kỳ nguy hiểm**, gây phân nhánh xung đột (diverged history) cho các thành viên khác nếu đã push lên remote. | **Chuẩn mực và an toàn tuyệt đối**, mọi thành viên chỉ cần `git pull` bình thường mà không bị xung đột lịch sử. |
| **Thao tác đồng bộ remote** | Bắt buộc phải dùng `git push --force` (hoặc `--force-with-lease`) nếu commit cũ đã từng push. | Chỉ cần `git push` thông thường (Fast-forward push). |
| **Rủi ro mất dữ liệu** | Rủi ro cao nếu dùng nhầm `--hard`. An toàn khi dùng `--mixed` hoặc `--soft`. | Rất thấp, không làm mất bất kỳ commit nào trong lịch sử. |

### 4.2. Sơ đồ luồng hoạt động

- **`git reset` (Local):**
  ```
  Trước: (A) -> (B) -> (C_lỗi) [HEAD]
  Sau:   (A) -> (B) [HEAD]  ==> (Nội dung C_lỗi được giữ lại ở Working Directory để viết lại code)
  ```

- **`git revert` (Shared/Remote):**
  ```
  Trước: (A) -> (B_chuẩn) -> (C_lỗi) -> (D_docs) [HEAD]
  Sau:   (A) -> (B_chuẩn) -> (C_lỗi) -> (D_docs) -> (C'_Revert) [HEAD]  ==> (Lịch sử tuyến tính, không bị viết lại)
  ```
