# General Booting

Boot Proccess có thể chia thành nhiều giai đoạn (Stage). Tuy nhiên, thông thường sẽ chỉ gồm 2 giai đoạn chính là Single-Stage và Two-Stage. Dưới đây là quá trình mô tả của Two-Stage.

# **1. Soc ROM Bootloader**

Khi hệ thống khởi động lần đầu tiên, hoặc reset. Quyền kiểm soát hệ thống sẽ thuộc về reset vector, nó là một đoạn mã assembly được ghi trước bởi nhà sản xuất chip (**Manufaturer** ). Sau đó reset vector sẽ trỏ tới địa chỉ vùng nhớ chứa các đoạn mã khởi động đầu tiên, cụ thể là boot rom. Nếu không có reset vector thì bộ xử lý sẽ không biết nên thực thi bắt đầu từ đâu.

Chức năng chính của boot rom đấy chính là sao chép nội dung trong file "**MLO**" hay còn được gọi là Second Program Loader (**SPL**) - chương trình tải phụ vào IRAM và excute nó.

Do bộ nhớ của boot rom khá nhỏ nên rom code cũng được giới hạn ở việc khởi tạo một số phần cứng cần thiết cho việc load SPL lên hệ thống như: MMC/eMMC, SDcard, NAND flash. Các phần cứng này được gọi chung là boot device.

Lấy ví dụ load lên từ SDcard:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXci8d0kWGhXwILlhaURxDkquRYnPA23mqUD-7IN9kMrnWOnbopQNXhUgwVdrDrE_msBcLz25fZwbdF-QjHkHX_92aezWu6aeFNr-qZynK4HmWsthK2w6YA9ULj-AVlw1swXMBVN?key=L_Gjf62Qg3JKXWpL6xNacw)

Rom code lựa chọn boot device (load từ thẻ nhớ, flash vv..) phụ thuộc vào việc cấu các pin thông qua switch/jump trên phần cứng.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf9F4VnRVDRdcpHKawIa6Ax8TwVVW7G2GORVBC_u7DY5TPmmnqWBrnebwBRnEMJMtdZt92FYUTeRJQRPoPqX8BmghyR7LRUDJepbBEyU3MFsByxqeWm-AqDrQRnxkUGj8N3p7Bh?key=L_Gjf62Qg3JKXWpL6xNacw)

# **2. Second Program Loader (SPL)**

SPL - chương trình tải phụ. Nhiệm vụ chính của SPL đó chính là tiếp tục setup các thành phần cần thiết như DRAM controler, eMMC vv.. Sau đó load U-boot tới địa chỉ **CONFIG_SYS_TEXT_BASE** của RAM.

- Hay nói ngắn gọn. Chức năng chính của SPL là để load được U-boot lên RAM.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc7fFuRI69t49B4nUStl79JiTXxNWcvi2U7zBQorJob7BeHEYQjWTuxOiEJRikuC18Y9oQ94lbZvD7z7mNu7RB6djQLJpDYN1KwfEcxuVnmr3S3rea5q5QJd9PHKq4a6Zj0qu_h?key=L_Gjf62Qg3JKXWpL6xNacw)

***Note***: *Đối với Single-Stage sẽ không có SPL.*

# **3. U-Boot**

Sau khi được load vào RAM, u-boot sẽ thực hiện việc relocation. Di dời đến địa chỉ relocaddr của RAM (Thường là địa chỉ cuối của RAM) và nhảy đến mã của u-boot sau khi di dời.

Lúc này u-boot sẽ kiểm tra xem file uEnv.txt có tồn tại hay không. Nếu có thực hiện load nó vào RAM ở bước tiếp theo.

Bản thân uEnv.txt là một bootscript, nó định nghĩa các tham số cấu hình, kernel parameters. Các tham số này mặc định đã được cấu hình trong u-boot. Tuy nhiên chúng ta có thể thêm, sửa, xóa các cấu hình này thông qua file uEnv.txt. Việc load uEnv.txt là một sự tùy chọn (Optional), nghĩa là nó có thể có hoặc không.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXesI_AQwr99RUPir88yc0UQ2usWVxkEW3CjOAlnOgtYmGHUFoIRDvFaOJpRKR30av7Jsg2bSDHSr-Swc3hYlJCz1BycZB7r-OlpjkCUUccCDp0KRPBSN9m0K8o1DvmGSv5qMX3w?key=L_Gjf62Qg3JKXWpL6xNacw)

Tiếp theo u-boot sẽ tiếp tục load kernel, device tree vào RAM tại các địa chỉ mà đã được cấu hình từ trước ở trong mã nguồn u-boot hoặc trong file uEnv.txt. Sau cùng nó sẽ truyền toàn bộ kernel parameters và nhường quyền thực thi lại cho kernel.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd7zWpZxpnrPme3UZOCMrCcKZj0MoU6tjMwn_JlDV0zyRsHnfLsafl0qdu75EHcdU9nahEgTU7fgX0VUyLWrXSzD00mJbrThcO7TPFuKVrYv-ujWbWpuOvPoKSxvBgvi7tXotKy_g?key=L_Gjf62Qg3JKXWpL6xNacw)