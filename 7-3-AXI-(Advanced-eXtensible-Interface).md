# AXI-(Advanced eXtensible Interface)

# **1. Giới thiệu**

## **1.1 Giới thiệu về giao thức AXI**

AXI (Advanced eXtensible Interface) là một giao thức bus thuộc họ AMBA do ARM phát triển. AXI được thiết kế để phục vụ các hệ thống hiệu năng cao, tần số cao, đặc biệt phù hợp cho việc truyền dữ liệu giữa các khối phần cứng như CPU, DMA, bộ nhớ, và các thiết bị ngoại vi.

**Các đặc điểm chính của AXI:**

- **Băng thông cao và độ trễ thấp:** Đảm bảo truyền tải dữ liệu nhanh chóng trong hệ thống.
- **Hỗ trợ hoạt động tần số cao:** Thiết kế đơn giản, không cần các bộ cầu phức tạp.
- **Tương thích rộng:** Hỗ trợ nhiều loại thiết bị và bộ điều khiển bộ nhớ có độ trễ truy cập cao.
- **Kiến trúc linh hoạt:** Cho phép mở rộng và tùy biến dễ dàng với nhiều kiểu liên kết.
- **Tương thích ngược:** Có thể tích hợp với các giao thức AMBA trước như AHB và APB.

## **1.2 Kiến trúc AXI**

AXI sử dụng kiến trúc **nhiều kênh (multi-channel)** để truyền tải dữ liệu, bao gồm:

| **Kênh** | **Mô tả** |
| --- | --- |
| **AW (Write Address Channel)** | Chứa thông tin địa chỉ và thuộc tính của yêu cầu ghi. |
| **W (Write Data Channel)** | Truyền dữ liệu thực tế cần ghi. |
| **B (Write Response Channel)** | Gửi phản hồi về trạng thái giao dịch ghi (ví dụ OKAY, ERROR). |
| **AR (Read Address Channel)** | Chứa địa chỉ và thông tin thuộc tính của yêu cầu đọc. |
| **R (Read Data Channel)** | Truyền dữ liệu đọc từ thiết bị subordinate về manager. |

**Điểm đặc biệt:**

- Mỗi kênh có **cơ chế bắt tay độc lập** (handshake), giúp hỗ trợ truyền tải song song và tăng hiệu suất.
- Các kênh hoạt động **không đồng bộ với nhau**, cho phép xử lý các phần của giao dịch tại các thời điểm khác nhau.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc8C63yHEjVpxO6WzuhlIM0kQxhZIIzqRywSof1YxLDnJBSxfRaiXF8HNQKRG-no94BTh5AwbCejCYmDPy_SAwJxJKKV4LqyxQFoVPh9oS1Ov-Gj1pg5V_Q8pmnNH4g2NtAQo1E5g?key=L_Gjf62Qg3JKXWpL6xNacw)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeuvrN2LGBTwzHjou7UPcttvAxh4i3aOdEmNcq9JUMurlqSnlE3e3rZtCORncIqxYBAPQS0kNrKGTQpYYz1xvpPh84QAIzgDzufum-l1Ty3zfAZxm2E1zT8OmPWTuIhuGFtk5qg?key=L_Gjf62Qg3JKXWpL6xNacw)

**Giao diện và kết nối liên kết (Interface and Interconnect)** Trong kiến trúc AXI, có thể chia thành ba loại phần tử:

**Manager Interface (Giao diện phía yêu cầu):**

- Là nơi phát sinh các yêu cầu đọc/ghi.
- Có thể là CPU, bộ điều khiển DMA, v.v.

**Subordinate Interface (Giao diện phản hồi):**

- Là nơi xử lý và phản hồi các yêu cầu.
- Ví dụ: bộ nhớ DDR, thiết bị ngoại vi.

**Interconnect (Bộ kết nối liên kết):**

- Cấu trúc trung gian kết nối nhiều manager với nhiều subordinate.
- **Crossbar**: cho phép nhiều giao dịch độc lập đi qua.
- **Bus đơn**: chia sẻ băng thông nhưng giới hạn đồng thời.
- **Các chức năng chính của interconnect:**
- Gửi đúng tín hiệu đến đúng địa chỉ đích.
- Giữ và định tuyến các giao dịch.
- Ánh xạ địa chỉ (address decoding).
- Đảm bảo trật tự và phân loại giao dịch nếu cần.

## **1.3. Thuật ngữ**

Thành phần và kiến trúc AXI

- Manager: Thiết bị khởi tạo các yêu cầu đọc/ghi (ví dụ: CPU, DMA).
- Subordinate: Thiết bị phản hồi các yêu cầu đó (ví dụ: bộ nhớ).
- Interconnect: Kết nối giữa nhiều Manager và Subordinate.

Giao dịch và truyền tải

- Transaction (Giao dịch): Một chuỗi hành động – gửi yêu cầu, nhận dữ liệu, nhận phản hồi.
- Transfer (Truyền tải): Một phần dữ liệu nhỏ bên trong một giao dịch.
- Burst: Nhiều truyền tải liên tục trong một giao dịch.

Bộ đệm và hoạt động bộ đệm

- Các thành phần có thể sử dụng cache để tăng hiệu năng.
- Giao thức AXI hỗ trợ quản lý cache, bao gồm các chế độ chia sẻ và không chia sẻ.

Mô tả theo thời gian

- Các kênh hoạt động độc lập, sử dụng cơ chế bắt tay với tín hiệu VALID và READY.
- Việc này giúp tăng hiệu suất và giảm độ trễ trong hệ thống.

# **2. Danh sách tín hiệu (Signal list)**

Giao thức AXI định nghĩa các nhóm tín hiệu riêng biệt cho từng loại kênh truyền dữ liệu. Mỗi kênh bao gồm các tín hiệu điều khiển và dữ liệu được sử dụng để thiết lập và thực hiện các giao dịch đọc/ghi giữa Manager và Subordinate. Ngoài ra, còn có các tín hiệu hỗ trợ cho snoop, đồng bộ, đánh thức, và quản lý chất lượng dịch vụ (QoS).

## **2.1. Write Request Channel (AW*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| AWVALID | Yêu cầu ghi hợp lệ từ Manager |
| AWREADY | Subordinate sẵn sàng nhận yêu cầu |
| AWADDR | Địa chỉ ghi |
| AWID | Transaction ID cho yêu cầu |
| AWLEN | Số lượng transfer trong burst |
| AWSIZE | Kích thước của mỗi transfer |
| AWBURST | Kiểu burst |
| AWCACHE | Thuộc tính cache |
| AWPROT | Thuộc tính bảo mật và quyền truy cập |
| AWQOS | Chỉ số QoS |
| AWREGION | Vùng địa chỉ |
| AWUSER | Tín hiệu người dùng tùy chọn |

## **2.2. Write Data Channel (W*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| WVALID | Dữ liệu hợp lệ từ Manager |
| WREADY | Subordinate sẵn sàng nhận dữ liệu |
| WDATA | Dữ liệu ghi |
| WSTRB | Byte strobe (chọn byte hợp lệ) |
| WTAG | Tag cho dữ liệu ghi |
| WLAST | Đánh dấu transfer cuối cùng trong burst |
| WUSER | Tín hiệu người dùng tùy chọn |
| WPOISON | Báo lỗi dữ liệu |
| WTRACE | Tín hiệu trace |

## **2.3 Write Response Channel (B*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| BVALID | Phản hồi hợp lệ từ Subordinate |
| BREADY | Manager sẵn sàng nhận phản hồi |
| BRESP | Mã phản hồi giao dịch |
| BUSER | Tín hiệu người dùng tùy chọn |
| BTRACE | Tín hiệu trace |
| BLOOP | Loopback tín hiệu |
| BBUSY | Trạng thái bận của Subordinate |

## **2.4. Read Request Channel (AR*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| ARVALID | Yêu cầu đọc hợp lệ từ Manager |
| ARREADY | Subordinate sẵn sàng nhận yêu cầu |
| ARADDR | Địa chỉ đọc |
| ARID | Transaction ID |
| ARLEN | Số lượng transfer trong burst |
| ARSIZE | Kích thước của mỗi transfer |
| ARBURST | Kiểu burst |
| ARCACHE | Thuộc tính cache |
| ARPROT | Quyền truy cập và bảo mật |
| ARQOS | Chỉ số QoS |
| ARSNOOP | Snoop request type (ACE extension) |
| ARTRACE | Tín hiệu trace |

## **2.5. Read Data Channel (R*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| RVALID | Dữ liệu đọc hợp lệ từ Subordinate |
| RREADY | Manager sẵn sàng nhận dữ liệu |
| RDATA | Dữ liệu đọc |
| RRESP | Mã phản hồi giao dịch đọc |
| RLAST | Đánh dấu transfer cuối cùng |
| RUSER | Tín hiệu người dùng tùy chọn |
| RTRACE | Tín hiệu trace |
| RPOISON | Báo lỗi dữ liệu |

## **2.6. Snoop Request Channel (AC*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| ACVALID | Yêu cầu snoop hợp lệ từ Manager |
| ACREADY | Subordinate sẵn sàng nhận yêu cầu |
| ACADDR | Địa chỉ snoop |
| ACVMIDEXT | Virtual Machine ID extension |
| ACTRACE | Tín hiệu trace |

## **2.7. Snoop Response Channel (CR*)**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| CRVALID | Phản hồi snoop hợp lệ từ Subordinate |
| CRREADY | Manager sẵn sàng nhận phản hồi |
| CRTRACE | Tín hiệu trace |

## **2.8 Interface level signals**

| **Tín hiệu** | **Mô tả** |
| --- | --- |
| ACLK | Tín hiệu clock chính |
| ARESETn | Reset bất đồng bộ, active low |
| AWAKEUP | Wake-up request từ Manager |
| ACWAKEUP | Wake-up request từ Subordinate |
| VAWQOSACCEPT | QoS Accept cho write |
| VARQOSACCEPT | QoS Accept cho read |
| SYSCOREQ | Yêu cầu coherency connection từ Manager |
| SYSCOACK | Phản hồi coherency connection từ Subordinate |
| BROADCASTATOMIC | Cho phép broadcast atomic |
| BROADCASTSHAREABLE | Cho phép broadcast shareable |
| BROADCASTCACHEMAINT | Cho phép broadcast cache maintenance |

# **3. AXI Transport**

## **3.1 Clock and Reset**

### **Clock (ACLK)**

- Là **tín hiệu clock chính** của giao diện AXI.
- **Tất cả các tín hiệu input được chốt (sample)** vào cạnh lên (rising edge) của ACLK.
- **Tín hiệu output chỉ thay đổi sau cạnh lên tiếp theo.**
- Điều này đảm bảo mọi giao tiếp đều **đồng bộ hóa clock**, tránh lỗi timing.

### **Reset (ARESETn)**

- Tín hiệu reset bất đồng bộ, active-low (0 là reset, 1 là hoạt động bình thường).
- Khi reset:
    - Manager phải giữ AWVALID, WVALID, ARVALID = 0
    - Subordinate phải giữ BVALID, RVALID = 0
- Deassert reset (từ 0 về 1) phải đồng bộ với ACLK để đảm bảo an toàn trạng thái.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdlbyvIuCArQwrFgNxAkrxSV3cIRj25w-sEBb1cYA9tTef3IdUP8RIVEcPVrx0SKVOSzRqku9vnjaSN2_qcfnyHgC1Y6E6moDlpDsHz3wVSusKsz5xTEYKFhCFGgXSYfuzuiYm-hg?key=L_Gjf62Qg3JKXWpL6xNacw)

## **3.2 Channel Handshake**

Cơ chế **VALID/READY handshake** đảm bảo hai bên (source & destination) đồng ý khi gửi và nhận dữ liệu.

| **Source (Sender)** | **Destination (Receiver)** | **Điều kiện chuyển dữ liệu** |
| --- | --- | --- |
| VALID = 1 | READY = 1 | Dữ liệu transfer |
- **VALID:** Source thông báo dữ liệu sẵn sàng.
- **READY:** Destination sẵn sàng nhận dữ liệu.
- Chỉ khi **cả hai cùng lên 1** ở cạnh lên ACLK thì dữ liệu mới được chuyển.

=> VALID phải giữ nguyên cho đến khi handshake xong.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd3qa5Ez12SR7dMx1yG4ktnk8eEBcm7Vh1v18PysL1VVq6FVCYv5iN3v2QIumrsWC1zvIHRIvGB4RdFYB_KIQ-aKDm38G6ye_k0PwuWFmNKU6bBNWfHT0luZ_L49pL1ZJt6mwlB?key=L_Gjf62Qg3JKXWpL6xNacw)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeZ4eQ1gu0LqXNtssuehzkwNBr2bcOTw6jdWGml2K6q-T99LMRZuryk2KeXyUj8Tf8RIVlYBwDoaI4B97wdh_I-jNx9V-tW_rs_aVo96xkDSifnA1GgwhGrYPdsPpMRyruZoIKasw?key=L_Gjf62Qg3JKXWpL6xNacw)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe-48ZS1AhBWec82EVw3gtlro1j2-UI-kWwhPFVtkCVSTIEkFj29P8gaus90nd6jtG1NzeVkRcoe4-9tqhZ2h_ihCAHeEmFB3SCn64B06IWINwejAHzkmYGSZR0BB4Q4Dy_TSasTA?key=L_Gjf62Qg3JKXWpL6xNacw)

## **3.3 Write and Read Channels**

Gồm 5 channel chính:

**Write Request Channel (AW)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| AWVALID | 1 | Manager | Báo yêu cầu ghi hợp lệ |
| AWREADY | 1 | Subordinate | Báo sẵn sàng nhận yêu cầu ghi |

VALID phải giữ đến khi handshake.

Khuyến nghị AWREADY mặc định HIGH để giảm số chu kỳ.

**Write Data Channel (W)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| WVALID | 1 | Manager | Báo dữ liệu ghi hợp lệ |
| WREADY | 1 | Subordinate | Báo sẵn sàng nhận dữ liệu |
| WLAST | 1 | Manager | Báo transfer cuối cùng trong burst |

Khuyến nghị WREADY mặc định HIGH.

Manager phải assert WLAST cho transfer cuối.

**Write Response Channel (B)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| BVALID | 1 | Subordinate | Phản hồi ghi hợp lệ |
| BREADY | 1 | Manager | Sẵn sàng nhận phản hồi |

BVALID phải giữ đến khi handshake với BREADY.

**Read Request Channel (AR)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| ARVALID | 1 | Manager | Yêu cầu đọc hợp lệ |
| ARREADY | 1 | Subordinate | Sẵn sàng nhận yêu cầu |

Khuyến nghị ARREADY mặc định HIGH.

**Read Data Channel (R)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| RVALID | 1 | Subordinate | Dữ liệu đọc hợp lệ |
| RREADY | 1 | Manager | Sẵn sàng nhận dữ liệu |
| RLAST | 1 | Subordinate | Transfer cuối trong burst |

RVALID phải giữ đến khi handshake với RREADY.

## **3.4 Relationships Between the Channels**

- Các channel hoạt động độc lập.
- Không yêu cầu thứ tự giữa request, data và response.
- Manager có thể gửi tiếp transaction mới khi các response trước chưa về.

## **3.5 Dependencies Between Channel Handshake Signals**

**Write Transaction Dependencies**

- AW và W channel handshake độc lập.
- Subordinate có thể nhận data trước hoặc sau address.
- Response (B) chỉ xảy ra sau khi toàn bộ write data đã handshake xong.

**Read Transaction Dependencies**

- AR và R channel handshake độc lập.
- Data được trả về theo thứ tự hoặc out-of-order tùy thiết kế.
- RVALID và RLAST phải đúng quy tắc.

## **3.6 Snoop Channels**

Chỉ có ở hệ thống hỗ trợ DVM.

**Snoop Request Channel (AC)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| ACVALID | 1 | Subordinate | Yêu cầu snoop hợp lệ |
| ACREADY | 1 | Manager | Sẵn sàng nhận |

**Snoop Response Channel (CR)**

| **Tín hiệu** | **Width** | **Source** | **Mô tả** |
| --- | --- | --- | --- |
| CRVALID | 1 | Manager | Phản hồi snoop hợp lệ |
| CRREADY | 1 | Subordinate | Sẵn sàng nhận |

**Snoop Transaction Dependencies**

- Subordinate không đợi ACREADY trước khi ACVALID.
- Manager đợi cả ACVALID và ACREADY trước khi CRVALID.
- Tương tự các channel khác, VALID phải giữ đến khi handshake.

# **4. AXI Transactions**

## **4.1 Transaction request**

Size attribute

- Xác định số byte tối đa cho mỗi data transfer.
- Giá trị qua AWSIZE và ARSIZE (3 bit).
- Encoding:

| **AxSIZE** | **Số byte/transfer** |
| --- | --- |
| 0b000 | 1 |
| 0b001 | 2 |
| 0b010 | 4 |
| 0b011 | 8 |
| 0b100 | 16 |
| 0b101 | 32 |
| 0b110 | 64 |
| 0b111 | 128 |

**Length attribute**

- Xác định số transfer trong burst qua AWLEN và ARLEN.
- Tổng số byte = Size × Length
- Length là giá trị thực tế cộng 1 so với giá trị mã hóa.

**Maximum number of bytes in a transaction**

- Tối đa **4KB** và không được cross qua 4KB boundary.
- Có thể chọn Max_Transaction_Bytes nhỏ hơn (64, 128, 256…).

**Burst attribute**

- Xác định cách địa chỉ tăng:
    - **FIXED**: địa chỉ không đổi.
    - **INCR**: tăng theo Size.
    - **WRAP**: như INCR nhưng quay vòng khi đến giới hạn.

**Transfer address**

- Địa chỉ giao dịch qua AWADDR, ARADDR.
- Độ rộng qua ADDR_WIDTH (ví dụ 32-bit, 64-bit).
- Quy tắc mapping nếu manager/subordinate khác size.

**Transaction equations**

Công thức tính:

Lower_Byte_Lane = Addr mod Data_Bytes

Upper_Byte_Lane = Lower_Byte_Lane + Size - 1

- Với INCR và WRAP: tính thêm Container_Size, Container_Upper.

**Pseudocode description**

Giả lập thuật toán tính toán địa chỉ và byte lane từng transfer.

**Regular transactions**

Điều kiện:

- Length: 1, 2, 4, 8, 16
- Size = Data channel width (nếu Length > 1)\
- Burst: INCR, WRAP
- Địa chỉ phải aligned theo quy định.

## **4.2 Write and read data**

**Write strobes**

- WSTRB xác định byte hợp lệ trong WDATA
- 1 bit strobe cho mỗi 8 bit data.

**Narrow transfers**

- Transfer nhỏ hơn data channel width.
- INCR và WRAP: sử dụng các byte lane khác nhau.
- FIXED: giữ nguyên byte lane.

**Byte invariance**

- Duy trì mapping byte-invariant, cho phép coexist big-endian và little-endian trên cùng hệ thống.
- Ví dụ và mô tả cụ thể qua Figure A4.3, A4.4, A4.5.

**Unaligned transfers**

- Cho phép address không aligned.
- Manager dùng low-order address line hoặc byte lane strobe để báo offset.
- Subordinate không cần xử lý đặc biệt.

## **4.3 Transaction response**

**Write response**

- Phản hồi bằng BRESP và BCOMP.
- Giá trị:

| **BRESP** | **Ý nghĩa** |
| --- | --- |
| 0b000 | OKAY |
| 0b001 | EXOKAY |
| 0b010 | SLVERR |
| 0b011 | DECERR |
| 0b100 | DEFER |
| 0b101 | TRANSFAULT |
| 0b111 | UNSUPPORTED |

**Read response**

- Mỗi transfer đi kèm RRESP.
- Giá trị:

| **RRESP** | **Ý nghĩa** |
| --- | --- |
| 0b000 | OKAY |
| 0b001 | EXOKAY |
| 0b010 | SLVERR |
| 0b011 | DECERR |
| 0b100 | PREFETCHED |
| 0b101 | TRANSFAULT |
| 0b110 | OKAYDIRTY |

**Subordinate Busy indicator**

- BBUSY báo busy.
- Cho phép báo đang bận khi transaction chưa thể xử lý.