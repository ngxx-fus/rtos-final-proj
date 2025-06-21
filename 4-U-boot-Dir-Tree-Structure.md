# U-boot Dir-Tree Structure

# **1. Cây thư mục:**

- Cây thư mục (Device Tree) là hệ thống mô tả phần cứng của board (SoC, RAM, Flash,…). Hệ điều hành (hoặc bootloader như U-Boot) đọc Device Tree Blod (file nhị phân sinh ra từ Cây thư mục) để biết nên điều khiển phần cứng như thế nào.
- Cây thư mục chính:

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeANDsjMTwfADJ6RYnMs1-eVYD4Q3ltxFbsat-xHikzu-r6mRrrFOg_Q-1mNVKZ_p4NewbYQDfvaVC-Jm7GexyD1Nlw5aXDlSv5G3gy_h2fxtrC7e6LDJxufCJjCVQ2FoNMKj52Fw?key=L_Gjf62Qg3JKXWpL6xNacw)

---

**Giải thích:**

- **Kconfig** là hệ thống cấu hình dùng trong U-Boot (tương tự như Linux Kernel). Nó định nghĩa các tùy chọn cấu hình (config options) mà người dùng có thể bật/tắt khi biên dịch hệ thống. Mỗi Kconfig file mô tả:
- Lựa chọn phần cứng, tính năng (board, SoC, driver…)
- Phụ thuộc giữa các lựa chọn.
- Nhóm các lựa chọn lại thành menu hoặc submenu
    
    Ví dụ:
    
- File: *arch/arm/Kconfig*
- Là entry point chính cho cấu hình hệ kiến trúc ARM trong U-Boot.
- Lập trình viên sẽ tham chiếu đến các Kconfig nhỏ hơn bên trong, ví dụ: source "*arch/arm/mach-nf271x/Kconfig*"
- Các dòng config sẽ chọn SoC, CPU, và kiến trúc vi xử lý
- File: *arch/arm/mach-nf271x/Kconfig*
- Định nghĩa các thiết lập dành riêng cho SoC dòng **NF271X**.
- Có thể chứa các tùy chọn liên quan đến bộ khởi động (bootloader), bộ nhớ, clock…

-**Thư mục *board/ngxxfus/myrpi4cp/*** chứa mã nguồn khởi động riêng cho board tùy chỉnh *myrpi4cp*. Các file chính:

| Tên File | Vai trò |
| --- | --- |
| *Makefile* | Chỉ định các file cần biên dịch cho board này. |
| *myrpi4cp.c* | Cài đặt hàm khởi động board, setup pin, RAM, UART,... |
| *myrpi4cp.h* | Header chứa định nghĩa cấu trúc, macro cho board. |
| *Kconfig* | Khai báo các tùy chọn liên quan SoC này. |
- **Thư mục *arch/arm/mach-nf271x/*** đại diện cho phần SoC cụ thể (NF271X) trong hệ kiến trúc ARM, gồm các file sau:

| Tên File | Vai trò |
| --- | --- |
| *cpu.c* | Hàm khởi tạo CPU, thiết lập cache, MMU. |
| *lowlevel_init.S* | Hàm khởi động ở mức rất thấp (Assembly) - setup clock, DRAM. |
| *timer.c* | Thiết lập timer, clock ticks. |
| *Kconfig* | Khai báo các tùy chọn liên quan SoC này. |
- **File *configs/myrpi4cp_defconfig***
- Đây là file cấu hình mặc định dùng để build U-Boot cho board.
- Nó bật CONFIG_TARGET_MYRPI4CP=y và các tùy chọn liên quan khác như bộ nhớ, bộ nạp SPI, UART…
- Khi chạy make *myrpi4cp_defconfig*, hệ thống sẽ đọc file này và sinh ra *.config*.

# **Các thành phần bên trong cây thư mục**

## **2.1. arch/ - Mã nguồn liên quan đến kiến trúc cụ thể**

Mã nguồn trong thư mục arch/ tập trung xử lý phần khởi tạo thấp nhất và đặc thù cho từng kiến trúc CPU như ARM, x86,… Nhiệm vụ chính bao gồm:

- Thiết lập các thanh ghi đặc thù CPU
- Khởi tạo stack pointer
- Thiết lập MMU, cache
- Chuẩn bị môi trường chạy cho các đoạn mã C tiếp theo

Ví dụ:

Thư mục *arch/arm/mach-nf271x/* chứa mã khởi tạo đặc thù cho dòng SoC NF271x, trong đó có các file nguồn cấu hình clock, UART, bộ nhớ, và các thiết bị đặc trưng cho board. Ví dụ, file *arch/arm/mach-nf271x/nf271x_init.c* có hàm *nf271x_board_init_f()* gọi trong giai đoạn khởi động ban đầu để thiết lập UART cho debug và cấu hình bộ nhớ.

- ***/home/ngxxfus/Desktop/mod_uboot/arch/arc/dts - Thư mục chứa Device Tree Source (DTS) cho kiến trúc vi xử lý ARC (Argonaut RISC Core)***

ARC là một dòng CPU nhúng do Synopsys phát triển, được dùng trong các SoC tùy chỉnh, bộ xử lý nhúng nhỏ gọn (tương tự như RISC-V hay ARM Cortex-M).

Thư mục dts thường chứa*:*

| Tên file | Mô tả |
| --- | --- |
| **.dts* | File định nghĩa thiết bị cho một board cụ thể (Device Tree Source). |
| **.dtsi* | File chung được include trong nhiều DTS (Shared Includes). |
| **Makefile* | Tập tin build để biên dịch DTS thành DTB (Device Tree Blod). |
- ***arch/nnn/mach-foo - Mã nguồn dành riêng cho dòng SoC hoặc họ chip***

Mã nguồn trong thư mục này tập trung cho từng dòng SoC, có nhiệm vụ cấu hình bộ nhớ, thiết lập clock, UART, timer, và các thiết bị phần cứng đặc thù của SoC, ví dụ:

- *arch/arm/mach-imx/* - cho dòng i.MX của NXP.
- *arch/arm/mach-nf271x/* - cho SoC NF271x (repo bạn gửi).
- **arch/nnn/dts - Cây mô tả phần cứng (Device Tree)**

Thư mục *arch/arm/dts/* chứa các file *.dts* và *.dtsi* mô tả phần cứng của board, giúp U-Boot và kernel Linux biết được:

- CPU có những tính năng gì
- Bộ nhớ RAM kích thước bao nhiêu, địa chỉ ra sao
- Các thiết bị ngoại vi: UART, I2C, SPI, Ethernet, SD/MMC, HDMI, GPIO…

## **2.2. board / - Mã nguồn dành riêng cho từng bo mạch**

Thư mục board/ chứa mã nguồn khởi tạo đặc thù cho từng bo mạch cụ thể. Nhiệm vụ của phần này là thiết lập các thiết bị cấp board như:

- Cấu hình GPIO, LED
- Khởi tạo UART để debug
- Khởi tạo USB, Ethernet PHY
- Thiết lập watchdog timer
- Cấu hình bộ nhớ và các thành phần phần cứng đặc thù khác

Đảm bảo U-Boot chạy đúng và ổn định trên từng bo mạch vật lý.

Ví dụ: Thư mục *board/ngxxfus/myrpi4cp/* là mã nguồn dành cho bo mạch RPi4 trong dự án: *raspberrypi4.c* (chứa code dùng chung cho nhiều đời Raspberry Pi, không chỉ RPi4, ví dụ như các hàm chung cho UART hoặc GPIO) - tập trung xử lý khởi tạo các phần cứng cấp board-level như:

- Điều khiển LED báo trạng thái
- Thiết lập UART để in debug messages
- Khởi động watchdog để bảo vệ hệ thống

## **2.3. config/ — Cấu hình U-Boot theo từng bo mạch**

Các file .h hoặc .Kconfig chứa định nghĩa cấu hình build cụ thể cho bo mạch. Điều khiển U-Boot sẽ build với tính năng gì, hỗ trợ phần cứng nào.

Ví dụ: Tập tin này kích hoạt các tính năng cụ thể cho build U-Boot:

- Cho phép boot từ USB
- Hỗ trợ bàn phím USB
- Có thể đọc file .img từ thẻ SD/USB (FAT/ext4)
- Sử dụng device tree tương ứng.

## **2.4. include/ - Các tập tin header chứa định nghĩa toàn cục**

Thư mục này chứa các file .h dùng chung toàn bộ U-Boot. Trong đó khai báo các hàm, macro, cấu trúc (struct) được sử dụng xuyên suốt. Ví dụ một số file quan trọng:

- *include/common.h* - khai báo các hàm chuẩn, các tiện ích phổ biến.
- *include/linux/errno.h* — định nghĩa các mã lỗi tương tự Linux.
    - **include/configs/ - Cấu hình cũ theo từng bo mạch**

Đây là thư mục chứa các file cấu hình theo từng bo mạch sử dụng cách khai báo macro C, được áp dụng trong các board cũ hoặc trong các trường hợp legacy, trước khi hệ thống cấu hình chuyển sang Kconfig. Ví dụ điển hình là file cấu hình cho board *myrpi4cp*:

**File:** [*include/configs/myrpi4cp.h*](https://github.com/hg-xnb/learn-uboot/blob/master/include/configs/myrpi4cp.h)

## **2.5. drivers/ - Trình điều khiển (driver) của U-Boot**

Mã điều khiển phần cứng: UART, USB, Ethernet, SPI, I2C, GPIO….Chịu trách nhiệm giao tiếp với phần cứng như bộ nhớ Flash, thẻ SD, bàn phím,...

Ví dụ: Các driver quan trọng mà RPi4 cần:

- *drivers/usb/host/xhci-pci.c* – hỗ trợ USB 3.0 (trên RPi4)
- *drivers/mmc/bcm2835_sdhost.c* – đọc thẻ nhớ SD
- *drivers/net/smsc95xx.c* – driver mạng Ethernet (LAN9514)
- *drivers/video/bcm2835.c* – xuất HDMI (nếu dùng U-Boot graphics)

Trong repo, các driver thường có hàm reset hoặc hàm khởi ===================== WARNING ======================
CONFIG_OF_EMBED is enabled. This option should only
be used for debugging purposes. Please use
CONFIG_OF_SEPARATE for boards in mainline.
See doc/develop/devicetree/control.rst for more info.
====================================================
tạo lại thiết bị, mục đích là đưa phần cứng về trạng thái ban đầu, sẵn sàng hoạt động.

**driver Ethernet smsc95xx.c** trong drivers/net/smsc95xx.c — driver cho chip LAN9514 Ethernet.

- **cmd / - Các lệnh của U-Boot**

Thư mục này chứa mã nguồn định nghĩa các lệnh CLI mà người dùng có thể nhập khi chạy U-Boot, ví dụ: help, boot, printenv, setenv... Đây là giao diện tương tác để người dùng cấu hình và khởi động hệ thống.

- **common/ lib/ - Mã nguồn chung**

Thư mục chứa các hàm tiện ích, thư viện dùng chung, không phụ thuộc kiến trúc CPU hay bo mạch cụ thể. Ví dụ: xử lý chuỗi, bộ nhớ, các hàm hỗ trợ chung.

## **2.6. fs/ - Triển khai hệ thống tập tin**

Chứa mã để truy cập các hệ thống tập tin phổ biến như FAT, ext2/3/4, UBIFS... Giúp U-Boot có thể đọc file từ thẻ nhớ, ổ cứng hoặc NAND. Thư mục *fs/fat/* chứa các file xử lý hệ thống FAT.

Một số file tiêu biểu trong *fs/fat/*:

- *fat.c* - các hàm chính xử lý đọc file, thư mục, quản lý cluster FAT.

**Chức năng:**

**Đọc boot sector:** Phân tích cấu trúc phân vùng FAT, lấy thông tin về số cluster, sectors, size, loại FAT.

**Quản lý cluster:** Xác định vị trí dữ liệu của file/thư mục dựa trên bảng FAT (File Allocation Table).

**Đọc file:** Lấy dữ liệu từ cụm cluster liên tục, xử lý các cluster rải rác bằng cách truy cập bảng FAT.

**Hỗ trợ thư mục:** Đọc và duyệt danh sách file trong thư mục FAT.

- *fatfs.h* - định nghĩa cấu trúc dữ liệu và hàm liên quan cho FAT.
- *fat_private.h* - các khai báo nội bộ hỗ trợ xử lý FAT.
- *fat_find_file()* - tìm file theo tên.
- *fat_read_clusters()* - đọc dữ liệu file dựa trên cluster.

**Lưu ý:** *Kconfig* and *Makefile* xuất hiện xuyên suốt toàn bộ cấu trúc

- *Kconfig*: Hệ thống cấu hình dùng menuconfig (như trong Linux kernel).

Cho phép chọn tính năng theo bo mạch, SoC và hỗ trợ USB, Ethernet, SPI…

- *Makefile*: Kiểm soát cách U-Boot được build. Quy trình biên dịch, liên

kết mã nguồn và chọn tập tin .c nào sẽ được biên dịch cho bo mạch.