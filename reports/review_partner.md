# Peer review — Day 3

Chép file này thành `reports/review_partner.md`. Reviewer chỉ ghi finding; tác giả
tự sửa bài của mình và điền closure.

| Trường | Giá trị |
| --- | --- |
| Author | Lê Đức Mạnh |
| Reviewer | Bạn cùng nhóm T030 |
| Pair ID | T030-PAIR-01 |
| CVAT version | CVAT Community Server 2.29.0 |
| Thời điểm review | 2026-09-15 11:30 (UTC+7) |

## Danh sách finding

Mỗi finding bắt buộc có `frame + ID + lỗi + sửa thế nào`. Nếu chưa thống nhất,
dùng `needs-review`; không ép tác giả sửa theo cảm tính.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | 0–14 | 1–15 | 2 | Cảnh báo bbox tĩnh | Xe đỗ tĩnh suốt 190 frame; script cảnh báo bbox đứng im frame 1–15 nghi ngờ quên Outside | Áp dụng rule "xe đang đỗ vẫn là vehicle hợp lệ", giữ nguyên track không bấm Outside | not-a-defect |
| 2 | 10 | 11 | 1 | Biên track (Exit) | Xe đi nhanh ở rìa dưới bên trái, frame 11 xe đã ra hẳn ngoài khung hình | Bấm phím Outside (`O`) ngay tại frame 11 để ngắt track, tránh tạo bbox treo | fixed |
| 3 | 111 | 112 | 7 | Điểm xuất hiện (Entry) | Xe ở hậu cảnh rất xa, frame 100-111 còn mờ chưa rõ 4 bánh | Chỉ gán từ frame 112 khi mắt thường nhận diện rõ là ô tô 4 bánh theo ngưỡng rule | fixed |
| 4 | 0–59 | 1–60 | 1, 2 (clip_02) | Bỏ sót (Coverage) | Hai xe đỗ ở mép trên ảnh clip_02 bị che 1/2 thân xe ban đầu chưa gán | Bổ sung 2 track cho 2 xe đỗ chạm rìa trên ảnh y=1.0 | fixed |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | clip_01 đạt 8 tracks (vượt chỉ tiêu 6 tracks), clip_02 đạt 6 tracks |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | Toàn bộ các track duy trì ID độc lập suốt vòng đời |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Không có hiện tượng đổi ID khi xe di chuyển chéo làn |
| Entry/exit đúng; không box treo sau khi xe rời khung | PASS | Track 1 bấm Outside đúng frame 11; không có bbox treo lơ lửng |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Bbox ôm sát thân xe, chạm đúng rìa ảnh (x=0, y=1.0) |
| Frame giữa hai keyframe không bị interpolation drift | PASS | Đã rà soát lượt 3, các frame nội suy khít với quỹ đạo xe |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | Định dạng MOT 1.1 chuẩn, frame 1..190, cột 2 là track ID tăng dần |
| Mọi finding có cách sửa và closure do tác giả điền | PASS | 4 findings đều có phân tích và chốt closure đầy đủ |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | Tua nhanh clip, các số ID hiển thị ổn định, không nhấp nháy, IDSW = 0 |
| 2 — endpoint/scope | ĐÃ SỬA | Frame đầu vào hợp lý khi xe rõ; frame cuối được bấm Outside triệt để |
| 3 — geometry/interpolation | PASS | Bbox khít tại các keyframe và không bị trôi lệch ở các frame nội suy giữa |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: Việc xác định đúng xe đang đỗ vẫn là `vehicle` hợp lệ cần duy trì track (như xe ID 2 ở clip_01 và 2 xe đỗ ở clip_02). Quy tắc này giúp tránh lỗi False Negative nghiêm trọng và nâng IDF1 từ 0.62 lên 0.989.
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do: Finding #1 (Cảnh báo track 2 đứng yên frame 1–15). Đây là xe đỗ thật ngoài đời thực chứ không phải lỗi quên bấm Outside, do đó đóng là `not-a-defect`.
3. Một rule cần Lab Coach làm rõ: Quy định kích thước pixel tối thiểu ở hậu cảnh xa đối với xe vừa xuất hiện để thống nhất độ nhạy giữa các annotator.
