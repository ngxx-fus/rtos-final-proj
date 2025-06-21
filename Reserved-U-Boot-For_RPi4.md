# Triển khai U-boot trên RPi4

## **1. TẠO BOARD FILE**

Định nghĩa board mới, cấu trúc board bao gồm ít nhất 03 file: Kconfig, Makefile, board.c tại đường dẫn u-boot/board/<vendor>/<board>/. Hình bên dưới mô tả cấu trúc thư định nghĩa board (sao chép từ board/raspberrypi/).

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeRvBohR43ehKt8uCgnzDlJiRzXAIlLAP-Ab7ZvgeKdi0LmiXRTCSFLode8hwi45I4txhIilaNbbumi8ay4W2U3bssI7lHlQvouoQE1IfWlu7K9_4Q0hFNrZMur_p6YX_LcxcsX2w?key=L_Gjf62Qg3JKXWpL6xNacw)

Trong đó:

- vendor : ngxxfus

- board: myrpi4 và myrpi4cp

### **1.1. myrpi4.c**

<board>.c - myrpi4cp.c là mã nguồn chính chứa code khởi tạo phần cứng cụ thể cho bo mạch đó, được thực thi khi U-Boot bắt đầu chạy trên thiết bị.

<board>.h - myrpi4cp.h là nơi định nghĩa cấu hình cụ thể cho phần cứng của một board. Và điều đặc biệt ở file này là nó không được lưu cùng <board>.c mà được lưu ở u-boot/include/configs/ .

### **1.2. Makefile**

Makefile định nghĩa cách mà myrpi4.c nói riêng và các file các trong thưc mục myrpi4cp sẽ được biên dịch.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfZiLzJNXBWpG4HzT7NVkaWqwlIH-wS49yOZN-FXHPF3Q6K_KFoyeDKWMYU53S7OO6_duv6iuInJIGR_GecDhlS7oAD3_oYarCdNXlkRaW_JpCjURC-gLZZjXLgZ6Gdb3Hbqibrmw?key=L_Gjf62Qg3JKXWpL6xNacw)

### **1.3. Kconfig**

Kconfig định nghĩa cấu hình liên quan, có thể thiết lập thông qua menuconfig.

## **2. TẠO SOC FILE**

Sau khi tạo xong Board, tiếp tục tạo SoC file để định nghĩa một SoC tùy chỉnh (dựa trên ARM64). Hình bên dưới là cấu trúc thư mục định nghĩa một SoC. Bao gồm các định nghĩa cơ sở, GPIO, MailBox, Message, ...

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXefTsLACM6kMBxQGPyTvGeJXLDx28WypNZruf152kp_vs9Mnsj3h9XZHE5QzlwbaPLcJxEn47cayY7dLEuVz14nmwB7gQO0UqflN03my59Fq7iADD5QGpgQ4dxEqg7gmBFfS1ho?key=L_Gjf62Qg3JKXWpL6xNacw)

Ghi chú: các file/folder trong include sẽ được liên kết lúc biên dịch thông qua include-path nhờ vậy mã nguồn rõ ràng và dễ thao tác hơn.

### **2.1. Makefile**

Hình bên dưới cho thấy nội dung của Makefile chỉ định luôn biên dịch các file init.c reset.c mbox.c, ... và riêng lowlevel_init.S chỉ khi CONFIG_NF271X được bật mới được biên dịch

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfUhzrEXF7usTN2e6yrefQF5hFyiucRo7ZdULOp_fqFyqvEd75YNsBd2EBKVne7-lTiwcLpF8PwzdfnVDskrsvGmc6ZLu6y4E8RNYon0TQSboCCXFvB0JqH9p9357vO_dgfInaI?key=L_Gjf62Qg3JKXWpL6xNacw)

### **2.2. Kconfig**

Trong file Kconfig, định nghĩa thêm hai phiên bản SoC 32 bit và 64 bit dựa trên SoC NF271X (xem phần 2.3).

| config NF2711
      bool "NgxxFus NF2711 SoC support (32/64-bit)"
      depends on ARCH_NF271X

config NF2711_32B
      bool "NGXXFUS NF2711 SoC 32-bit support"
      depends on ARCH_NF271X
      select NF2711
      select ARMV7_LPAE
      select CPU_V7A
      select PHYS_64BIT   |
| --- |

Từ các dựa trên hai phiên bản SoC, định nghĩa các bo mạch tương ứng:

| menu "NGXXFUS NF271X family"
      depends on ARCH_NF271X
choice
      prompt "Ngxxfus NF271X board select"
      optional

config TARGET_MYRPI4CP_32
      bool "MYRPI4CP_32 build (32-bit)"
      help
            NEW BOARD 32BIT WITH NF2711!

      select NF2711_32B
      imply OF_HAS_PRIOR_STAGE

config TARGET_MYRPI4CP
      bool "MYRPI4CP build (64-bit)"
      help
            NEW BOARD 64BIT WITH NF2711!
      select NF2711_64B
      select MMC
      select MMC_SDHCI
      select MMC_SDHCI_NF2711
      select DM_SERIAL
      imply OF_HAS_PRIOR_STAGE

endchoice

endmenu |
| --- |

Cuối cùng, định nghĩa các thông tin về bo mạch và source đến Kconfig bên trong thư mục định nghĩa board mạch để đảm bảo các Kconfig được tìm thấy bới hệ thống xây dựng (build system)config SYS_BOARD

| config SYS_BOARD
       string
       default "myrpi4cp"

config SYS_VENDOR
      string
      default "ngxxfus"

config SYS_CONFIG_NAME
      string
      default "myrpi4cp"

config SYS_SOC
      default "nf271x"

source "board/ngxxfus/myrpi4cp/Kconfig" |
| --- |

### **2.3. Kconfig (tt)**

Đa số các thư mục bên trong U-boot đều chứa Kconfig để phục vụ cho các thiết lập, để build system có thể nhận diện được board mạch mới thêm, buộc phải thiết lập Kconfig cũng như mà Makefile của thư mục cha hoặc các thư mục liên quan.

### *2.3.1. Thêm lựa chọn SoC NF271X vào <Target select>*

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdlQ-v9Vk08qmRk6gWmwwS3MMd2SSlZ1WJ0sB_gkL5FZdmKBqWFfYzskyIfHjvhS6p9w8k7wYOoOfJFjF6zkrjx0ZtyQjfDo16NLfo-HnOEtVnVfpHTgoRNXtPnsVTKWVZW-TJa?key=L_Gjf62Qg3JKXWpL6xNacw)

### *2.3.2. Source đến file Kconfig trong mach-nf271x*

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeR2GNgl1UzW2C9iK2aXwGgfUI2G5J5YkDqqJRYnGdN9i7PPibvH6SZCV9Alvp3yLxUVbHu1Adl5FQoM974Sbxb4GNbRj-VWQUn5N1tEWqYkZKhSDzN-frNUH-wC18m6w_tDe6s?key=L_Gjf62Qg3JKXWpL6xNacw)

### **2.4. Kết quả trên menuconfig**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdBCK1X4tUPZ5WLEb8HyhKpyXcs73eHGV6iLlj_E7PD-flXD9SIfbop8t0R4-q5DrqaNlF_92_NwXr265Jt9-b80q78me4oBIOaLp3M8lhZDzxjFo6CKc9o0X0vEtB-hJ5QvsNdHQ?key=L_Gjf62Qg3JKXWpL6xNacw)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXet26hVm5WgBleag2-vPR_RoFh7QFGEKbNkdqiBW_sCfFMkbVGj0yRmONTardI5AY98t6fMvbRIzS2Bf3gXKXjPYzfRKJj6g-f_fL2TfjvOLuJcYKUAgrtOu2fRyG9I3U_QLSgCgg?key=L_Gjf62Qg3JKXWpL6xNacw)

## **3. TẠO DEFCONFIG CHO BOARD MYRPI4CP**

DEFCONFIG của một board là file cấu hình mặc định dùng để xác định những tính năng nào được bật hoặc tắt khi biên dịch cho board đó. Các defconfig cuả tất cả các thư mục được chứa bên trong u-boot/configs/ với quy ước là các file này sẽ kết thúc bằng _defconfig VD: myrpi4cp_defconfig

## **4. TẠO DRIVER**

Các driver này chứa định nghĩa vật lý về SoC, cũng như là cách chúng hoạt động. Các driver được phân theo chức năng và được lưu tại u-boot/drivers/ . Bên trong các thư mục chức năng chứa các tập tin định nghĩa cụ thể cho từng board.

## **5. TẠO DEVICE-TREE**

Trong khi driver chứa logic chung để quản lý phần cứng (đọc/ghi thanh ghi, xử lý ngắt, v.v.), nhưng không biết cấu hình cụ thể của phần cứng trên board (địa chỉ bộ nhớ, chân GPIO, tần số clock, v.v.) thì device Tree (file .dts/.dtb) mô tả cấu hình phần cứng của board một cách chi tiết.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfypMHCFo_bA2DpaDzMU1lJpmXqPrcDV9e--R3rtJ9fUcrGtXhHtwo-9gRVhlIKgxnkughwWUG7hX0TWv97gI8B7deScN81YdZ1MGx5wZqtfulHs9FWEh6ws_lwyrg1MJFWq0qnpA?key=L_Gjf62Qg3JKXWpL6xNacw)

Đoạn mã bên dưới mô tả một số đặc điểm phần cứng trong bảng sau:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd4gepQC1LjTFdMC7EWNcqDa8zSqf3hmrxr2u-2BKdRZR2F55hUa-_m0NHqD0saSmfOEJID-ypL_zdqrywDWlD-h40080PgUiEpfIwutJj_HEIjmSI5Aef4cLVmvKnP3qBeZgxhOw?key=L_Gjf62Qg3JKXWpL6xNacw)

## **6. BUILD**

Sau khi hoàn tất các thiết lập, tiến hành build với make.

| #! /bin/sh
my_vender=ngxxfus
my_board=myrpi4cp
defcon=myrpi4cp_defconfig
clear;
make -j8 distclean ;
make -j8 ARCH=arm CROSS_COMPILE=aarch64-linux-gnu-  $defcon;
make -j8 ARCH=arm CROSS_COMPILE=aarch64-linux-gnu-  ; |
| --- |

Kết quả:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfhO2Ud2_6gYq-djxKa0JOLtXhhvakkdSxFqubWGBKmHi_uiY8AGrzDBCaG1XDNTYQQ5XVj89IoQgyaVVs2Z6YdAV8V3StUNwJjOhVOyyLQjEfRxbqoXJKKrppwkM9YHNDBUlxX4Q?key=L_Gjf62Qg3JKXWpL6xNacw)

Quá trình build không có lỗi, nhưng khi nạp vào thẻ SD và chạy trên RPi4 thì lại bị lỗi tắt đột ngột.