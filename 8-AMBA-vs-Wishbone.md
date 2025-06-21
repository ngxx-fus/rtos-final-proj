# So sánh chi tiết: AMBA vs Wishbone

| Tiêu chí                         | AMBA (AXI, AHB, APB - ARM)                                            | Wishbone (OpenCores)                                         |
|----------------------------------|------------------------------------------------------------------------|--------------------------------------------------------------|
| Tổ chức phát triển              | ARM Ltd.                                                              | OpenCores (cộng đồng mã nguồn mở)                           |
| Giấy phép                       | Có thể cần giấy phép khi dùng IP ARM thương mại                       | Hoàn toàn mở, không ràng buộc bản quyền                     |
| Tương thích IP thương mại       | Rất cao (ARM, Xilinx, Altera, Cadence...)                            | Tốt với IP mở (OpenCores), ít dùng trong thương mại         |
| Độ phức tạp thiết kế            | Phức tạp hơn (đặc biệt AXI với burst, QoS, nhiều channel)            | Đơn giản, dễ tích hợp, phù hợp sinh viên & nghiên cứu       |
| Hiệu năng                       | Cao, hỗ trợ song song hóa, pipelining, burst                         | Vừa đủ cho thiết kế nhỏ, không có burst                     |
| Kiểu truyền dữ liệu             | - AXI: song song, burst, nhiều master/slave<br>- AHB: bán song song  | Truyền tuần tự, đơn giản, thường 1 master - 1 slave         |
| Hỗ trợ tính năng nâng cao       | QoS, cache coherency, out-of-order (AXI full)                        | Không hỗ trợ các tính năng cao cấp                          |
| Dễ dùng với FPGA                | Có thể dùng nếu IP hỗ trợ, nhưng cần công cụ/flow ARM tương thích     | Dễ dùng, nhiều core OpenCores có sẵn                        |
| Khả năng mở rộng                | Rất tốt, có thể mở rộng dễ dàng cho SoC lớn                          | Hạn chế, không tối ưu cho hệ thống đa lõi hoặc phức tạp     |
| Tài liệu và hỗ trợ              | Chính thức từ ARM, nhưng có thể giới hạn truy cập                    | Rất nhiều ví dụ, tài liệu mở trên OpenCores                |
| Thường gặp trong                | SoC ARM (Cortex-A, Cortex-M), chip thương mại                        | Dự án nghiên cứu, học thuật, SoC RISC-V nhỏ, FPGA tự làm    |

---

## Khi nào chọn mỗi loại?

### Chọn AMBA khi:
- Bạn dùng CPU ARM (Cortex-M/A/R)
- Cần hiệu năng cao, thiết kế phức tạp, hỗ trợ nhiều IP thương mại
- Dự án ASIC chuyên nghiệp hoặc thương mại

### Chọn Wishbone khi:
- Bạn dùng RISC-V, FPGA hoặc thiết kế CPU riêng
- Mục tiêu là học thuật, mã nguồn mở, dễ hiểu và tự do bản quyền
- Dự án nhỏ, dễ tích hợp và debug

---

## Ghi chú:
- AMBA có nhiều biến thể: AXI (cao cấp), AHB (trung bình), APB (đơn giản)
- Wishbone đơn giản, không có phân cấp bus rõ ràng như AMBA
