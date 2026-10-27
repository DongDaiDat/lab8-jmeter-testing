# THỰC HÀNH LAB 8: KIỂM THỬ HIỆU NĂNG VỚI APACHE JMETER

**Thông tin sinh viên:**
* **Họ và tên:** Đồng Đại Đạt  
* **Mã sinh viên:** 23010877  

---

## 1. MỤC TIÊU BÀI LAB

* **Về kiến thức:** Nắm vững các khái niệm nền tảng, nguyên lý vận hành và vai trò chiến lược của kiểm thử hiệu năng (*Performance Testing / Load Testing*) trong toàn bộ vòng đời phát triển và triển khai hệ thống phần mềm.
* **Về công cụ:** Làm chủ kỹ năng cài đặt, cấu hình và phối hợp vận hành các thành phần cốt lõi trong hệ sinh thái Apache JMeter bao gồm: *Thread Group* (Quản lý luồng tải), *Sampler / HTTP Request* (Giả lập hành vi giao tiếp), và *Listeners* (Thu thập & trực quan hóa dữ liệu kết quả).
* **Về kỹ năng:** Phát triển năng lực tự xây dựng kịch bản kiểm thử tải độc lập, giả lập chính xác hành vi truy cập đồng thời của lượng người dùng lớn (*Concurrent Users*) lên hệ thống API. Đồng thời, hình thành tư duy phân tích sâu các chỉ số hiệu năng kỹ thuật trọng yếu như: *Response Time* (Thời gian phản hồi), *Error Rate* (Tỷ lệ lỗi), và *Throughput* (Băng thông/Năng suất xử lý máy chủ) để đưa ra đánh giá chính xác về ngưỡng chịu tải của hệ thống.

---

## 2. KỊCH BẢN KIỂM TRẠ (TEST SCENARIO)

Để đảm bảo tính khách quan, độc lập và tiệm cận với môi trường thực tế, kịch bản lựa chọn thực hiện kiểm thử hiệu năng trên hệ thống Open API quốc tế: **PokeAPI** (Hệ thống cung cấp dữ liệu mở về Pokémon).

* **Đối tượng kiểm thử (API Endpoint):** `https://pokeapi.co/api/v2/pokemon?limit=100`
* **Mục đích kịch bản:** Giả lập hành vi của một nhóm người dùng lớn cùng lúc gửi yêu cầu truy vấn cấu trúc dữ liệu JSON của 100 loài Pokémon. Qua đó, đo lường năng suất phản hồi và đánh giá độ ổn định của hệ thống Backend Server dưới áp lực tải tăng dần.
* **Thông số cấu hình tải (Thread Group):**
  * **Number of Threads** *(Số lượng luồng/người dùng giả lập đồng thời)*: 100 users.
  * **Ramp-up Period** *(Thời gian kích hoạt toàn bộ luồng)*: 10 giây (Tăng tải tuyến tính trung bình 10 users ứng với mỗi giây - *Linear Ramp-up*).
  * **Loop Count** *(Số vòng lặp thực thi của mỗi user)*: 1 lần.
  * **Tổng số Request dự kiến gửi đi:** 100 Requests.

### Cấu hình kịch bản thực tế trên JMeter

#### 1. Khởi tạo nhóm luồng tải (Thread Group)
<img width="627" height="177" alt="Tạo Thread Group" src="https://github.com/user-attachments/assets/b0485a0b-8e01-4880-a698-d481971aced5" />

#### 2. Cấu hình giao thức kết nối (HTTP Request Sampler)
<img width="627" height="151" alt="Thêm HTTP Request" src="https://github.com/user-attachments/assets/eab943fd-00a9-4675-b4a5-6a6437cd1e60" />

---

## 3. KẾT QUẢ KIỂM TRA (TEST RESULTS)

Quá trình thực thi kịch bản tải trên Apache JMeter được giám sát, ghi nhận trực quan thông qua 2 bộ đọc dữ liệu đầu ra (*Listeners*):

### 3.1. Kết quả chi tiết từng Request (View Results Tree)
* **Mục tiêu:** Giám sát thời gian thực (*Real-time*) trạng thái sống/chết (Health Status) và mã phản hồi HTTP (*HTTP Response Code*) của từng luồng yêu cầu riêng lẻ dưới áp lực tăng tải liên tục.
* **Đánh giá sơ bộ:** Toàn bộ 100 requests đều được hệ thống xử lý thành công (hiển thị trạng thái xanh mã hóa thành công), trả về mã trạng thái tiêu chuẩn `HTTP 200 OK`. Không xảy ra hiện tượng ngắt kết nối giữa chừng hoặc từ chối dịch vụ.

<img width="627" height="340" alt="view result tree" src="https://github.com/user-attachments/assets/bc711ae6-8a53-426a-84ab-5a48698d169e" />

### 3.2. Bảng thống kê hiệu năng tổng quan (Summary Report)
* **Mục tiêu:** Tổng hợp số liệu thống kê thô, tính toán các chỉ số toán học về thời gian phản hồi hệ thống và hiệu suất truyền tải dữ liệu.
* **Ghi nhận dữ liệu:** Bảng số liệu kết xuất tự động sau khi kết thúc chu kỳ tải 10 giây:

<img width="627" height="336" alt="Summary Report" src="https://github.com/user-attachments/assets/01754d2a-0327-4bf8-adc7-02f51d40e4ed" />

---

## 4. PHÂN TÍCH KẾT QUẢ KIỂM TRA

Dựa trên các số liệu kỹ thuật thực tế thu được từ báo cáo tổng hợp `Summary Report`, hiệu năng vận hành của hệ thống PokeAPI được phân tích chuyên sâu thông qua 3 chỉ số trọng yếu:

* **Tỷ lệ lỗi hệ thống (Error Rate):** Đạt mức **0.00%**. Kết quả này chứng minh máy chủ Backend của PokeAPI sở hữu cơ chế định tuyến mạng tốt và kiến trúc lưu trữ bộ nhớ đệm (*Caching*) tối ưu. Dù bị dồn ép tải lượng (gửi 100 yêu cầu tải dữ liệu JSON khối lượng tương đối lớn trong khung thời gian ngắn), hệ thống vẫn duy trì sự toàn vẹn, xử lý mượt mà toàn bộ các luồng và không từ chối bất kỳ yêu cầu nào.
* **Thời gian phản hồi (Response Time):**
  * *Thời gian phản hồi trung bình (Average):* Đạt ~4.05 giây.
  * *Thời gian phản hồi nhanh nhất (Min):* Đạt 0.159 giây.
  * *Thời gian phản hồi chậm nhất (Max):* Chạm ngưỡng ~8.8 giây. Sự chênh lệch này chỉ ra rằng khi lượng người dùng đồng thời đạt đỉnh điểm (ở giây thứ 9 và thứ 10), hàng đợi xử lý (*Request Queue*) của Server bắt đầu xuất hiện tình trạng quá tải nhẹ, kéo theo độ trễ mạng (*Latency*) tăng cao.
* **Năng suất xử lý của Server (Throughput):** Đạt tốc độ **9.4 requests/giây**. Chỉ số này phản ánh năng lực giải phóng băng thông và tốc độ phân phối gói tin JSON ổn định của máy chủ dưới điều kiện áp lực tải được thiết lập.

---

## 5. KẾT LUẬN

**Kết quả bài thực hành:** Bài Lab 8 đã hoàn thành chính xác, nghiêm túc và đáp ứng đầy đủ tất cả các tiêu chí yêu cầu của đề bài. Kịch bản kiểm thử hiệu năng vận hành đúng biểu đồ thiết kế ban đầu; các số liệu kỹ thuật thu thập được đảm bảo tính trung thực, minh bạch với minh chứng trực quan rõ ràng thông qua hệ thống hình ảnh kiểm thử thực tế.
