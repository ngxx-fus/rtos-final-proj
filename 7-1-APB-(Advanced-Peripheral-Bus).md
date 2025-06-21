# APB-(Advanced Peripheral Bus)

# **Giới thiệu**

- Giao thức APB là một giao diện chi phí thấp, được tối ưu hóa cho mức tiêu thụ điện năng tối thiểu và độ phức tạp giao tiếp thấp. Giao diện APB không sử dụng pipeline và là một giao thức đồng bộ đơn giản. Mỗi lần truyền dữ liệu yêu cầu ít nhất hai chu kỳ xung để hoàn tất.
- Giao diện APB được thiết kế để truy cập các thanh ghi điều khiển có thể lập trình của các thiết bị ngoại vi. Các thiết bị ngoại vi APB thường được kết nối với hệ thống bộ nhớ chính thông qua một cầu nối APB. Ví dụ, một cầu nối từ AXI sang APB có thể được sử dụng để kết nối nhiều thiết bị ngoại vi APB vào một hệ thống bộ nhớ sử dụng AXI.
- Các giao dịch APB được khởi tạo bởi cầu nối APB. Các cầu nối APB còn được gọi là **Requester** (bên yêu cầu). Giao diện của thiết bị ngoại vi phản hồi lại các yêu cầu này. Các thiết bị ngoại vi APB còn được gọi là **Completer** (bên phản hồi). Tài liệu đặc tả này sẽ sử dụng thuật ngữ **Requester** và **Completer**.

# **1. Đặc điểm chính của giao thức AMBA APB**

- Giao tiếp đơn giản, không pipeline: APB là một giao thức đồng bộ và không sử dụng pipeline, nghĩa là tất cả các tín hiệu đều được định thời theo cạnh lên của xung đồng hồ PCLK. Mỗi giao dịch truyền dữ liệu gồm hai pha chính: pha thiết lập (Setup) và pha truy cập (Access). Thiết kế này giúp đơn giản hóa việc kiểm soát tín hiệu cũng như giảm độ phức tạp trong việc lập trình điều khiển.
- **Tối ưu hóa cho tiêu thụ điện năng thấp: Một trong những điểm mạnh đáng chú ý của APB là khả năng tiết kiệm điện năng. Nhờ thiết kế không pipeline và không yêu cầu logic xử lý phức tạp, APB tiêu tốn rất ít năng lượng trong quá trình hoạt động. Điều này đặc biệt phù hợp cho các hệ thống nhúng, nơi việc quản lý năng lượng là yếu tố then chốt.**
- **Phù hợp với ngoại vi tốc độ thấp và chi phí thấp: APB thường được sử dụng để kết nối các thiết bị ngoại vi như UART, SPI, I2C, GPIO, hoặc bộ định thời (timer). Vì những thiết bị này không yêu cầu băng thông cao, nên việc sử dụng APB sẽ giúp giảm chi phí phần cứng và đơn giản hóa thiết kế hệ thống.**
- Cấu trúc truyền dữ liệu hai pha: Mỗi giao dịch trong APB diễn ra theo hai pha:
- Setup Phase: Bộ điều khiển (Requester) thiết lập địa chỉ (PADDR), loại giao dịch (PWRITE), và dữ liệu ghi nếu có (PWDATA).
- Access Phase: Tín hiệu PENABLE được kích hoạt để thực hiện truyền dữ liệu. Giao dịch kết thúc khi thiết bị phản hồi PREADY= 1.

Việc chia tách rõ ràng hai pha này giúp đảm bảo độ tin cậy và dễ dàng mô phỏng hoạt động của bus.

- **Hỗ trợ cả giao dịch đọc và ghi: APB cho phép thực hiện cả giao dịch đọc và ghi thông qua tín hiệu PWRITE. Nếu PWRITE = 1, đây là một giao dịch ghi và dữ liệu sẽ được truyền trên PWDATA. Ngược lại, nếu PWRITE = 0, dữ liệu đọc sẽ được phản hồi trên PRDATA. Độ rộng của các bus dữ liệu này có thể là 8, 16 hoặc 32 bit, và chúng phải có cùng độ rộng trong một giao diện.**
- Khả năng mở rộng tính năng (qua các phiên bản): Theo thời gian, các phiên bản mới hơn của APB (APB3, APB4, APB5) đã được phát triển để mở rộng khả năng sử dụng:
- APB3 bổ sung tín hiệu PREADY và PSLVERR giúp xử lý wait-state và phản hồi lỗi.
- APB4 thêm hỗ trợ bảo vệ truy cập với PPROT và ghi dữ liệu không liên tục với PSTRB.
- APB5 mở rộng thêm hỗ trợ phân vùng bảo mật với PNSE, tín hiệu đánh thức PWAKEUP, tín hiệu người dùng (PAUSER, PRUSER,...) và kiểm tra lỗi giao tiếp bằng parity.
- Giao tiếp thông qua cầu nối (Bridge): Trong kiến trúc SoC, APB không giao tiếp trực tiếp với CPU hay bộ nhớ chính mà được kết nối thông qua một cầu nối APB (APB Bridge), ví dụ cầu nối từ AXI hoặc AHB sang APB. Cầu nối này đóng vai trò là Requester, khởi tạo các giao dịch, trong khi thiết bị ngoại vi đóng vai trò Completer, phản hồi lại yêu cầu tương ứng.
- Không hỗ trợ truyền dữ liệu đồng thời: Do không có các tín hiệu handshake riêng biệt cho đọc và ghi, APB không hỗ trợ truyền dữ liệu đồng thời. Tại một thời điểm chỉ có thể thực hiện một giao dịch đọc hoặc ghi, điều này tuy làm giảm hiệu suất nhưng lại giúp đơn giản hóa logic điều khiển.

# **2. Bảng mô tả tín hiệu APB**

| Tín hiệu | Vai trò | Ghi chú |
| --- | --- | --- |
| PCLK | Clock chính | Toàn bộ tín hiệu đồng bộ với cạnh lên |
| PRESETn | Reset đồng bộ, active-LOW |  |
| PADDR | Địa chỉ | Tối đa 32 bit |
| PWRITE | Đọc/ghi | HIGH: ghi, LOW: đọc |
| PWDATA, PRDATA | Dữ liệu ghi / đọc | 8, 16 hoặc 32 bit |
| PENABLE | Kích hoạt chu kỳ Access | HIGH ở pha Access |
| PSELx | Lựa chọn Completer | Có thể có nhiều tín hiệu PSELx cho các thiết bị khác nhau |
| PREADY | Hoàn thành giao dịch | Cho phép thêm chu kỳ chờ |
| PSLVERR | Lỗi giao dịch | Optional |
| PSTRB | Byte strobe | Ghi từng byte riêng biệt |
| PPROT, PNSE | Bảo vệ và phân vùng địa chỉ Realm | Dùng cho hệ thống bảo mật cao |
| PWAKEUP, PAUSER, PWUSER, PRUSER, PBUSER | Tín hiệu mở rộng cho ứng dụng tùy biến | Chỉ hỗ trợ ở APB5 |
- **Address bus: Một giao diện APB có một bus địa chỉ duy nhất, PADDR, dùng cho cả các giao dịch đọc và ghi. PADDR biểu thị địa chỉ theo byte.**
- **Data buses: Giao thức APB có hai bus dữ liệu độc lập: PRDATA dùng cho dữ liệu đọc và PWDATA dùng cho dữ liệu ghi. Các bus này có thể rộng 8 bit, 16 bit hoặc 32 bit. Bus dữ liệu đọc và ghi phải có cùng độ rộng.**

# **3. Kết nối của APB bus**

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfqhlsRc4ItxPQjw-kxtPf7FjXVmJ-b4FMMIMBaaEiVEmsQ20PhcrOpyk6lkDCxXYeOdnnl6YkRAsZxYhgmKJUpfaLZ2ePEVCpV4h8UMde0e-b5HBbtAZfTSZBCJCRIhD5O7CKJ?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 1. Minh họa về kết nối APB*

- Master APB: thành phần điều khiển các tín hiệu điều khiển của bus APB. Master APB thường là một bus khác có tốc độ cao hơn trong hệ thống bus, ví dụ như AXI, AHB, ... tuy nhiên nó cũng có thể là một master đầu cuối có giao tiếp trực tiếp đến bus APB.
- Slave APB: thành phần nhận các tín hiệu điều khiển của bus APB. Bộ điều khiển EEPROM trong hình 1 là một slave APB

# **4. Hoạt động của giao tiếp APB**

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd61F9cR0VhPag97JuQgxYqATk-PO_zoz2T-qSOWewpMtgC4De7HQ9XPr-oNmkibl6UquJYpeU_9IQVHvpMTMghTbbyBLOQg_IMh2x7TZCxMYHxdRQz9ubMzqZ56UBIlnFtLiDHgA?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 2. Các trạng thái hoạt động của bus APB*

- IDLE: khi PSEL = 0. Lúc này, PENABLE cũng phải bằng "0" và slave APB không quan tâm đến giá trị các tín hiệu điều khiển còn lại trên bus như PWRITE, PWDATA hay PADDR
- SETUP: khi PSEL = 1 và PENABLE = 0. Lúc này, các tín hiệu điều khiển trên bus phải có giá trị hợp lệ. Các tín hiệu điều khiển bao gồm PADDR, PWRITE và PWDATA nếu là một truy cập "ghi".
- ENABLE: khi PSEL = 1 và PENABLE = 1. Lúc này, slave APB phải nhận xong các giá điều khiển và phải lái PRDATA đến giá trị hợp lệ nếu đó là một truy cập "đọc"
- Mỗi truy cập "đọc" hoặc "ghi" từ master APB đến slave APB phải qua 2 bước là SETUP và ENABLE. Sau bước ENABLE nếu không có truy cập nào khác thì trạng thái truy cập sẽ về IDLE nhưng nếu master APB tiếp tục có truy cập tiếp theo thì có thể lái trực tiếp qua trạng thái SETUP.

# **5. Transfers**

- Điểm quan trọng trong APB chính là tương tác giữa 3 tín hiệu PSEL, PENABLE, và PREADY. Một transfer trong APB luôn phải trải qua 2 phase:
- Setup phase: PSEL = 1, PENABLE = 0, và bỏ qua PREADY. Trong phase này, master sẽ set các tín hiệu điều khiển và PWDATA. Các slave đơn giản không cần phải quan tâm phase này.
- Access phase: PSEL = 1, PENABLE = 1, và PREADY = 0 cho đến khi slave muốn kết thúc transfer thì slave sẽ set PREADY = 1. Trong phase này, slave sẽ thực hiện công việc ghi và đọc. Nếu loại giao dịch là đọc PWRITE = 0, thì giá trị của PRDATA và PSLVERR chỉ có hiệu lực khi PREADY = 1.

## **5.1 Write transfer**

### **5.1.1 With no wait states**

[!](https://lh7-rt.googleusercontent.com/docsz/AD_4nXceMdSdqnkqRat8mWaBYsdJdW31CfDjDBJU6OsNcWaFcx2nwW9FcGB-uLqsbjiGszxyTcNpyF6Klu4Eq6h2PBwuijQsl3SCjir288tN67-ZQfteY2jlIQU5ollv49fsTV5va2jbxQ?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 3. Transfer ghi không chờ*

- Transfer này có 2 phase rõ ràng: setup phase từ T1 đến T2 (PSEL = 1, PENABLE = 0), và access phase từ T2 đến T3 (PSEL = 1, PENABLE = 1).
- Do là một transfer ghi nên PWRITE = 1. Đồng thời địa chỉ PADDR và dữ liệu ghi PWDATA cũng được cung cấp trong setup phase.
- Transfer không chờ nên access phase được kết thúc ngay lập tức với PREADY = 1 và kết thúc transfers tại T3. Master có thể bắt đầu truyền dữ liệu mới.

### **5.1.2 With wait states**

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeME1qHZGLghNcc4-pc9x_IDbswiezNAyLjp3RjAhdSJQuhFOzMBS6N5ezJszh6CIEdbPr_OgSSfnS8wZWdgNYcaznKZFdVsHN6EO3fbvLYAu0ZQ4fdZNtcayYfrqJV_ZO2A9inWQ?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 4. Transfer ghi có chờ*

- Setup phase từ T1 đến T2, nhưng access phase không kết thúc ngay, mà được kéo dài từ T2 đến T5.
- Để báo với master là slave cần phải chờ thêm, slave đặt PREADY = 0 từ T2 đến T4.
- Đến T5, slave muốn kết thúc transfer và đặt PREADY = 1. Sau T5, master có thể bắt đầu truyền dữ liệu khác.

## **5.2 Write strobes**

- PSTRB cho phép truyền dữ liệu rải rác trên bus dữ liệu ghi. Mỗi bit trong tín hiệu PSTRB tương ứng với 1 byte trên bus dữ liệu ghi.
- Khi được đưa lên mức HIGH (mức cao), PSTRB cho biết rằng byte tương ứng trên bus dữ liệu ghi chứa thông tin hợp lệ.
- Mỗi bit PSTRB[n] tương ứng với 1 byte (8 bit) trên bus dữ liệu ghi (PWDATA).

Ví dụ: với bus 32-bit (PWDATA[31:0]), ta có 4 byte

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfde88ksOAsbPUYFsFLUHJ5c_t5RJPIYkPsf1lTgFcWG9MXUreVbQBZrSNJedE1VFVi4ponRP25W4lMWTtD16Qeg3Luz_ah_iPSw_VN92b4aSNVlH9DJbPJ5bPW9xOJIZj08KZW?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 5.  Minh họa việc mapping giữa các bit của PSTRB và các byte tương ứng trên bus dữ liệu 32-bit.*

- *Đặc biệt*
- PSTRB là tín hiệu tùy chọn (optional).
- Một số thiết bị ngoại vi có thể không hỗ trợ truyền ghi rải rác (sparse writes), vì vậy cần kiểm tra tính tương thích giữa hai đầu giao tiếp: Requester và Completer (thiết bị thực hiện).
- Bảng tương thích tín hiệu PSTRB

| PSTRB | Completer: không có tín hiệu | Completer: có tín hiệu |
| --- | --- | --- |
| Requester: không có tín hiệu | Không hỗ trợ ghi rải rác. | Tất cả các byte trên bus dữ liệu ghi đều hợp lệ.
Ghi toàn bộ dữ liệu 32-bit (không dùng ghi rải rác). |
| Requester: có tín hiệu | Không hỗ trợ ghi rải rác. | Tương thích. |

## **5.3 Read transfers**

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfJA6TtKaEBtdDylgnevO-zMmYNXfq-nuFPEHXPJaL7a7b_Hz4zsh95uRqqGMbjubeSy0G4uC0N0cgYYX-vrMGnMZ2ct50GmH19gvsijsQi45i5gaboY3LKD-q130Am-T4f58Uc2A?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 6. Transfer đọc không chờ*

!![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeI-SKuuc2im7QRJ6zwE__jX6D8JYYCqQyKHIE_7fFy8alYH0VbPHrQbkpK6zNKitgVx8wFEdY1VZ0SBovIwJWj46SIlu8eIVHr_guvqDHKYu1o5ccadrM2kkHFNAQnxHY8PgBH?key=L_Gjf62Qg3JKXWpL6xNacw)

*Hình 7. Transfers đọc có chờ*

- PSEL, PENABLE, và PREADY cũng tương tự như trường hợp ghi, nhưng do là transfer đọc nên có PWRITE = 0. Lưu ý rằng dữ liệu đọc PRDATA chỉ có hiệu lực khi PREADY = 1.

## **5.4 Error response**

- Trong quá trình truyền dữ liệu trên bus APB, lỗi là tình huống có thể xảy ra trong cả đọc và ghi. Để xử lý và phát hiện các lỗi này, giao thức APB cung cấp tín hiệu PSLVERR như một cơ chế phản hồi lỗi từ thiết bị ngoại vi (Completer) về cho bộ điều khiển (Requester).
- Tín hiệu PSLVERR chỉ được coi là hợp lệ trong chu kỳ cuối cùng APB, tức là thời điểm mà ba tín hiệu PSEL, PENABLE và PREADY cùng được thiết lập ở mức cao (HIGH). Khi đó, nếu PSLVERR cũng được đưa lên mức cao, điều này báo hiệu rằng đã xảy ra lỗi trong quá trình giao dịch. Trong các chu kỳ còn lại, nếu bất kỳ tín hiệu nào trong ba tín hiệu trên ở mức thấp, thì PSLVERR nên được giữ ở mức thấp để tránh gây hiểu nhầm.
- Việc xảy ra lỗi không nhất thiết đồng nghĩa với việc trạng thái của thiết bị ngoại vi không bị thay đổi. Cụ thể, khi ghi dữ liệu có thể đã được ghi vào thanh ghi mục tiêu dù lỗi được báo về. Tương tự, trong khi đọc dữ liệu trả về có thể không hợp lệ, nhưng không có yêu cầu nào bắt buộc thiết bị ngoại vi phải trả về toàn số 0. Do đó, bộ điều khiển cần cân nhắc kỹ lưỡng trước khi sử dụng dữ liệu trong những tình huống có lỗi.
- Ngoài ra, không phải tất cả thiết bị ngoại vi đều hỗ trợ tín hiệu PSLVERR. Trong trường hợp một thiết bị không cung cấp phản hồi lỗi, chân PSLVERR từ phía thiết bị cần được nối cứng xuống mức thấp (LOW) để đảm bảo tính nhất quán trong hệ thống.
- Khi có cầu nối (bridge) giữa các bus như AXI → APB hoặc AHB → APB, nếu xảy ra lỗi trong giao dịch APB (thể hiện qua tín hiệu PSLVERR), thì lỗi đó cần được chuyển đổi (mapped) về tín hiệu lỗi tương ứng trong giao thức nguồn (AXI hoặc AHB) để hệ thống hiểu và xử lý đúng.

## **5.5 Protection unit support**

- Trong các hệ thống phức tạp, việc bảo vệ chống lại các truy cập bất hợp pháp là điều cần thiết để đảm bảo an toàn và độ tin cậy của hệ thống. Đối với giao diện APB (Advanced Peripheral Bus), cơ chế bảo vệ này được thực hiện thông qua các tín hiệu PPROT[2:0]. Mỗi bit trong tín hiệu PPROT đại diện cho một mức độ bảo vệ khác nhau trong giao tiếp.
- Bảng dưới đây thể hiện ba mức độ bảo vệ truy cập dựa trên các bit của tín hiệu PPROT:

| Bit PPROT | Chế độ bảo vệ | Mô tả | Giá trị |
| --- | --- | --- | --- |
| PPROT[0] | Normal / Privileged | Chỉ định chế độ xử lý. LOW là normal, HIGH là privileged. | LOW: Truy cập bình thường
 HIGH: Truy cập đặc quyền |
| PPROT[1] | Secure / Non-secure | Phân biệt truy cập an toàn hoặc không an toàn. | LOW: An toàn
 HIGH: Không an toàn |
| PPROT[2] | Data / Instruction | Gợi ý loại truy cập là dữ liệu hay lệnh. Có thể không chính xác tuyệt đối. | LOW: Dữ liệu
 HIGH: Lệnh |
- Tín hiệu PPROT là tùy chọn trên cả giao diện Requester và Completer. Việc có hay không sử dụng tín hiệu này phụ thuộc vào yêu cầu của từng bên. Bảng sau đây thể hiện mức độ tương thích giữa các thiết bị Requester và Completer trong các trường hợp có và không sử dụng tín hiệu PPROT:

| PPROT | Completer: không có tín hiệu | Completer: có tín hiệu |
| --- | --- | --- |
| Requester: không có tín hiệu | Tương thích | Không tương thích
 (Có thể tương thích nếu thuộc tính bảo vệ là cố định và phù hợp) |
| Requester: có tín hiệu | Tương thích
 (PPROT có thể bị bỏ qua) | Tương thích |

## **5.6 Realm Management Extension (RME)**

Phần này mô tả cách giao thức APB hỗ trợ tính năng Realm Management Extension (RME) của kiến trúc ARM. RME cung cấp thêm các không gian địa chỉ vật lý Root và Realm, giúp tăng cường bảo mật hệ thống thông qua việc kiểm tra quyền truy cập khi thực hiện các giao dịch APB.

### **5.6.1 Root và không gian địa chỉ vật lý Realm**

- Tín hiệu PNSE (Physical Non-Secure Enable) được thêm vào để phân biệt không gian địa chỉ vật lý trong hệ thống sử dụng RME. Tín hiệu này chỉ hợp lệ khi PSEL được kích hoạt.
- Bảng mô tả tín hiệu PNSE

| Tín hiệu | Độ rộng | Nguồn | Mô tả |
| --- | --- | --- | --- |
| PNSE | 1 bit | Requester | Tín hiệu mở rộng quyền truy cập. PNSE phải được đặt HIGH khi PSEL được kích hoạt. |
- Khi sử dụng kết hợp PNSE và PPROT[1], không gian địa chỉ vật lý được xác định như sau:
- Bảng PNSE và PPROT[1] xác định không gian địa chỉ

| PNSE | PPROT[1] | Không gian địa chỉ vật lý |
| --- | --- | --- |
| 0 | 0 | Secure |
| 0 | 1 | Non-secure |
| 1 | 0 | Root |
| 1 | 1 | Realm |

### **5.6.2 Cấu hình**

- Thuộc tính RME_Support được sử dụng để xác định một giao diện APB có hỗ trợ Realm Management Extension hay không.
    
    + Nếu là True: Giao diện hỗ trợ RME, tín hiệu PNSE phải có mặt.
    
    + Nếu là False: Giao diện không hỗ trợ RME, tín hiệu PNSE không được sử dụng.
    
- Nếu tín hiệu PNSE có mặt trên giao diện, thì PPROT cũng bắt buộc phải có mặt. Ngoài ra, PNSE được bảo vệ bởi tín hiệu PCTRLCHK.
- Bảng tính tương thích của RME_Support

| RME_Support | Completer: False | Completer: True |
| --- | --- | --- |
| Requester: False | Tương thích. | Tương thích. PNSE nên buộc mức LOW. |
| Requester: True | Không tương thích. | Tương thích. |

## **5.7  Cơ chế Đánh Thức (Wake-up Signaling)**

- Trong giao diện APB, cơ chế đánh thức được sử dụng để thông báo hoạt động từ phía Requester, giúp kích hoạt các thành phần khác như bộ cấp nguồn hoặc bộ tạo xung. Tín hiệu đánh thức chính là PWAKEUP, một tín hiệu không nhiễu (glitch-free) có thể được định tuyến đến các khối quản lý năng lượng.
- Việc một giao diện có hỗ trợ tín hiệu đánh thức hay không được xác định bởi thuộc tính Wakeup_Signal:
- True: có tín hiệu PWAKEUP.
- False: không có tín hiệu PWAKEUP (mặc định nếu không khai báo).
- Tín hiệu wake-up chỉ được hỗ trợ trong giao thức APB5.
- Các lưu ý và khuyến nghị khi sử dụng PWAKEUP:
- Đồng bộ với PCLK, nhưng có thể được lấy mẫu không đồng bộ ⇒ cần không nhiễu.
- Có thể được kích hoạt trước, trong hoặc sau khi PSELx được kích hoạt.
- Completer có thể chờ PWAKEUP trước khi phản hồi bằng PREADY.
- PWAKEUP cần giữ nguyên cho đến khi PREADY được kích hoạt nếu PSELx đang ở mức cao.
- Nên kích hoạt PWAKEUP ít nhất một chu kỳ trước PSELx để tránh làm chậm giao dịch.
- Nên ngắt PWAKEUP khi không còn giao dịch.
- Nếu xung nhịp giữa Requester và Completer khác nhau, cần cẩn trọng vì có thể bị mất dữ liệu do lệch thời gian.

## **5.8 User signaling**

- Trong một số ứng dụng sử dụng giao thức APB (Advanced Peripheral Bus), có thể phát sinh nhu cầu bổ sung các tín hiệu không được định nghĩa trong giao thức chuẩn. Tính năng User Signaling được thiết kế để giải quyết vấn đề này, cho phép người dùng định nghĩa và sử dụng các tín hiệu riêng cho mục đích tùy biến, mở rộng khả năng giao tiếp của APB.
- Tuy nhiên, theo khuyến cáo, các tín hiệu user không nên được sử dụng nếu không thực sự cần thiết. Giao thức APB không định nghĩa chức năng cụ thể cho các tín hiệu này, dẫn đến nguy cơ không tương thích nếu hai thành phần sử dụng các tín hiệu user theo cách khác nhau. Đặc biệt, user signaling chỉ được hỗ trợ trong giao thức APB5.
- Tất cả các tín hiệu user đều là tùy chọn. Nếu thuộc tính chiều rộng (width) tương ứng bằng 0, tín hiệu đó sẽ không xuất hiện. Dưới đây là bảng mô tả chi tiết các tín hiệu user trong APB5:

| Tín hiệu | Chiều rộng | Nguồn phát | Mô tả |
| --- | --- | --- | --- |
| PAUSER | USER_REQ_WIDTH | Requester | - Thuộc tính yêu cầu do người dùng định nghĩa.
 - Hợp lệ khi PSELx được kích hoạt.
 - Phải giữ nguyên trong cả hai pha Setup và Access của giao dịch.
 - Giữ nguyên trong từng chu kỳ của pha Access. |
| PWUSER | USER_DATA_WIDTH | Requester | - Thuộc tính ghi dữ liệu.
 - Hợp lệ khi PSEL và PWRITE được kích hoạt.
 - Giá trị phải giống nhau trong pha Setup và Access.
 - Giữ nguyên giá trị trong từng chu kỳ của pha Access. |
| PRUSER | USER_DATA_WIDTH | Completer | - Thuộc tính đọc dữ liệu.
 - Hợp lệ khi PSEL, PENABLE và PREADY được kích hoạt và PWRITE bị vô hiệu hóa. |
| PBUSER | USER_RESP_WIDTH | Completer | - Thuộc tính phản hồi.
 - Hợp lệ khi PSEL, PENABLE và PREADY được kích hoạt. |
- Việc hỗ trợ các tín hiệu user là tùy chọn. Độ rộng của mỗi tín hiệu là do người thiết kế định nghĩa (IMPLEMENTATION DEFINED), có thể khác nhau giữa các loại tín hiệu yêu cầu, dữ liệu và phản hồi.
- Mặc dù không bắt buộc, nhưng nên xem xét cung cấp đầy đủ các tín hiệu user trong các hệ thống có cầu nối giữa các miền xung nhịp (clock domain bridge) hoặc các bộ liên kết (interconnect). Tuy nhiên, không bắt buộc phải áp dụng các tín hiệu user ở phía Completer.