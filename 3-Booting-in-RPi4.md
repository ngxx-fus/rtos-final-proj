# Booting in RPi4

# **1. Khởi động ban đầu với Boot ROM**

Boot ROM là điểm khởi đầu đảm bảo hệ thống có một nền tảng phần cứng ổn định. Vì được cài đặt cố định (immutable), nó đóng vai trò giống như “mẹ đẻ” của toàn bộ quá trình boot, chỉ cho phép các phần mềm khởi động sau đó dựa vào nền tảng đã được thiết lập từ trước.

**Vai trò của Boot ROM:**

- **Cốt lõi không thay đổi:** Boot ROM là một đoạn mã bất biến (firmware) được khắc sẵn trực tiếp vào chip của SoC (System on Chip). Nó không thể chỉnh sửa được bởi người dùng, giúp đảm bảo tính an toàn và tính ổn định của bước khởi động đầu tiên.
- **Khởi tạo phần cứng cơ bản:** Ngay khi cấp nguồn, Boot ROM thực hiện việc thiết lập các thanh ghi, cấu hình bộ nhớ cấp tốc (caches) và chuẩn bị các dịch vụ nội bộ như DMA (Direct Memory Access). Quá trình này giúp phần cứng chuyển về trạng thái hoạt động cơ bản, sẵn sàng cho các bước tiếp theo.
- **Xác định điểm chuyển giao:** Sau các kiểm tra ban đầu và cấu hình cơ bản, Boot ROM xác định vị trí Bootloader được lưu trên EEPROM và chuyển giao quyền điều khiển cho nó.

# **2. Bootloader từ EEPROM: Lớp trung gian linh hoạt**

Bootloader trên EEPROM là một chương trình “nửa mềm – nửa cứng”, vì nó vừa bất biến (được Boot ROM gọi đầu tiên), vừa có thể cập nhật để thêm tính năng hoặc sửa lỗi. Trên Raspberry Pi 4, bootloader được lưu trực tiếp trong EEPROM trên bo mạch — khác với các phiên bản trước, vốn phụ thuộc hoàn toàn vào bootcode từ thẻ microSD.

**Vai trò và đặc điểm:**

- Bootloader trong EEPROM hoạt động như một cầu nối giữa phần cứng bất biến (Boot ROM) và phần mềm có thể cập nhật (kernel, hệ điều hành, hoặc các bootloader thứ cấp như U-Boot). Nhờ đó, nó có thể kiểm tra thiết bị lưu trữ, nạp firmware GPU (start4.elf), đọc cấu hình từ config.txt, và chuyển quyền điều khiển sang kernel một cách linh hoạt. ****Bootloader mặc định trong EEPROM không phải là U-Boot.
- **Xác định nguồn cấp boot:** Bootloader đọc các tệp cấu hình (ví dụ: config.txt) trên phân vùng boot của nguồn được phát hiện. Nó được lập trình để kiểm tra các nguồn boot theo thứ tự ưu tiên (microSD, USB, hay network boot) và tìm ra nguồn có các file cần thiết.
- config.txt là tệp cấu hình được bootloader firmware của GPU đọc và thực thi trước khi kernel Linux được nạp. Cấu hình trong config.txt ảnh hưởng trực tiếp đến các thành phần phần cứng và các module firmware
- **Nạp firmware phụ thuộc phần cứng:** Sau khi phát hiện nguồn boot, bootloader nạp các file firmware như start4.elf và fixup4.dat. Các file này chịu trách nhiệm khởi động GPU và thiết lập các bộ điều khiển, giúp đảm bảo hiệu suất đồ họa cũng như các giao diện cơ bản đúng theo cấu hình phần cứng của Raspberry Pi 4.

# **3. Nạp kernel và Device Tree: Bước chuyển giao quyền điều khiển**

Việc sử dụng Device Tree thay cho các phương thức hard-coded cấu hình phần cứng giúp hệ thống trở nên linh hoạt hơn và dễ bảo trì. Cấu trúc boot này còn minh họa nguyên tắc “chain of trust” (chuỗi tin cậy), bắt đầu từ mã bất biến cho đến các thành phần có thể cập nhật, qua đó giảm thiểu lỗi cấu hình hệ thống.

**Các bước quan trọng:**

- **Tìm nạp hình ảnh kernel:** Sau quá trình khởi tạo của bootloader, hệ thống sẽ nạp file kernel (thường là kernel8.img cho kiến trúc 64-bit của Pi 4) từ phân vùng boot của thiết bị chứa hệ điều hành.
- **Device Tree Blob (DTB):** Cùng lúc đó, bootloader nạp file Device Tree – một file nhị phân chứa thông tin mô tả chi tiết về cấu hình phần cứng của bo mạch (số lượng RAM, loại giao tiếp, thiết bị ngoại vi, …). DTB cho phép kernel “nhận diện” và cấu hình tự động các thành phần phần cứng theo đúng thiết kế của board.
- **Truyền tham số boot:** Bootloader truyền cho kernel các tham số cấu hình cần thiết (boot arguments) như địa chỉ bộ nhớ, cấu hình hệ thống, và thông tin về phân vùng gốc của hệ điều hành.
- **Chuyển giao quyền:** Khi kernel và DTB đã có mặt trong bộ nhớ, bootloader chuyển giao quyền điều khiển cho kernel – đánh dấu sự chuyển giao từ giai đoạn “khởi động” sang giai đoạn “vận hành hệ điều hành”.

Việc sử dụng Device Tree thay cho các phương thức hard-coded cấu hình phần cứng giúp hệ thống trở nên linh hoạt hơn và dễ bảo trì. Cấu trúc boot này còn minh họa nguyên tắc “chain of trust” (chuỗi tin cậy), bắt đầu từ mã bất biến cho đến các thành phần có thể cập nhật, qua đó giảm thiểu lỗi cấu hình hệ thống.

# **4. Khởi tạo hệ điều hành: Từ kernel đến môi trường làm việc**

Giai đoạn này minh họa triết lý của các hệ thống Unix – mỗi thành phần nhỏ được khởi tạo độc lập và sau đó “liên kết” lại với nhau thành một hệ thống làm việc đồng bộ. Việc phân biệt rõ ràng giữa các bước (khởi tạo driver, mounting filesystem, chạy tiến trình chính) đảm bảo tối thiểu lỗi và tăng tính bảo mật cũng như khả năng

- **Khởi tạo phần cứng và driver:** Sau khi nhận quyền điều khiển, kernel tiến hành dò tìm và khởi tạo các thiết bị phần cứng, nạp các driver phù hợp. Mỗi driver được nạp nhằm đảm bảo rằng thiết bị ngoại vi như USB, mạng, đồ họa,… hoạt động đúng vai trò của chúng.
- **Mounting Filesystem:** Kernel gắn kết (mount) hệ thống tập tin gốc (root filesystem) từ phân vùng lưu trữ đã xác định. Đây là một bước quan trọng cho phép hệ điều hành truy cập và quản lý dữ liệu người dùng.
- **Khởi chạy tiến trình ‘init’:** Kernel gọi chương trình init (trên Linux có thể là init truyền thống hoặc systemd hiện đại). Đây là quá trình quản lý các dịch vụ hệ thống, từ đó khởi tạo giao diện người dùng hoặc các tiến trình nền cần thiết cho toàn bộ môi trường hệ thống.