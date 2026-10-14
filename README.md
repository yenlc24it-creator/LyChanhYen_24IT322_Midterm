# Lệnh `ls` của UNIX

Bài tập giữa kỳ môn Lập trình hệ thống: xây dựng chương trình `myls` bằng ngôn ngữ C, mô phỏng lệnh `ls(1)` của UNIX theo trang manual NetBSD 10.1 do giảng viên cung cấp. Dự án giúp tìm hiểu các thao tác trên hệ thống tập tin UNIX và lập trình ở mức hệ thống.

## Thông tin sinh viên

- **Sinh viên:** Lý Chánh Yên
- **MSSV:** 24IT322
- **Môn học:** Lập trình hệ thống
- **GitHub:** https://github.com/yenlc24it-creator/LyChanhYen_24IT322_Midterm

## Môi trường phát triển

- **Ngôn ngữ lập trình:** C
- **Hệ điều hành:** NetBSD 10.1 (amd64), chạy trong máy ảo VirtualBox và truy cập bằng SSH (xác nhận lại nếu bạn sử dụng đúng môi trường này).
- **Trình biên dịch:** `cc` (GCC 10.5.0) (xác nhận phiên bản thực tế).
- **Công cụ build:** `make` thông qua Makefile.
- **Quản lý mã nguồn:** Git và GitHub.

## Biên dịch và chạy

Tải dự án về máy:

```bash
git clone https://github.com/yenlc24it-creator/LyChanhYen_24IT322_Midterm.git
cd LyChanhYen_24IT322_Midterm
```

Biên dịch chương trình:

```bash
make clean
make
```

Chạy chương trình:

```bash
./myls
```

Một số lệnh sử dụng:

```bash
./myls -l
./myls -la
./myls -lh
./myls -R
./myls -lS
```

Xóa các file được tạo trong quá trình biên dịch:

```bash
make clean
```

Chương trình sử dụng tên `myls` để tránh trùng với lệnh `ls` có sẵn của hệ thống.

## Cấu trúc dự án

```text
.
├── Makefile
├── README.md
├── .gitignore
├── ls.h
├── main.c
├── options.c
├── options.h
├── entry.c
├── entry.h
├── sort.c
├── sort.h
├── print.c
├── print.h
├── format.c
├── format.h
├── longfmt.c
├── longfmt.h
├── util.c
├── util.h
└── tests/
```

Các module được phân chia theo chức năng để dễ quản lý, bảo trì và kiểm thử. Cấu trúc trên cần được đối chiếu với các file thực tế trong repository.

## Các tùy chọn được hỗ trợ

Chương trình hướng đến việc triển khai các tùy chọn theo manual `ls(1)` của NetBSD 10.1.

| Tùy chọn | Chức năng |
|---|---|
| `-A` | Liệt kê các mục ẩn, ngoại trừ `.` và `..`. |
| `-a` | Bao gồm các mục có tên bắt đầu bằng dấu chấm. |
| `-c` | Sử dụng thời gian thay đổi trạng thái file cho việc sắp xếp hoặc hiển thị phù hợp. |
| `-d` | Liệt kê thư mục như một mục thông thường, không duyệt nội dung. |
| `-F` | Thêm ký hiệu nhận biết loại file. |
| `-f` | Không sắp xếp danh sách. |
| `-h` | Hiển thị kích thước theo định dạng dễ đọc cho `-s` và `-l`. |
| `-i` | Hiển thị số inode. |
| `-k` | Hiển thị số block theo đơn vị kilobyte. |
| `-l` | Hiển thị thông tin file ở định dạng dài. |
| `-n` | Hiển thị UID và GID dạng số trong định dạng dài. |
| `-q` | Thay ký tự không in được trong tên file bằng `?`. |
| `-R` | Liệt kê đệ quy các thư mục con. |
| `-r` | Đảo ngược thứ tự sắp xếp. |
| `-S` | Sắp xếp theo kích thước file, lớn nhất trước. |
| `-s` | Hiển thị số block mà file sử dụng. |
| `-t` | Sắp xếp theo thời gian sửa đổi, mới nhất trước. |
| `-u` | Sử dụng thời gian truy cập thay cho thời gian sửa đổi khi phù hợp. |
| `-w` | Hiển thị nguyên dạng ký tự không in được trong tên file. |

Danh sách trên mô tả phạm vi tính năng mục tiêu. Khả năng hoạt động thực tế của từng tùy chọn cần được xác nhận bằng kiểm thử.

## Hành vi theo manual

- Khi không có operand, chương trình liệt kê nội dung thư mục hiện tại.
- Khi có nhiều operand, các mục không phải thư mục được xử lý trước các thư mục.
- Tùy chọn có thể được kết hợp, ví dụ `-la`, `-lh` và `-siF`.
- Một số tùy chọn ghi đè lẫn nhau theo thứ tự xuất hiện trên dòng lệnh.
- Định dạng dài có thể bao gồm quyền truy cập, số liên kết, chủ sở hữu, nhóm, kích thước, thời gian và tên file.
- Các tùy chọn về thời gian và kích thước ảnh hưởng đến cách sắp xếp hoặc hiển thị.
- Chương trình cần xử lý các lỗi như operand không tồn tại hoặc không thể truy cập thư mục.

## Thiết kế chương trình

- **`main.c`:** Điều khiển luồng thực thi chính và xử lý các operand.
- **`options.c` / `options.h`:** Phân tích các tùy chọn dòng lệnh.
- **`entry.c` / `entry.h`:** Đọc thông tin các mục trong thư mục.
- **`sort.c` / `sort.h`:** Thực hiện sắp xếp danh sách.
- **`print.c` / `print.h`:** Xuất thông tin ra màn hình.
- **`format.c` / `format.h`:** Định dạng kích thước và các thông tin liên quan.
- **`longfmt.c` / `longfmt.h`:** Xử lý định dạng thông tin file chi tiết.
- **`util.c` / `util.h`:** Cung cấp các hàm tiện ích dùng chung.

## Kiểm thử

Các trường hợp cần kiểm tra bao gồm:

1. Liệt kê thư mục hiện tại và thư mục được chỉ định.
2. Hiển thị file ẩn bằng `-a` và `-A`.
3. Hiển thị thông tin chi tiết bằng `-l`.
4. Hiển thị kích thước bằng `-h` và số block bằng `-s`.
5. Sắp xếp theo tên, thời gian và kích thước.
6. Liệt kê đệ quy bằng `-R`.
7. Xử lý symbolic link và các loại file khác.
8. Xử lý operand không tồn tại và các trường hợp lỗi.
9. Kiểm tra tổ hợp tùy chọn và thứ tự ghi đè.

Nếu repository có các script trong thư mục `tests/`, có thể sử dụng chúng để kiểm tra chương trình. Chỉ ghi kết quả kiểm thử là thành công sau khi đã chạy thực tế.

## Hạn chế

Các hạn chế sẽ được cập nhật sau khi hoàn thành kiểm thử, bao gồm những tùy chọn chưa hỗ trợ, khác biệt so với manual và các trường hợp chưa xử lý đầy đủ.

## Kết luận

Dự án tập trung vào việc vận dụng ngôn ngữ C để thao tác với hệ thống tập tin UNIX, xử lý thông tin file, phân tích tham số dòng lệnh và tổ chức chương trình thành nhiều module.

## Tài liệu tham khảo

- NetBSD 10.1, `ls(1)`, General Commands Manual, tài liệu được cung cấp trong bài tập.
- Các trang manual liên quan đến những hàm hệ thống được sử dụng trong chương trình.

## Repository

https://github.com/yenlc24it-creator/LyChanhYen_24IT322_Midterm
