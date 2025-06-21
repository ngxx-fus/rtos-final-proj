# Danh sách thành viên

| Họ và tên | MSSV | SĐT |
| --- | --- | --- |
| Nguyễn Thanh Phú | 22119211 | 0845939722 |
| Vũ Mai Liên | 22119194 | -- |
| Trần Xuân Mai | 22119196 | -- |
| Trần Thuỷ Tiên | 22119238 | -- |

# Giới thiệu U-boot

# **1. U-Boot Là Gì?**

**U-Boot**, tên đầy đủ là **Das U-Boot**, là một phần mềm bootloader mã nguồn mở dành cho các hệ thống nhúng. Đây là chương trình đầu tiên được thực thi sau khi thiết bị được cấp nguồn. Nhiệm vụ chính của U-Boot là khởi tạo phần cứng cơ bản như RAM, CPU, thiết bị I/O, sau đó nạp và khởi động hệ điều hành – thường là Linux.

U-Boot được sử dụng rộng rãi trên các thiết bị nhúng sử dụng kiến trúc ARM, MIPS, PowerPC... bao gồm cả Raspberry Pi, BeagleBone, các board phát triển và thiết bị IoT.

# **2. Vai trò của U-Boot trong hệ thống**

U-Boot giữ vai trò vô cùng quan trọng trong quy trình khởi động của một hệ thống nhúng. Nó là "cầu nối" giữa phần cứng và hệ điều hành. Sau khi bộ xử lý (SoC) thực hiện chương trình khởi động sơ cấp nằm trong ROM, U-Boot sẽ tiếp quản, thực hiện các bước khởi tạo phần cứng cần thiết và nạp kernel của hệ điều hành vào bộ nhớ.

Ngoài ra, U-Boot cũng có thể nạp các thành phần quan trọng khác như Device Tree (DTB), initramfs hoặc các module kernel tùy theo nhu cầu cấu hình của hệ thống.