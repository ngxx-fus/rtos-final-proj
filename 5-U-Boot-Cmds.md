# Các lệnh của U-Boot

# **1. Lệnh Quản Lý Biến Môi Trường**

Các lệnh liên quan đến biến môi trường rất quan trọng trong việc định nghĩa cách hệ thống khởi động cũng như các tham số khác:

- printenv Hiển thị toàn bộ các biến môi trường hiện có. Thông tin này bao gồm các thông số cấu hình boot như bootcmd, bootargs,…
- setenv Đặt hoặc thay đổi giá trị của một biến môi trường.
- saveenv Lưu lại các thay đổi của biến môi trường vào bộ nhớ không thoáng (thường là flash), giúp khi khởi động lại hệ thống vẫn dùng các thiết lập đã được thay đổi.
- env delete **/** env default Cho phép xóa một biến hoặc thiết lập lại toàn bộ biến môi trường về mặc định.

# **2. Lệnh Quản Lý Bộ Nhớ**

Trong môi trường bootloader, truy cập và thao tác với bộ nhớ là một tác vụ thường xuyên để kiểm tra, gỡ lỗi hoặc chuẩn bị khu vực chứa hình ảnh hệ điều hành.

- md **(memory display):** Hiển thị nội dung bộ nhớ từ một địa chỉ nào đó.
- mw **(memory write):** Ghi giá trị vào bộ nhớ tại địa chỉ chỉ định.
- mm **(memory modify):** Cho phép thay đổi nội dung bộ nhớ tương tác qua từng giá trị.
- cp **(copy):** Sao chép dữ liệu giữa các vùng nhớ.
- crc32**:** Tính toán hàm băm CRC32 cho một vùng dữ liệu, hỗ trợ kiểm tra tính toàn vẹn của dữ liệu đã load vào bộ nhớ.

# **3. Lệnh Quản Lý Hệ Thống Tập Tin và Thiết Bị Lưu Trữ**

U‑Boot cho phép truy cập các thiết bị lưu trữ để tải hình ảnh hệ điều hành hoặc các tệp cấu hình khác:

- fatload**:** Tải file từ hệ thống tập tin FAT (thường dùng trên thẻ SD hoặc USB dạng FAT32).
- ext4load**:** Tương tự như fatload nhưng dành cho hệ thống tập tin EXT (EXT2/3/4).
- mmc **commands:** Bộ lệnh liên quan đến các thiết bị MMC/SD bao gồm:
    - mmc list: Liệt kê các thiết bị MMC.
    - mmc info: Hiển thị thông tin chi tiết của một MMC.
    - mmc read / mmc write: Đọc/ghi dữ liệu từ/đến MMC.
- usb start**:** Khởi động hệ thống USB, cho phép nhận dạng và truy cập các thiết bị USB, như ổ flash hay bộ nhớ ngoài.

# **4. Lệnh Khởi Động Hệ Thống (Boot Commands)**

Quá trình khởi động hệ điều hành từ U‑Boot dựa vào một số lệnh cốt lõi giúp chuyển giao điều khiển cho kernel:

- bootm**:** Lệnh dùng để khởi động từ một image đã được load vào bộ nhớ (thường là kernel Linux) cùng với các thành phần đi kèm như DTB hay ramdisk.
- bootz ****và ****booti**:** Các biến thể của boot load cho các loại image khác nhau (zImage cho bootz, Image hoặc thứ tự khác cho booti).
- bootd**:** Trong một số trường hợp, lệnh này dùng để làm “reset” hoặc đưa bộ khởi động về trạng thái ban đầu trước khi bắt đầu quá trình khởi động chính thức.

# **5. Lệnh Giao Tiếp Mạng và Tải Qua Giao Thức Mạng**

Nhiều hệ thống nhúng cần tải hình ảnh hoặc cập nhật qua mạng, nên U‑Boot tích hợp các lệnh liên quan đến kết nối mạng:

- dhcp**:** Yêu cầu cấp phát địa chỉ IP thông qua DHCP, giúp board có thể giao tiếp với các máy chủ trong mạng.
- tftpboot**:** Tải hình ảnh (kernel, ramdisk…) từ máy chủ TFTP về bộ nhớ
- ping**:** Kiểm tra kết nối mạng bằng cách gửi gói tin ICMP đến địa chỉ IP mục tiêu.

# **6. Lệnh Gỡ Lỗi và Kiểm Tra Thông Tin Hệ Thống**

Trong quá trình phát triển, chẩn đoán và gỡ lỗi rất quan trọng, vì vậy U‑Boot cung cấp các lệnh kiểm tra và thông tin:

- bdinfo**:** Hiển thị các thông tin về bo mạch như địa chỉ bộ nhớ, tốc độ bus, và các thông tin cấu hình phần cứng khác.
- reset**:** Thực hiện thao tác reset (khởi động lại) bo mạch, thường được dùng khi muốn thử lại một quy trình khởi động sau khi thay đổi cấu hình.
- Các lệnh kiểm tra khác như iminfo (kiểm tra thông tin ảnh boot) thường được sử dụng để xác thực độ chính xác của hình ảnh đã tải vào bộ nhớ.