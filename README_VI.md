# PHCN AI 4.1.0 – Integrated Offline-first Clinical Support

## 1. Điểm nâng cấp 4.1

- Bổ sung ô **Bệnh sử tự do**: có thể nhập/dán bệnh sử theo cách ghi thông thường, không bắt buộc điền theo mẫu câu có sẵn.
- Bổ sung `core/semantic_clinical_parser.py`: bộ phân tích tiếng Việt chạy offline, trích xuất triệu chứng, phủ định, vị trí giải phẫu, bên trái/phải, diễn tiến, thời lượng, yếu tố tăng/giảm, quan hệ trước-sau/chuỗi sự kiện được thuật lại và dấu hiệu cảnh báo.
- Phân biệt dữ kiện được ghi nhận, dữ kiện bị phủ định và dữ kiện chưa nhận diện; không tự coi thông tin vắng mặt là âm tính.
- Phát hiện một số mâu thuẫn trong bệnh sử (ví dụ cùng triệu chứng vừa được ghi nhận vừa phủ định; thông tin trái/phải chưa nhất quán).
- Ghép kết quả NLP vào bộ suy luận chuyên biệt và suy luận tổng quát; báo cáo giải thích dữ kiện nào được trích xuất và phần nào còn chưa rõ.
- Giữ nguyên 8 khu vực chức năng, SQLite, kho bài tập và nguyên tắc không gọi API AI bên ngoài.

## 2. Giới hạn cần hiểu đúng

Đây là **NLP tiếng Việt offline dựa trên quy tắc và từ vựng**, không phải mô hình ngôn ngữ lớn. Hệ thống có thể phân tích nhiều cách ghi bệnh sử mới nếu câu có dấu hiệu ngôn ngữ nhận diện được, nhưng không thể hiểu hoàn hảo mọi cách diễn đạt, từ địa phương, lỗi chính tả, thuật ngữ hiếm hoặc ngữ cảnh phức tạp. Không được diễn giải điểm heuristic thành xác suất mắc bệnh. Quan hệ trước-sau hoặc yếu tố làm tăng/giảm triệu chứng không tự chứng minh quan hệ nhân quả.

Muốn đạt mức hiểu ngôn ngữ tự do sâu hơn mà vẫn offline, giai đoạn tiếp theo cần tích hợp mô hình NLP tiếng Việt chạy cục bộ, có đánh giá trên bộ ca chuẩn hóa và kiểm định độc lập. Mô hình phải được đóng gói, quản lý phiên bản, đo RAM/tốc độ trên thiết bị mục tiêu và không tự cập nhật trọng số lâm sàng từ phản hồi chưa thẩm định.

## 3. Nội dung bản phát hành

- Ứng dụng Streamlit với 8 khu vực: Hồ sơ, Cẩm nang tri thức, Khám lâm sàng, Kho bài tập, Đối soát, Theo dõi, Kế hoạch PHCN, Hệ thống & Sao lưu.
- Bộ suy luận chuyên biệt kế thừa nhánh Deepseek PHCN 2.6; bộ suy luận tổng quát kế thừa các module PHCN AI 3.0-dev2 hiện có trong baseline 4.0.
- SQLite cục bộ, lịch sử lần khám, kế hoạch PHCN dự thảo và sao lưu.
- Kho bài tập: catalog, details, legacy library; không tự động kê phác đồ.
- Phản hồi học chỉ tạo đề xuất; trọng số chỉ thay đổi theo cơ chế duyệt hiện có.
- Không gọi OpenAI API, Gemini API hoặc dịch vụ AI bên ngoài.

## 4. Chạy trên Windows

1. Cài Python 3.10 trở lên (khuyến nghị Python 3.11).
2. Giải nén ZIP vào thư mục có quyền ghi.
3. Nhấp đúp `run_windows.bat`.
4. Lần đầu cần Internet để cài thư viện Python từ PyPI.
5. Mở địa chỉ Streamlit hiển thị trong terminal (thường `http://localhost:8501`).
6. SQLite mặc định nằm tại `data/phcn_ai.db`.

## 5. Chạy macOS/Linux

```bash
chmod +x run_mac_linux.sh
./run_mac_linux.sh
```

## 6. Truy cập bằng điện thoại

- Chạy trên máy tính trong mạng nội bộ thì điện thoại cần cùng mạng.
- Muốn truy cập từ mạng di động/Internet, phải triển khai máy chủ có địa chỉ công khai, HTTPS và mật khẩu ứng dụng.
- Không đưa hồ sơ sức khỏe lên hosting công cộng chưa được bảo vệ.

## 7. Offline trên điện thoại

- `static/offline_capture.html` là PWA ghi nhận bệnh sử thô và lưu cục bộ bằng IndexedDB sau khi được cache.
- Khi có mạng, xuất JSON từ PWA rồi nhập vào ứng dụng Streamlit để phân tích/lưu SQLite.
- **Bộ suy luận chính, kho tri thức và SQLite vẫn chạy trên máy chủ/máy chạy Streamlit.** Khi iPhone mất kết nối máy chủ, các trang Streamlit chính không tiếp tục hoạt động; chỉ trang ghi nhận offline đã cache còn dùng được. Đây chưa phải ứng dụng suy luận hoàn toàn offline trên iPhone.

## 8. An toàn lâm sàng

- Kết quả hỗ trợ tổng hợp dữ kiện và hình thành giả thuyết, không phải chẩn đoán xác định.
- Không coi nghiệm pháp chưa thực hiện là âm tính.
- Điểm heuristic không phải xác suất bệnh đã hiệu chuẩn.
- Dấu hiệu cảnh báo cần đánh giá y khoa trực tiếp; không tự động khởi động bài tập khi chưa làm rõ nguy cơ.
- Bài tập/kế hoạch PHCN phải được cá thể hóa và duyệt bởi người hành nghề có thẩm quyền.
- Không sử dụng dữ liệu bệnh nhân thật để thử nghiệm nếu chưa có cơ chế bảo mật, phân quyền và đồng ý phù hợp.

## 9. Kiểm thử

Chạy tại thư mục `PHCN_AI_4_0`:

```bash
python -m compileall -q .
PYTHONPATH=. python -m pytest -q
```

Bộ kiểm thử tự động hiện tại kiểm tra logic suy luận, trạng thái nghiệm pháp, dữ liệu thư viện bài tập, lưu trữ, hồi quy và parser bệnh sử tự do. Đây là kiểm thử phần mềm, không thay thế kiểm định lâm sàng độc lập hoặc thử nghiệm giao diện trên thiết bị thực.
