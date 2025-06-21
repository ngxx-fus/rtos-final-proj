# AHB-(Advanced High-performance Bus)

# **1. Tổng quan về AMBA và vai trò của AHB**

Trong số các chuẩn bus thuộc AMBA, **Advanced High-performance Bus (AHB)** là một kiến trúc bus hiệu năng cao, hỗ trợ tần số xung nhịp lớn, truyền dữ liệu tốc độ cao và hoạt động trong các thiết kế có nhiều thiết bị master/slave.

AHB được xem là **xương sống dữ liệu** (data backbone), đóng vai trò truyền dữ liệu chính trong các hệ thống SoC hiện đại, cho phép kết nối linh hoạt giữa bộ xử lý (CPU), bộ nhớ, DMA và các thiết bị ngoại vi tốc độ cao trong hệ thống. Mục tiêu chính của AHB là cung cấp một giao thức bus độc lập công nghệ, hỗ trợ đa master, burst transfer, split transfer và có thể cấu hình ở nhiều độ rộng dữ liệu khác nhau (32, 64, 128 bit…) để đáp ứng yêu cầu về hiệu năng cao và khả năng mở rộng.

# **2. Lý thuyết về AHB**

## **2.1.Mục tiêu thiết kế**

AHB được thiết kế nhằm:

- Tăng khả năng tái sử dụng và di động
- Hỗ trợ hiệu năng cao
- Đơn giản hóa thiết kế và kiểm thử

## **2.2.Đặc tính chính của AHB**

- **Burst Transfers**: Cho phép truyền nhiều giao dịch liên tiếp với địa chỉ tăng hoặc địa chỉ “wrap” (quay vòng) khi vượt quá giới hạn vùng nhớ (ví dụ: WRAP4, WRAP8, WRAP16).
- **Split & Retry Transfers**: Slave có thể trả về mã SPLIT hoặc RETRY để cho phép bus “mở” cho các master khác khi slave chưa sẵn sàng (ví dụ: truy cập bộ nhớ ngoài chậm).
- **Lock Transfers**: Hỗ trợ tính nguyên tử (atomicity) cho các nhóm giao dịch (locked sequence), đảm bảo không xảy ra phân mảnh giữa các master.
- **Hỗ trợ đa master**: Sử dụng các tín hiệu HBUSREQx (request), HGRANTx (grant), HMASTER (chỉ báo master hiện hành) để điều phối giữa các master.

## **2.3.Các thành phần chính của AHB**

### **2.3.1. Master (Thiết bị điều khiển giao dịch)**

**Chức năng:** Là thành phần khởi tạo các giao dịch trên bus, bao gồm yêu cầu đọc hoặc ghi dữ liệu đến các thiết bị khác.

**Cách hoạt động:**

- Master gửi yêu cầu đến hệ thống khi muốn truy cập dữ liệu.
- Trước khi có thể gửi, master phải xin quyền truy cập bus và chỉ thực hiện khi được hệ thống cấp quyền.
- Khi được phép, master cung cấp thông tin như địa chỉ cần truy cập, loại giao dịch (đọc/ghi), độ lớn dữ liệu và trạng thái khóa (nếu cần thực hiện giao dịch liên tiếp không bị gián đoạn).

### **2.3.2. Slave (Thiết bị phản hồi yêu cầu)**

**Chức năng:** Là thành phần tiếp nhận và xử lý các yêu cầu từ master. Slave có thể là vùng nhớ như RAM, ROM hoặc ngoại vi tốc độ cao.

**Cách hoạt động:**

- Khi hệ thống xác định rằng một slave nào đó cần được truy cập, slave này sẽ nhận yêu cầu từ master.
- Dựa vào thông tin nhận được, slave sẽ thực hiện đọc dữ liệu từ bộ nhớ hoặc ghi dữ liệu vào đúng vị trí.
- Sau khi xử lý xong, slave gửi lại phản hồi về trạng thái hoàn tất và, nếu là giao dịch đọc, gửi cả dữ liệu về cho master.
- Nếu slave cần thêm thời gian để xử lý, nó có thể yêu cầu master chờ hoặc đề nghị thực hiện lại giao dịch sau.

### **2.3.3. Decoder (Bộ giải mã địa chỉ)**

**Chức năng:** Phân tích địa chỉ được gửi từ master để xác định slave nào là đích đến của giao dịch.

**Cách hoạt động:**

- Khi master khởi tạo giao dịch, decoder kiểm tra địa chỉ và kích hoạt đúng slave tương ứng với vùng nhớ đó.
- Mỗi slave có một “đường chọn” riêng, decoder đảm bảo chỉ một slave được kích hoạt tại một thời điểm.

### **2.3.4. Multiplexer (Bộ chọn dữ liệu phản hồi)**

**Chức năng:** Đóng vai trò là cầu nối hai chiều trong pha dữ liệu, chuyển thông tin phản hồi từ slave trở lại master.

**Cách hoạt động:**

- Khi có nhiều slave trong hệ thống, multiplexer chọn đúng nguồn dữ liệu và tín hiệu phản hồi từ slave đang được chọn.
- Nó đảm bảo master nhận đúng dữ liệu từ slave tương ứng với yêu cầu trước đó, đồng thời nhận được trạng thái phản hồi (thành công, lỗi, chờ,...).

### **2.3.5. Arbiter (Bộ điều phối quyền truy cập bus)**

**Chức năng:** Quản lý việc cấp quyền sử dụng bus khi có nhiều master cùng yêu cầu truy cập cùng lúc.

**Cách hoạt động:**

- Arbiter theo dõi yêu cầu từ các master và quyết định ai được cấp quyền truy cập dựa trên chính sách ưu tiên hoặc luân phiên công bằng.
- Nếu master cần giữ quyền truy cập liên tục cho một chuỗi giao dịch không bị gián đoạn, arbiter cũng phải xem xét điều này.
- Khi một slave trước đó từ chối phục vụ vì quá tải (sử dụng cơ chế SPLIT), arbiter chỉ cấp quyền lại cho master đó khi nhận được tín hiệu từ slave cho biết đã sẵn sàng trở lại.

## **2.4.Mô hình hệ thống sử dụng AHB**

### **2.4.1. Mô hình cơ bản**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcnWVUjVWfIhiTHAiGWKVVoKiTGSvXsYc6ZVOn6uRnH4f4JT1O5Puxh3va_oPHrZjeDfuB-xJfOkdMcEwbzzB_EVYF4X5l8a0DrcfZCLt2aUVINJLGEBM2lP38gEVAgiU3fBuBP?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 1. Mô hình khối AHB cơ bản với 1 master, 3 slave, decoder và multiplexor

---

Mô hình AHB cơ bản gồm:

- Một Manager phát tín hiệu địa chỉ và điều khiển.
- Nhiều Subordinate nhận/gửi dữ liệu tương ứng với địa chỉ được chọn.
- Bộ Decoder và Multiplexor đảm nhận điều hướng và chọn slave đúng.
- Nếu có nhiều master, sẽ có thêm Arbiter để phân quyền sử dụng bus.

### **2.4.2. Mô hình nâng cao - Kết hợp AHB và APB**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcUSrkBBptltMAbfPUGdjoclVuZ0B-eq89LWPIIj4BqjkvwITqN1pTf2Yww58Wvnh0abYwRPZ9qELpmW9ZI9eFdwf1AC3leX5_EIJCjlcgap_nTFbKbivxY-C1s24ACCVOq0S3O?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 2. Mô hình khối AHB nâng cao kết hợp với APB

---

**Giải thích:** Do các ngoại vi đơn giản không cần tốc độ cao, chúng thường không kết nối trực tiếp với AHB mà thông qua AHB-to-APB Bridge. APB hoạt động chậm hơn nhưng tiêu tốn ít năng lượng và đơn giản hóa thiết kế. Trong ví dụ trên:

- **AHB (Advanced High-performance Bus):** Là bus chính, tốc độ cao, kết nối giữa các thành phần hiệu năng cao như CPU ARM, bộ nhớ RAM, DMA, và giao diện bộ nhớ ngoài.
- **Bridge (Cầu nối AHB-APB):** Là bộ chuyển tiếp giữa AHB và APB. Cho phép AHB giao tiếp với các ngoại vi chậm hơn qua APB.
- **APB (Advanced Peripheral Bus):** Là bus phụ, tốc độ chậm hơn, tiêu thụ ít điện năng, dùng để kết nối các ngoại vi đơn giản như UART, Timer, Keypad, PIO.

# **3. Nguyên lý hoạt động của AHB**

## **3.1.Cấu trúc một chu trình truyền dữ liệu AHB (Basic Transfer)**

Mỗi lần Master (ví dụ CPU hoặc DMA) muốn đọc hoặc ghi dữ liệu trên bus AHB, quy trình diễn ra theo **hai giai đoạn**:

- **Giai đoạn “Đưa thông tin yêu cầu”: Address Phase** (Chu kỳ địa chỉ)
- **Giai đoạn “Trao đổi Dữ liệu”: Data Phase** (Chu kỳ dữ liệu)

### **3.1.1.Address Phase (Chu kỳ địa chỉ)**

**Ý tưởng cơ bản:** Master cần thông báo cho hệ thống biết:

- Muốn đọc hay muốn ghi
- Dữ liệu thuộc vùng nhớ nào
- Kích cỡ của dữ liệu (1 byte, 2 byte, 4 byte hay lớn hơn)
- Nếu thực hiện nhiều mục liên tiếp (như đọc/gắn dữ liệu theo khối), Master cũng sẽ cho biết “tôi sẽ tiếp tục đọc/gửi bao nhiêu đoạn liền kề”.

**Cách hoạt động:**

Ngay khi đến cạnh lên của xung đồng hồ chung, Master đồng thời xuất địa chỉ và thông tin điều khiển lên bus.

Bộ giải mã (Decoder) tại thời điểm này kiểm tra vùng địa chỉ nhận được so với bản đồ các thiết bị trên hệ thống, như RAM, bộ nhớ cache, ngoại vi hoặc các nhánh bus.

Khi bộ giải mã xác định được thiết bị phù hợp, nó sẽ gửi tín hiệu chọn (select) đến Slave tương ứng, báo hiệu cho Slave biết cần khởi động quá trình chuẩn bị cho giao dịch tiếp theo.

**Ý nghĩa và vai trò:** Master có nhiệm vụ xác định chính xác vị trí và khối lượng dữ liệu cần đọc hoặc ghi. Bộ giải mã cho phép hệ thống dễ dàng mở rộng: khi thêm một khối nhớ hoặc ngoại vi mới, chỉ cần cập nhật vùng địa chỉ trong bảng giải mã mà không cần thay đổi logic của Master hay các Slave hiện có. Nhờ tín hiệu chọn từ bộ giải mã, Slave tương ứng biết ngay vị trí dữ liệu cần truy xuất và bắt đầu chuẩn bị nội dung (ví dụ: truy cập bộ nhớ, đọc từ chip ngoài,...).

### **3.1.2.Data Phase (Chu kỳ dữ liệu)**

**Ý tưởng cơ bản:** Sau khi Slave nhận được yêu cầu, Slave và Master sẽ chia sẻ dữ liệu qua bus, theo cách sau:

-**Nếu Master yêu cầu ghi:**

- Master gửi giá trị cần ghi lên “đường dữ liệu chung”.
- Slave nhận giá trị đó, ghi vào bộ nhớ hoặc thiết bị cần ghi.
- Khi ghi xong, Slave gửi tín hiệu xác nhận để Master biết đã ghi xong.
- **Nếu Master yêu cầu đọc:**
- Slave lấy sẵn dữ liệu từ bộ nhớ/thiết bị rồi gửi lên “đường dữ liệu chung”.
- Master nhận được tín hiệu, lấy dữ liệu đó về.
- Slave gửi tín hiệu xác nhận đã cung cấp dữ liệu thành công.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdYE6avtCqr8NAFg959FQewrQOBNzvjgn-0MHvfaW-FLOh7cju8XaRGB93okNYnWY35Q0cAmFsbD4OTikeIaI8uuEwzyqx-5GUMrLzENcOYKlbqf1SMwfsd4ECC9CZOPj6luXzw?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 2. Ví dụ giao dịch đơn giản “Simple Transfer” (Không có Wait State)

---

**Giải thích:** Trong ví dụ này, Master thực hiện một giao dịch Read đơn lẻ như sau:

1. Chu kỳ 1 (Address Phase): Master đưa thông tin yêu cầu (địa chỉ và loại giao dịch) lên bus, và Slave ngay lập tức “bắt” được yêu cầu.
2. Chu kỳ 2 (Data Phase): Slave trả dữ liệu về cho Master mà không chèn chu kỳ chờ nào, nghĩa là dữ liệu đã sẵn sàng ngay trong nhịp tiếp theo.
3. Chu kỳ 3: Master sample nhận tín hiệu phản hồi (HRESP = OKAY) vào ngay cạnh lên thứ ba xác nhận giao dịch thành công.

Vì Slave đáp ứng ngay mà không cần thêm thời gian chờ, toàn bộ giao dịch hoàn thành chỉ trong hai chu kỳ bus (Address Phase + Data Phase), với bước xác nhận vào nhịp thứ ba.

## **3.2.Các loại truyền dữ liệu (Transfer Types)**

Trong thực tế, một Master không phải lúc nào cũng khởi tạo ngay một giao dịch mới. Có những lúc nó chỉ đang tạm dừng, có lúc bắt đầu một giao dịch hoàn toàn mới, và cũng có khi đang tiếp tục một chuỗi truyền dữ liệu liên tục đã được thiết lập từ trước. Những tình huống này được phân loại thành các trạng thái giao dịch khác nhau trên bus AHB:

1. **IDLE (00) – Trạng thái nghỉ**

**Ý nghĩa: Không truyền**

**Mô tả:** Master giữ quyền truy cập bus nhưng không có yêu cầu nào cần gửi. Đây là trạng thái không thực hiện truyền → Slave sẽ phản hồi ngay là sẵn sàng (OKAY), không cần chèn chu kỳ chờ.

1. **BUSY (01) – Trạng thái bận**

**Ý nghĩa: Chờ**

**Mô tả:** Master tạm dừng giữa hai beat trong một burst, có thể vì đang xử lý nội bộ hoặc chuẩn bị dữ liệu tiếp theo → Vẫn giữ bus nhưng chưa phát giao dịch mới. Slave vẫn phản hồi OKAY.

1. **NONSEQ (10) – Giao dịch không liên tiếp**

**Ý nghĩa: Giao dịch mới**

**Mô tả:** Master bắt đầu một giao dịch đơn lẻ hoặc bắt đầu một burst hoàn toàn mới. Địa chỉ truy cập không liên quan đến giao dịch trước đó → Đây là trạng thái được dùng cho giao dịch đơn hoặc là beat đầu tiên của một burst.

1. **SEQ (11) – Giao dịch liên tiếp (sequential)**

**Ý nghĩa: Tiếp nối**

**Mô tả:**

Tiếp nối giao dịch trong burst, địa chỉ = địa chỉ trước đó + kích thước (HSIZE), hoặc wrap (nếu WRAP burst). Control = y hệt giao dịch trước

Master đang trong một burst nhiều beat, tiếp tục truy xuất dữ liệu ở các địa chỉ liên tiếp → Mỗi beat sẽ tự động tăng địa chỉ dựa trên kích thước dữ liệu, hoặc quay vòng nếu đó là burst dạng wrap.

**Ví dụ chuỗi giao dịch với IDLE / NONSEQ / BUSY / SEQ**

Giả sử một Master được cấp quyền truy cập bus trong khi chưa có dữ liệu cần truyền:

- **Chu kỳ 1:** Master ở trạng thái IDLE, giữ bus nhưng không gửi yêu cầu. Slave trả OKAY.
- **Chu kỳ 2:** Master phát sinh yêu cầu đọc dữ liệu tại địa chỉ 0x1000 → chuyển sang NONSEQ, bắt đầu một giao dịch mới.
- **Chu kỳ 3:** Slave đã nhận yêu cầu, nhưng Master cần thêm thời gian xử lý nên chuyển sang BUSY. Giao dịch tạm ngưng, Slave có thể chèn một chu kỳ chờ.
- **Chu kỳ 4:** Slave sẵn sàng, truyền dữ liệu cho beat đầu tiên. Master quyết định tiếp tục đọc dữ liệu trong cùng burst, nên chuyển sang SEQ → địa chỉ tự tăng lên 0x1004.
- **Chu kỳ 5:** Slave trả dữ liệu cho beat thứ hai. Master tiếp tục truyền các beat còn lại trong burst (nếu có).

Qua các trạng thái này, AHB giúp Master kiểm soát chặt chẽ luồng giao dịch, đồng thời đảm bảo hiệu quả và độ ổn định khi truyền dữ liệu liên tục hoặc ngắt quãng.

### **3.2.1. Burst Transfers (Truyền dữ liệu theo chuỗi)**

**Khái niệm Burst:**

- **Burst** = một loạt các beats (đơn vị nhỏ nhất là “beat” – tương đương 1 chunk dữ liệu, ví dụ 4 byte)
- Thay vì mỗi beat phải có riêng Address Phase, ta phát một Address Phase cho beat đầu, sau đó cứ n lần beat tiếp theo sẽ đi vào Data Phase liền, và **địa chỉ** sẽ được hệ thống **tự động tính** (chẳng hạn: beat 1 = 0x1000, beat 2 = 0x1004, beat 3 = 0x1008, v.v.)
- Giúp tiết kiệm 1 Address Phase cho mỗi beat, tăng tốc độ rất nhiều khi sao chép/ghi block dữ liệu lớn.

**Các kiểu Burst:**

1. **SINGLE (length=1)**
    
    Tương đương với giao dịch đơn lẻ. Chỉ có một beat, sau đó kết thúc.
    
2. **INCR (incrementing, độ dài không cố định)**
- Master không cần chỉ độ dài trước, cứ tiếp tục gửi beat; địa chỉ mỗi beat sẽ tăng lên kích thước cụ thể (ví dụ: +4 byte cho mỗi word).
- Chỉ dừng lại khi Master không còn beat nào. Chú ý: không được vượt quá 1 KB boundary.
1. **INCR4, INCR8, INCR16**
- Cụ thể yêu cầu đếm trước (ví dụ, INCR4 nghĩa là “tôi sẽ có 4 beat liên tiếp, địa chỉ tự động tăng”).
- Khi beat cuối (4) hoàn thành, Burst tự động kết thúc – không cần Master phải làm gì thêm.
1. **WRAP4, WRAP8, WRAP16**
- Tương tự INCR4/8/16, nhưng nếu **địa chỉ của beat** nào đó vượt quá giới hạn (ví dụ: với WRAP4 × word → wrap tại 16 byte boundary), nó sẽ “quay trở lại” địa chỉ ban đầu hoặc về một vùng xác định, rồi tiếp tục.
- Ví dụ WRAP4 với word (4 byte) bắt đầu tại 0x38:
- Beat 1: 0x38
- Beat 2: 0x3C
- Beat 3: (nếu 0x40 > 0x3F, wrap về 0x30) = 0x30
- Beat 4: 0x34 (0x30 + 4)
- Kết thúc, không có beat 5.

Thay vì chỉ truyền một ô nhớ duy nhất, AHB cho phép Master gửi yêu cầu xử lý nhiều ô nhớ liên tiếp chỉ bằng một lần gọi. Khi đó, AHB sẽ tự động tăng địa chỉ cho mỗi bước hoặc “quay vòng” trong một vùng cố định nếu chọn chế độ wrap.

- **Giao dịch đơn (single)**: Muốn đọc/ghi chỉ một ô nhớ → Master gửi yêu cầu, Slave trả hoặc nhận 1 đơn vị dữ liệu, xong.
- **Burst (liên tục):**

Master khởi đầu một burst bằng cách cùng lúc phát địa chỉ ô đầu tiên và thông báo “tôi cần N lượt dữ liệu liên tiếp.” (N có thể là 4, 8, 16 hoặc không giới hạn trước).

Slave nhận yêu cầu và trả dữ liệu cho ô đầu; các lượt kế tiếp được thực hiện tự động với địa chỉ tiếp theo (đã cộng thêm kích thước mỗi phần).

Đặc biệt trong các trường hợp wrap (Wrap là cơ chế trong truyền dữ liệu burst mà khi địa chỉ vượt qua ranh giới khối cố định, nó sẽ quay lại địa chỉ đầu khối thay vì tiếp tục tăng), khi địa chỉ vượt quá ngưỡng đã xác định trước (ví dụ 16 byte), nó sẽ quay ngược về địa chỉ đầu để tiếp tục.

Quá trình kết thúc sau khi toàn bộ N lượt đã hoàn thành. Slave trả dữ liệu mà không cần Master gửi lại địa chỉ cho từng lượt, tiết kiệm rất nhiều thời gian bus.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe5Y_y4s2uXM4JqEjuWD8507o8W1FeNDzjMpqiI2ADxZcst-yqqNrcsS9tnP4O5lp47xOvyBF9fb_fpsUb2RCnx042dsy9Dh1_ssCrdPOHxhJIAXiHUeAifkOSgoiAiV3aHQ4Z-NA?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 3. Ví dụ về trường hợp “wrap”.

---

**Giải thích:** Ví dụ trên minh họa cách truyền dữ liệu theo burst 8 lần (8 beat), kiểu WRAP, tức là địa chỉ sẽ tự động quay vòng trong một khối 8 × kích thước dữ liệu. Lúc địa chỉ vượt quá một ngưỡng ranh giới cục bộ (boundary) do n × kích thước beat xác định, chuỗi sẽ quay vòng (wrap) về đầu vùng đó thay vì tiếp tục gia tăng. AHB hỗ trợ WRAP4, WRAP8, WRAP16 (tương ứng 4, 8 hoặc 16 beat). Giới hạn địa chỉ wrap = n × kích thước mỗi beat.

Giả định trong hình:

- Mỗi beat là một word = 4 byte
- Tổng burst = 8 beat × 4 byte = 32 byte
- Khối địa chỉ liên quan nằm trong ranh giới 32 byte, ví dụ: 0x30 đến 0x4F
- Điểm bắt đầu không phải đầu khối (ví dụ bắt đầu tại 0x38).

Trình tự địa chỉ của 8 beat như sau: Beat 1 → 0x38, Beat 2 → 0x3C, Beat 3 → 0x30 (wrap về đầu khối), Beat 4 → 0x34, Beat 5 → 0x38, Beat 6 → 0x3C, Beat 7 → 0x30, Beat 8 → 0x34

Khi địa chỉ vượt qua giới hạn khối 32 byte (ví dụ: 0x3C + 4 = 0x40), nó sẽ “wrap” về địa chỉ thấp nhất trong khối (0x30) và tiếp tục quay vòng.

**Ý nghĩa:**

- WRAP8 đảm bảo rằng 8 beat luôn nằm trong phạm vi cố định 32 byte.
- Địa chỉ không tăng mãi mà tuần hoàn trong vùng nhớ giới hạn.
- Hữu ích cho các buffer dạng vòng (circular buffer), như bộ đệm truyền dữ liệu liên tục.

| **HBURST[2:0]** | **Tên Burst** | **Mô tả** |
| --- | --- | --- |
| 000 | **SINGLE** | Giao dịch đơn lẻ – chỉ truyền đúng 1 đơn vị dữ liệu (1 beat). Không phải burst. |
| 001 | **INCR** | Burst tăng địa chỉ, không xác định độ dài. Master sẽ truyền nhiều dữ liệu liên tiếp cho đến khi dừng, miễn là không vượt ranh giới 1KB. |
| 010 | **WRAP4** | Burst 4 beat (4 lần truyền), kiểu cuộn vòng. Khi địa chỉ đến ranh giới 16 byte thì quay lại đầu vùng. Dùng cho truy xuất bộ đệm vòng (circular buffer). |
| 011 | **INCR4** | Burst 4 beat, tăng địa chỉ đều đặn. Truyền 4 đơn vị dữ liệu với địa chỉ kế tiếp tăng dần (ví dụ: 0x1000 → 0x1004 → 0x1008 → 0x100C). |
| 100 | **WRAP8** | Burst 8 beat, kiểu cuộn vòng. Khi vượt quá vùng 32 byte thì quay về đầu vùng đó. Phù hợp truyền vòng trong bộ đệm dài hơn. |
| 101 | **INCR8** | Burst 8 beat, địa chỉ tăng đều đặn qua 8 lần truyền liên tiếp. |
| 110 | **WRAP16** | Burst 16 beat, cuộn vòng trong vùng 64 byte (16 × kích thước beat). Sau khi đạt giới hạn, địa chỉ quay lại đầu vùng. |
| 111 | **INCR16** | Burst 16 beat, địa chỉ tăng đều đặn suốt 16 lần truyền. Rất phù hợp để truyền các khối dữ liệu lớn, liên tiếp trong RAM. |

### **3.2.2.Pipelining (Xếp Hàng Yêu Cầu Liên Tiếp)**

Để tận dụng tối đa mỗi nhịp xung, AHB hỗ trợ Pipelining cho phép Master khởi tạo yêu cầu của giao dịch kế tiếp ngay trong lúc Slave vẫn đang trả dữ liệu cho giao dịch hiện tại. Cách thức hoạt động như sau:

- Trong nhịp đầu tiên, Master đặt yêu cầu đọc (hoặc ghi) cho địa chỉ A lên bus. Slave tương ứng nhận được yêu cầu và bắt đầu chuẩn bị dữ liệu.
- Trong ngay nhịp sau đó, Slave của giao dịch A trả dữ liệu A cho Master, đồng thời Master có thể phát yêu cầu mới cho địa chỉ B. Nhờ vậy, Slave B đã “bắt sóng” yêu cầu ngay từ nhịp này.
- Ở nhịp thứ ba, Slave B trả dữ liệu cho yêu cầu mới; Master lần lượt nhận dữ liệu B.

Như vậy, hai giao dịch liên tiếp chỉ cần ba nhịp xung thay vì bốn nhịp nếu mỗi giao dịch chờ hoàn toàn xong mới bắt đầu.

### **3.2.3. Khi Slave Quá Bận: Thử lại (Retry) và Tách Hàng (Split)**

Trong quá trình xử lý, Slave không thể hoàn thành giao dịch trong một khoảng chờ tạm thời (chèn tối đa wait state) hoặc muốn nhường bus cho Master khác. Khi đó, AHB cho phép hai cách để Slave “báo vắng mặt” tạm thời:

1. **Retry (Thử lại)**: Slave trả tín hiệu báo “Tôi chưa xong, anh thử lại sau.” Master lập tức hủy nốt lượt hiện tại và sẽ gửi lại yêu cầu cho chính lượt đó ở nhịp kế tiếp. Ưu điểm là Master giữ nguyên vị trí ưu tiên trên bus mà không cần xin cấp quyền lại. Nhược điểm là Master phải chờ thêm và toàn bộ giao dịch bị trì hoãn.
2. **Split (Tách hàng)**: Slave trả tín hiệu “Tôi quá bận, anh nhường bus cho người khác, tôi sẽ báo lại khi xong.” Master phải xin cấp bus mới lại từ đầu khi có cơ hội (re-arbitrate). Slave, sau khi xử lý xong bên trong, sẽ bật tín hiệu cho arbiter biết “tôi đã sẵn sàng.” Khi nhận được tín hiệu này, arbiter sẽ cấp bus trở lại cho Master ban đầu (nếu vẫn cần). Cơ chế Split giúp bus không bị giữ lâu khi Slave chậm, nhưng đổi lại Master phải chờ lâu hơn vì mất ưu tiên tạm thời.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXextohGLlpzjBCIEuJg3keZBP5yhvwzT9Quz3578dqgij4EOyu2whXs8akdJb1qBADf4e0JnufvOdrm0uKuiI5C59-9OyjLIJMGdMBAIMsRaBc6cYECFFEit_LCAEFMd9JDy7pC?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 4. Ví dụ về giao dịch từ trạng thái IDLE đến NONSEQ nhưng bị wait state.

---

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc0hK98j-yBxz8ptuzJP6y1QSBXHO84OkHuRUifH_L0MoMq-3WFiYgGpjAmUe6Qkr1I16uTzfanvt8eKZr91ml8LZVoO_xJjE2QhfR5SCpNXT3zvayNrlDSUVRBtXPD8SepdsJl?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 5. Ví dụ về quá trình phản hồi dạng Retry.

---

**Giải thích:**

**Hình 5.** Minh họa một giao dịch bắt đầu từ trạng thái IDLE và chuyển sang NONSEQ (giao dịch mới), nhưng bị chèn một chu kỳ chờ (wait state) do Slave chưa sẵn sàng.

1. Chu kỳ 1: Master ở trạng thái IDLE (không gửi gì).
2. Chu kỳ 2: Master gửi NONSEQ (bắt đầu giao dịch mới).
3. Chu kỳ 3: Slave chưa sẵn sàng → chèn wait state (HREADY = LOW).
4. Chu kỳ 4: Slave sẵn sàng → trả dữ liệu, giao dịch hoàn tất.

Ý nghĩa: Thể hiện việc Master phải giữ nguyên yêu cầu khi gặp wait state. Giao dịch không được tiếp tục cho đến khi HREADY lên HIGH.

**Hình 6.** Minh họa quy trình phản hồi dạng RETRY từ Slave, khi Slave chưa thể hoàn thành giao dịch và yêu cầu Master gửi lại sau.

1. Chu kỳ 1: Master gửi NONSEQ (bắt đầu giao dịch).
2. Chu kỳ 2: Slave phản hồi tạm thời bằng HRESP = OKAY, HREADY = LOW (chu kỳ chờ đầu tiên).
3. Chu kỳ 3: Slave trả HRESP = RETRY, HREADY = HIGH → báo Master “hãy thử lại sau.”
4. Master phải hủy các beat sau và sẽ gửi lại giao dịch sau khi được arbiter cấp lại quyền bus.

Ý nghĩa: Cho thấy cơ chế Slave chủ động trì hoãn xử lý bằng RETRY, giúp giữ bus không bị chiếm lâu nếu chưa xử lý kịp. Master sẽ cần gửi lại yêu cầu sau.

### **3.2.4.Wait state (Chu kỳ chờ):**

Khi Slave cần thêm thời gian xử lý (ví dụ: phép tính phức tạp, truy cập bộ nhớ chậm, chờ ngoại vi), nó có thể tạm “chặn” giao dịch bằng cách đưa vào các chu kỳ chờ (wait states). Trong thời gian này: Slave chủ động báo chưa sẵn sàng để tránh tình trạng treo bus và Master nhận biết và tạm ngừng truyền/nhận dữ liệu, đợi Slave hoàn tất. Cơ chế này giúp hệ thống duy trì hiệu quả hoạt động, tránh đóng băng bus dù Slave có tốc độ chậm hơn.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdiwN4BfWZCv0wX7R15mmFYuFA_HvGwSoe6DE5ZZdgpH3iiurdYpL2Tfedk7AmOGop7S1D_jHgul6OkRhgvmmYEeq6SCTLYc90RgUzaHrBqQNniEhWc8W6qY9KGVSZcIB6VnSFq?key=L_Gjf62Qg3JKXWpL6xNacw)

---

Hình 6. Ví dụ giao dịch có Chu kỳ chờ (Wait State).

---

**Giải thích:**

Trong ví dụ này, Slave chưa sẵn sàng trả dữ liệu ngay nên chèn thêm một chu kỳ chờ (Wait state) trước khi hoàn tất giao dịch. Các bước diễn ra như sau:

1. Chu kỳ 1 (Address Phase):
- Master đặt yêu cầu đọc (hoặc ghi) lên bus cùng thông tin địa chỉ.
- Slave ngay lập tức nhận được yêu cầu và bắt đầu chuẩn bị nội dung dữ liệu.
1. Chu kỳ 2 (Data Phase – Wait State):
- Slave chưa kịp hoàn tất công việc nội bộ nên chưa thể trả dữ liệu.
- Thay vì đưa dữ liệu, Slave giữ bus thêm một chu kỳ và báo “chưa sẵn sàng” (wait state). Trong nhịp này, Master không nhận dữ liệu và không phát yêu cầu mới.
1. Chu kỳ 3 (Data Phase – Dữ liệu trả về):
- Slave đã hoàn tất xử lý, đẩy dữ liệu lên bus.
- Master đọc dữ liệu trong chu kỳ này.
1. Chu kỳ 4 (Phản hồi xác nhận):
- Slave phát tín hiệu xác nhận “OKAY” (hoặc “ERROR”) để cho Master biết kết quả giao dịch.
- Master, sau nhịp này, ghi nhận giao dịch đã hoàn thành và chuyển đổi sang trạng thái IDLE hoặc tiến hành yêu cầu tiếp theo.

Toàn bộ quy trình từ khi Master đặt địa chỉ đến khi nhận dữ liệu kéo dài ba chu kỳ bus (Address + wait + Data), và phải chờ thêm một chu kỳ nữa để lấy phản hồi.

### **3.2.5. Locked Sequence (Giữ Bus Nguyên Tử)**

Đôi khi Master cần thực hiện nhiều thao tác liên tiếp mà không muốn bất kỳ Master khác chen ngang (ví dụ thao tác đọc–xử lý–ghi ở những ô nhớ “kề nhau” nhằm đảm bảo tính nhất quán). AHB hỗ trợ điều này bằng cơ chế “locked sequence”:

- Trước khi bắt đầu chuỗi thao tác, Master phát tín hiệu khóa bus (lock).
- Arbiter sau khi cấp bus cho Master đó sẽ giữ nguyên quyền cho đến khi Master hoàn tất toàn bộ chuỗi, không cho phép Master khác tranh chấp.
- Master thực hiện một loạt bước (có thể đọc, ghi, hoặc kết hợp), rồi khi đã xong, Master hạ tín hiệu lock. Lúc này arbiter mới cho phép Master khác được cấp bus tiếp.

## **3.3. Tổng kết luồng hoạt động AHB**

1. **Master khởi xướng:** Thông báo nhu cầu đọc hoặc ghi, xác định vị trí và kích thước. Nếu là burst, Master cho biết số lượt cần truyền liên tiếp.
2. **Decoder xác định Slave:** Dựa vào vùng địa chỉ, hệ thống phân công Slave phù hợp và chỉ định Slave đó thực hiện giao dịch.
3. **Slave phản hồi:** Nếu có thể hoàn thành ngay, Slave trả dữ liệu hoặc ghi xong. Nếu chưa sẵn sàng, Slave chèn wait state. Khi quá bận, Slave có thể trả Retry hoặc Split. Khi Slave sẵn sàng, nó báo tín hiệu để bus tiếp tục.
4. **Burst processing:** Sau beat đầu (NONSEQUENTIAL), các lượt kế tiếp thường ở trạng thái SEQUENTIAL, với địa chỉ tự động tăng hoặc wrap. Giúp tiết kiệm bước đưa địa chỉ mỗi lần.
5. **Trong đa-Master:** Mỗi Master phải xin cấp bus từ arbiter, arbiter ưu tiên theo chính sách (tôn trọng lock sequence). Khi gặp Retry hoặc Split, arbiter tiếp tục phần phân phối bus cho các Master còn lại, đảm bảo hiệu suất và công bằng.

Nhờ những cơ chế này, AHB vừa đạt được hiệu năng cao nhờ pipelining và burst, vừa giữ được tính linh hoạt, ổn định khi Slave chậm hoặc cần bảo toàn tính nguyên tử.

# **4. Danh sách tín hiệu**

## **4.1. Tín hiệu Global (Common to Master & Slave)**

| Tín hiệu | Mô tả |
| --- | --- |
| **HCLK** | Xung đồng hồ; mọi tín hiệu pipelined đều lấy cạnh lên (rising edge). |
| **HRESETn** | Reset hệ thống, active LOW. |

## **4.2. Tín hiệu Address & Control (Do Master drive, Slave sample)**

| Tín hiệu | Mô tả |
| --- | --- |
| **HADDR[31:0]** | Bus địa chỉ 32-bit. |
| **HTRANS[1:0]** | Loại giao dịch (IDLE, BUSY, NONSEQ, SEQ). |
| **HWRITE** | Ghi (1) hay đọc (0). |
| **HSIZE[2:0]** | Kích thước (000=byte, 001=halfword, 010=word, 011..111=các kích thước 64/128… bits). |
| **HBURST[2:0]** | Burst type (SINGLE, INCR, WRAP4, INCR4, WRAP8, INCR8, WRAP16, INCR16). |
| **HPROT[3:0]** | Thông tin bảo vệ (fetch/data, privileged/user, bufferable, cacheable).
 |
| **HMASTLOCK** | Locked sequence (1 = giữ bus cho đến khi deassert). |

## **4.3. Tín hiệu dữ liệu**

| Tín hiệu | Mô tả |
| --- | --- |
| **HWDATA[31:0]** | Dữ liệu ghi (Master ➔ Slave). Nếu cần wait state, master giữ dữ liệu ổn định đến khi **HREADY = HIGH**. |
| **HRDATA[31:0]** | LDữ liệu đọc (Slave ➔ Master). Slave chỉ đảm bảo dữ liệu valid trước cạnh lên kết thúc data phase. |

## **4.4. Tín hiệu Phản hồi (Response)**

| Tín hiệu | Mô tả |
| --- | --- |
| **HREADY** | (Master sample) Slave (qua **HREADYOUT**) kéo LOW để chèn wait state, HIGH để cho phép transfer kết thúc. |
| **HRESP[1:0]** | (Slave drive) 00=OKAY, 01=ERROR, 10=RETRY, 11=SPLIT. Khi cần trả ERROR/RETRY/SPLIT, slave phải drive **HRESP = OKAY, HREADY = LOW** ít nhất một chu kỳ, sau đó drive **HRESP = ERROR/RETRY/SPLIT, HREADY = HIGH**. (Two-cycle response). |

## **4.4. Tín hiệu Multi-Master (Arbitration)**

| Tín hiệu | Mô tả |
| --- | --- |
| **HBUSREQx** (x = 0..15) | Master x gửi request cấp bus. |
| **HGRANTx** | Arbiter cấp bus cho master x (HIGH). |
| **HMASTER[3:0]** | Mã số master hiện đang nắm bus (dùng trong split/retry để slave biết cập nhật **HSPLITx**). |
| **HSPLITx[15:0]** | Slave use để thông báo master x có thể hoàn thành khi sử dụng SPLIT. |
| **HMASTLOCK** | Arbiter drive để báo bus hiện đang ở locked sequence. |