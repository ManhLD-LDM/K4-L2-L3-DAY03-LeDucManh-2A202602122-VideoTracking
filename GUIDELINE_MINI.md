# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: Lê Đức Mạnh / Nhóm T030
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): Tuyệt đối không gán xe máy và người đi bộ xuất hiện xen kẽ giữa các làn đường; xe kéo chở hàng có đầu kéo 4 bánh thì gán phần đầu xe/toàn bộ xe, không gán xe ba gác.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | Giữ nguyên ID nếu bị che **dưới 25 frame** (2 giây @ 12.5 fps) | Duy trì tính nhất quán của identity (tối ưu AssA và IDF1) theo chuẩn lab |
| Xe bị che lâu hơn ngưỡng trên | Mở track mới với ID mới | Quá 2 giây không xác định được danh tính phương tiện có bị thay thế hay không |
| Xe rời khung hình rồi quay lại | Mặc định: **track mới** | Phương tiện đã rời hẳn khỏi không gian quan sát thì không bảo toàn trạng thái |
| Hai xe cắt nhau / chồng lên nhau | Mỗi xe giữ nguyên ID của mình, không hoán đổi | Tránh lỗi ID Switch (IDSW) khi hai bounding box có độ trùng lặp IoU cao |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | Bbox chạm đúng rìa ảnh (0 hoặc max width/height), không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | Bbox ôm sát phần **nhìn thấy được**, không vẽ ước lượng vùng bị che |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | Bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh (chiều rộng tối thiểu >= 15px) |
| Xe đang đỗ, không di chuyển | Gán liên tục từ frame đầu đến frame cuối nó có mặt trong khung hình, giữ cố định 1 ID |
| Keyframe đặt dày ở đâu | Đặt dày quanh các khúc cua, đổi hướng, tăng/giảm tốc hoặc bị che khuất; thưa khi đi thẳng đều |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01` / Frame 1–190 / ID 2
- Tình huống: Chiếc ô tô đỗ tĩnh bên lề đường bên trái suốt toàn bộ thời lượng 190 frame của clip, gần như không di chuyển (tọa độ x ≈ 209.9, y ≈ 251.0).
- Quyết định: Giữ nguyên track ID 2 liên tục từ frame 1 đến frame 190, không bấm Outside.
- Lý do: Theo định nghĩa schema `vehicle`, phương tiện đang đỗ vẫn là ô tô 4 bánh hợp lệ trong video, việc duy trì track này đảm bảo độ bao phủ (Coverage/Recall) không bị đánh lỗi False Negative (FN).

### Ca 2
- Clip / frame / ID: `clip_01` / Frame 1–10 / ID 1
- Tình huống: Xe di chuyển nhanh ở góc dưới bên trái màn hình và nhanh chóng lao ra khỏi khung hình ở frame 10.
- Quyết định: Bắt đầu vẽ từ frame 1, đến đúng frame 11 khi xe vừa khuất hẳn thì bấm ngay phím Outside (`O`).
- Lý do: Nếu quên bấm Outside, CVAT sẽ giữ nguyên vị trí bbox tĩnh này kéo dài đến tận frame 190, gây lỗi "bbox treo" (ghost bbox) làm tụt nghiêm trọng chỉ số MOTA và DetA.

### Ca 3
- Clip / frame / ID: `clip_01` / Frame 112–190 / ID 7
- Tình huống: Phương tiện xuất hiện ở khoảng cách rất xa từ hậu cảnh (tọa độ ban đầu kích thước nhỏ w ≈ 40px, h ≈ 55px), di chuyển dần vào giữa khung hình.
- Quyết định: Chỉ bắt đầu tạo track ID 7 từ frame 112 khi xe đã hiện rõ nét là xe 4 bánh, không gán ở các frame 100-111 khi đối tượng chỉ là vệt mờ chưa phân biệt được với xe máy.
- Lý do: Tuân thủ quy định ngưỡng nhận diện xe bốn bánh để tránh phát sinh False Positive (FP) do phỏng đoán.

### Ca 4 (Bổ sung từ kinh nghiệm warm-up `clip_02`)
- Clip / frame / ID: `clip_02` / Frame 1–60 / ID 1 & ID 2
- Tình huống: Hai xe ô tô đỗ ở sát mép trên cùng của khung hình (y = 1.0), chỉ nhìn thấy khoảng 1/2 thân dưới của xe.
- Quyết định: Gán bbox kéo chạm sát mép trên ảnh (y = 1.0), không ước lượng phần nóc xe nằm ngoài ảnh, duy trì suốt 60 frame.
- Lý do: Lần gán đầu tiên nhóm bỏ sót 2 xe này khiến điểm warm-up bị thiếu 2 track. Sau khi bổ sung chuẩn theo luật "bbox chạm rìa ảnh", IDF1 của `clip_02` đạt `0.989`.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- Làm rõ quy định đối với xe đỗ tĩnh ở rìa khung hình: Kể cả khi xe bị khuất một phần bởi mép trên/dưới ảnh, nếu nhận diện được là xe bốn bánh đang đỗ thì vẫn bắt buộc gán và duy trì trọn vẹn thời lượng xuất hiện.
- Quy định rõ thao tác kích hoạt bbox trên canvas trước khi kéo resize để tránh lỗi trôi khung hình (pan canvas) thay vì chỉnh bbox.
