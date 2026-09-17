# Báo cáo Day 5

- Mã học viên theo lớp: **2A202602311**
- Ngày / CVAT local: 17/09/2026 · http://localhost:8080
- Công cụ đã dùng: CVAT local, Polygon

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | chưa có | 0 / 1 | 3 |
| cp3_thin | chưa có | 0 / 1 | 3 |
| cp4_curb | chưa có | 0 / 1 | 3 |
| cp6_coverage | chưa có | 0 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Tôi chưa ghi lại tên ảnh và vị trí của object đầu tiên, nên chưa cung cấp được thông tin xác định chính xác object đó.
- Class và quy tắc tôi dùng để chọn biên: Trong quá trình làm Medium, tôi đã hỏi lại về cách gán nhãn người đứng cạnh nhau. Quy tắc được làm rõ là mỗi người có một mask `person` riêng, dù đứng sát nhau; đường biên chỉ bao phần nhìn thấy, không bao sang người bên cạnh.
- Gợi ý tự động: Tôi đã thử Interactor ViT-B nhưng gặp lỗi `Failed to parse: http://host.docker.internal:None`, nên tiếp tục làm thủ công bằng Polygon.
- Quyết định được ghi nhận: Với instance segmentation, cùng class không có nghĩa là cùng object. Tôi cần phân biệt từng người/phương tiện thay vì gom tất cả vào một vùng như semantic segmentation.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: Công cụ AI Tools / Interactor ViT-B trong CVAT local; lỗi xảy ra khi gọi công cụ, không xác định là lỗi của một vùng annotation cụ thể.
- Lỗi thuộc loại: Khác — lỗi triển khai công cụ hỗ trợ gán nhãn.
- Bằng chứng tôi nhìn thấy: CVAT báo `Failed to parse: http://host.docker.internal:None`. Khi kiểm tra, Nuclio ghi nhận model ở trạng thái `building`, trong khi container build đã dừng với mã 255 và không có container SAM chạy. Kiểm tra GPU trong container CUDA nhận được GTX 1650.
- Hành động xử lý: Tôi đã sao lưu cấu hình, gỡ đăng ký ViT-B bị kẹt bằng nuctl và kiểm tra kết quả `No functions found`. Do máy chỉ còn khoảng 0,6 GB RAM trống tại thời điểm kiểm tra, tôi chưa build lại model và tiếp tục làm bài bằng Polygon.
- Trạng thái sau xử lý: Đã gỡ trạng thái triển khai bị kẹt, nhưng AI Tools chưa được khôi phục. Tôi đã hoàn thành và export 5 task ghi trong mục 1 bằng phương pháp thủ công; chưa có lần export lại nào được ghi nhận do sửa một lỗi mask cụ thể.
- Phần còn thiếu: Tôi chưa ghi lại một lỗi annotation cụ thể và bằng chứng trước/sau sửa. Nội dung trên là sự cố công cụ thực tế, không phải xác nhận rằng đã sửa một lỗi mask. Nếu cần dùng AI Tools, tôi cần hỗ trợ triển khai lại model phù hợp với tài nguyên máy.
- Kết quả tự đánh giá: chưa có điểm.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp1_holes, ảnh 000000144300.jpg, kính chắn gió và khe trên xe máy Honda đỏ ở tiền cảnh | Khoét các phần này khỏi mask hoặc giữ trong mask xe | Quy tắc riêng của cp1_holes yêu cầu giữ kính/khe trong mask vật | Quy tắc chọn để áp dụng: giữ các phần này trong mask `motorcycle`, không khoét tùy tiện |
| cp2_slice, ảnh 000000017627.jpg, cụm ô tô đứng sát nhau ở giữa và bên phải ảnh | Gộp các xe cùng class hoặc tách từng xe | Instance segmentation phân biệt từng vật; các xe có thân và đường viền riêng | Quy tắc chọn để áp dụng: mỗi xe một instance `car`, chỉ lấy phần xe nhìn thấy |
| easy_semantic, vùng trời tiếp giáp nhà và tán cây; đã trao đổi về object 27 và 33 nhưng chưa ghi lại tên ảnh/class tương ứng | Vẽ các vùng không chồng lấn hoặc vẽ sky nền rồi phủ building/vegetation bằng layer | Khi tạo mask semantic, cần kiểm soát z-order và kiểm tra nhãn cuối cùng ở vùng chồng; ID object không quyết định thứ tự lớp | Tôi đã hỏi về cách tô nền và thứ tự lớp. Cần đặt đúng lớp trước/sau và kiểm tra mask export; không kết luận object 27 hay 33 ở trên chỉ từ số ID |
