Họ tên : Đồng Đại Đạt - mã sinh viên:  23010877
 
THỰC HÀNH LAB 8: KIỂM THỬ HIỆU NĂNG VỚI APACHE JMETER

1. MỤC TIÊU BÀI LAB
Về kiến thức: Nắm vững khái niệm, nguyên lý và tầm quan trọng của kiểm thử hiệu năng (Performance Testing/Load Testing) trong quy trình phát triển và vận hành phần mềm.
Về công cụ: Thành thạo cách cài đặt, cấu hình kịch bản và sử dụng các thành phần cốt lõi của công cụ Apache JMeter (Thread Group, Sampler, Listeners).
Về kỹ năng: Có khả năng tự xây dựng kịch bản kiểm thử tải độc lập, giả lập lượng người dùng lớn truy cập đồng thời vào hệ thống API, từ đó biết cách đọc hiểu và phân tích các chỉ số hiệu năng (Response Time, Error Rate, Throughput) để đánh giá sức chịu tải của hệ thống.


2. KỊCH BẢN KIỂM TRA (TEST SCENARIO)

Để đảm bảo tính độc lập và không trùng lặp đề tài, kịch bản lựa chọn thực hiện kiểm thử hiệu năng trên hệ thống Open API quốc tế cung cấp dữ liệu Pokémon (**PokeAPI**).

* **Đối tượng kiểm thử (API Endpoint):** `https://pokeapi.co/api/v2/pokemon?limit=100`
* **Mục đích kịch bản:** Giả lập hành vi của một lượng lớn người dùng đồng thời gửi yêu cầu truy vấn thông tin dữ liệu của 100 loài Pokémon, từ đó đánh giá năng suất phản hồi và độ ổn định của hệ thống Server Backend dưới áp lực tải.
* **Thông số cấu hình tải (Thread Group):**
  * **Number of Threads** *(Số người dùng giả lập đồng thời)*: 100 users.
  * **Ramp-up Period** *(Thời gian kích hoạt toàn bộ 100 users)*: 10 giây (tăng tải tuyến tính trung bình 10 users mỗi giây).
  * **Loop Count** *(Số vòng lặp thực thi của mỗi user)*: 1 lần.
  * **Tổng số Request dự kiến gửi đi:** 100 Requests.
   
   
### Cấu hình kịch bản trên JMeter

#### 1. Tạo nhóm người dùng (Thread Group)
<img width="627" height="177" alt="Tạo Thread Group" src="https://github.com/user-attachments/assets/b0485a0b-8e01-4880-a698-d481971aced5" />

#### 2. Cấu hình đường link gọi dữ liệu (HTTP Request)
<img width="627" height="151" alt="Thêm HTTP Request" src="https://github.com/user-attachments/assets/eab943fd-00a9-4675-b4a5-6a6437cd1e60" />

---

 3. KẾT QUẢ KIỂM TRA (TEST RESULTS)

Quá trình thực thi kịch bản tải trên Apache JMeter được ghi nhận và giám sát trực quan qua 2 bộ đọc kết quả (Listeners) dưới đây:

 3.1. Kết quả chi tiết từng Request (View Results Tree)
 Mục tiêu giám sát: Theo dõi trạng thái Sống/Chết và mã phản hồi HTTP của từng request riêng lẻ dưới áp lực tăng tải.
 Kết quả Toàn bộ 100 request đều được hệ thống đánh dấu tích xanh thành công, trả về mã phản hồi chuẩn `HTTP 200 OK` và không có request nào bị gián đoạn hay ngắt kết nối giữa chừng.
 <img width="627" height="340" alt="view result tree" src="https://github.com/user-attachments/assets/bc711ae6-8a53-426a-84ab-5a48698d169e" />



 3.2. Bảng thống kê hiệu năng tổng quan (Summary Report)
 Mục tiêu giám sát: Thu thập các số liệu tính toán thô về thời gian phản hồi hệ thống và năng suất xử lý dữ liệu.
 Bảng số liệu nhảy đầy đủ thông tin sau khi kết thúc 10 giây chạy tải:
<img width="627" height="336" alt="Summary Report" src="https://github.com/user-attachments/assets/01754d2a-0327-4bf8-adc7-02f51d40e4ed" />


4. PHÂN TÍCH KẾT QUẢ KIỂM TRA
Dựa trên các số liệu kỹ thuật thực tế thu được từ bảng "Summary Report", hiệu năng của hệ thống PokeAPI được phân tích chi tiết qua 3 chỉ số:

Tỷ lệ lỗi hệ thống: Đạt mức 0.00% => máy chủ Backend của PokeAPI có cấu hình hạ tầng mạng và bộ nhớ đệm tốt. Dù bị 100 đối tượng pokemon json dùng dồn tải liên tục trong thời gian ngắn để lấy khối lượng dữ liệu tương đối lớn , hệ thống vẫn xử lý ok, không sập luồng và không từ chối bất kỳ yêu cầu nào.
- Thời gian phản hồi :
  Thời gian phản hồi trung bình: Đạt ~4.05 giây.
  Thời gian phản hồi nhanh nhất: Đạt 0.159 giây.
  Thời gian phản hồi chậm nhất: Lên tới ~8.8 giây, cho thấy khi lượng người dùng được đẩy lên mức đỉnh điểm (giây thứ 9 và thứ 10), hàng đợi xử lý của Server bắt đầu bị quá tải nhẹ, dẫn đến độ trễ mạng tăng.
- Năng suất xử lý của Server: Đạt tốc độ 9.4 request/giây. Chỉ số này phản ánh khả năng giải phóng băng thông và phân phối gói tin JSON của máy chủ ở mức ổn định dưới điều kiện kiểm thử.


5. KẾT LUẬN
 kết quả bài thực hành: Bài Lab 8 đã hoàn thành chính xác và đầy đủ tất cả các nội dung yêu cầu. Kịch bản kiểm thử hiệu năng hoạt động đúng thiết kế, số liệu thu thập trung thực và minh chứng trực quan rõ ràng.
