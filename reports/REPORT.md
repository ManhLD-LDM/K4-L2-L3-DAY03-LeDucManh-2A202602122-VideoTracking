# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: Lê Đức Mạnh / Nhóm T030
Ngày: 2026-09-15

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT Community Server 2.29.0 |
| Thời gian gán `clip_02` (warm-up) | 25 phút |
| Thời gian gán `clip_01` | 75 phút |
| Số track đã vẽ trong `clip_01` | 8 tracks |
| Số keyframe trung bình mỗi track | 6.5 keyframes/track |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. **Xe đỗ dài hạn (ID 2 ở `clip_01` đỗ suốt từ frame 1 đến 190)**: Phương tiện gần như đứng yên hoàn toàn. Xử lý: Duy trì track xuyên suốt 190 frame theo đúng luật xe đỗ vẫn là `vehicle`, không bấm Outside để bảo toàn độ phủ (Recall/Coverage).
2. **Xe di chuyển nhanh và rời khung hình sớm (ID 1 ở `clip_01`)**: Xe chỉ xuất hiện từ frame 1 đến frame 10 rồi lao ra khỏi góc dưới màn hình. Xử lý: Bấm phím Outside (`O`) dứt khoát tại frame 11 để ngăn CVAT nội suy kéo dài bbox treo (ghost bbox) đến cuối video.
3. **Xe xuất hiện ở hậu cảnh xa, kích thước rất nhỏ (ID 7 ở `clip_01`)**: Từ frame 100 đến 111 xe chỉ là các điểm ảnh mờ, khó phân biệt với xe máy đi cùng chiều. Xử lý: Áp dụng quy tắc ngưỡng tối thiểu (w >= 15px), chỉ bắt đầu track từ frame 112 khi xe đã hiển thị rõ đặc trưng 4 bánh.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- **Lượt 1 (Nhìn số ID)**: Tua nhanh video với tốc độ cao, mắt tập trung vào các con số ID trên bbox. Xác nhận các số ID ổn định, không bị nhấp nháy hoặc nhảy sang số khác giữa chừng (ID switch = 0).
- **Lượt 2 (Frame đầu và frame cuối)**: Rà soát frame xuất hiện và frame biến mất của từng xe. Phát hiện và xử lý triệt để frame thoát cho ID 1 (bấm Outside tại frame 11); xác nhận xe đỗ ID 2 tồn tại trọn vẹn 190 frame.
- **Lượt 3 (Frame giữa các keyframe)**: Kiểm tra các frame nội suy giữa hai keyframe đặt xa nhau. Bbox bám khít theo chuyển động tịnh tiến của xe, không bị trôi lệch hình học (LocA đạt mức cao).

Kiểm chéo với: Bạn cùng nhóm T030 (Pair ID: `T030-PAIR-01`). Chi tiết ở `reports/review_partner.md`.  
Số lỗi bạn tìm được trong bản của bạn ấy: 2 lỗi (1 lỗi bỏ sót xe đỗ sát mép trên ở `clip_02`; 1 lỗi trôi bbox nội suy ở khúc xe đổi hướng). Số lỗi bạn ấy tìm được trong bản của bạn: 2 lỗi (1 lỗi biên xe rời khung cần bấm Outside dứt khoát tại frame 11 của `clip_01`; 1 lưu ý xe đỗ ID 2 đứng yên suốt 190 frame).

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

- **Ca quyết khác nhau**: Tình huống xe ID 2 (`clip_01`) đỗ suốt từ frame 1 đến 190. Ban đầu bạn cùng nhóm nghi ngờ đây là bbox treo bỏ quên cần bấm Outside sau frame 15. Sau khi trao đổi, hai bên đối chiếu thực tế xác nhận đây là xe thật đang đỗ tại lề đường và thống nhất áp dụng rule: "Phương tiện bốn bánh đang đỗ vẫn là `vehicle` hợp lệ, duy trì track liên tục không ngắt". Bạn đóng finding này là `not-a-defect`.
- **Luật còn thiếu trong `GUIDELINE_MINI.md` ban đầu**: Thiếu quy định cụ thể về việc gán xe đỗ bị che khuất ở sát mép trên khung hình (y=1.0) và ngưỡng tối thiểu (w >= 15px) cho xe xuất hiện ở hậu cảnh xa. Sau buổi kiểm chéo, cả hai đã thống nhất bổ sung 2 quy tắc này vào `GUIDELINE_MINI.md` để đồng bộ tiêu chuẩn gán nhãn cho toàn nhóm.

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `d177758923aaf3066b75f6db8cfedf37ac63465786019b87976810a72da34deb` |
| Thời điểm khóa | `2026-09-15T05:12:53.092056+00:00` |
| Số row / frame / track trước khi mở reference | 532 rows / 190 frames / 8 tracks |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.824 | 0.807 | 0.843 | 0.894 | 0.961 | 0.925 | 0.882 | 1 | 42 | 0 |
| Sau rework | 0.824 | 0.807 | 0.843 | 0.894 | 0.961 | 0.925 | 0.882 | 1 | 42 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **CÓ (ĐẠT ngay từ vòng Pre-Gold ở mức Xuất sắc)**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Xe đỗ rìa ảnh (clip_02) | 1–60 | 1, 2 | Bổ sung 2 track cho 2 xe đỗ ở mép trên ảnh, chạm đúng rìa y=1.0 |
| Căn chỉnh biên | 11 | 1 | Đảm bảo bấm Outside dứt khoát tại frame 11 khi xe rời khung hình |
| Keyframe refinement | 61–147 | 4 | Thêm 2 keyframe ở khúc xe chuyển hướng nhẹ để tăng IoU/LocA |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | Python 3.10.12 / ultralytics 8.4.145 / torch 2.4.0+cu121 / lap 0.5.13 |
| weights / hai tracker | yolo26n.pt / bytetrack.yaml & configs/trackers/botsort-reid.yaml |
| conf / IoU / imgsz / classes | conf=0.25 / iou=0.70 / imgsz=960 / classes=[2, 5, 7] |
| device | cuda:0 (Tesla T4 GPU trên Colab) |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.824 | 0.807 | 0.843 | 0.894 | 0.961 | 0.925 | 0.882 | 1 | 42 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.769 | 0.708 | 0.838 | 0.882 | 0.891 | 0.759 | 0.874 | 117 | 11 | 0 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

- Trong kết quả của tôi: $\text{IDF1} = 0.961 > \text{MOTA} = 0.925$. Cả hai chỉ số đều ở mức rất cao ($> 0.90$), trong đó không hề có lỗi ID switch ($\text{IDSW} = 0$).
- Về mặt bản chất: $\text{MOTA} = 1 - \frac{\sum(\text{FN} + \text{FP} + \text{IDSW})}{\sum \text{GT}}$. Trong công thức này, mỗi lần xảy ra ID switch chỉ bị tính là 1 đơn vị lỗi duy nhất tại frame xảy ra chuyển đổi. Do đó, nếu một chiếc xe xuất hiện trong 100 frame nhưng bị đứt đoạn thành 2 track ID khác nhau ở giữa, MOTA chỉ bị trừ $1/100$, điểm số MOTA vẫn có thể đạt $> 0.95$.
- Ngược lại, IDF1 đo lường độ trùng khớp danh tính trên toàn bộ vòng đời quỹ đạo (trajectory lifecycle). Khi một xe bị chia đôi ID, chỉ một nửa dài hơn được ghép cặp đúng, còn nửa kia bị coi là sai danh tính hoàn toàn, khiến IDF1 bị phạt nặng (tụt xuống dưới $0.60$).
- Vì vậy, nếu thấy **MOTA cao mà IDF1 thấp**, điều đó khẳng định chắc chắn rằng bài gán nhãn không bị bỏ sót vật thể (detection tốt) nhưng **bị lỗi nghiêm trọng về duy trì danh tính** (bị phân mảnh track hoặc nhảy ID).

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

- So sánh giữa hai mô hình:
  - **IDF1**: BoT-SORT + ReID đạt **`0.900`**, cao hơn đáng kể so với ByteTrack control (**`0.875`**).
  - **AssA**: Tăng từ **`0.776`** lên **`0.820`**.
  - **IDSW**: Cả hai đều có 2 lần ID switch.
    - Ở ByteTrack: Frame 59 (track gold 4 nhảy từ ID 14 sang 15) và Frame 94 (track gold 5 nhảy từ ID 23 sang 32).
    - Ở BoT-SORT + ReID: Frame 87 (track gold 5 nhảy từ ID 17 sang 18) và Frame 113 (track gold 6 nhảy từ ID 24 sang 31).
  - **Minh chứng cụ thể (Frame sequence 55–95 của track gold 4)**: Ở ByteTrack, track gold 4 bị cắt thành 2 ID riêng biệt ([14, 15]), trong khi BoT-SORT + ReID với sự trợ giúp của vector đặc trưng ngoại hình (appearance embedding) đã liên kết mượt mà hơn, duy trì track gold 4 trọn vẹn hơn.
  - **Lưu ý quan trọng**: Đây là một **so sánh ở cấp độ hệ thống (System Comparison)**, không thể kết luận ReID là nguyên nhân nhân quả duy nhất (causal effect), bởi vì ByteTrack và BoT-SORT có sự khác nhau về kiến trúc bộ lọc Kalman, thuật toán ma trận chi phí liên kết và cơ chế bù trừ chuyển động camera (Camera Motion Compensation).

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

- **DetA**: Tăng từ `0.649` (ByteTrack) lên `0.711` (BoT-SORT + ReID).
- **FN (Bỏ sót)**: Giảm rất mạnh từ **54 xuống còn 26** (giảm hơn 50% số bbox bị sót).
- **FP (Bắt nhầm)**: Tăng nhẹ từ 88 lên 91.
- **Phân tích nguyên nhân lỗi**:
  - Việc FN giảm mạnh cho thấy BoT-SORT ReID duy trì tracklet tốt hơn khi đối tượng bị che khuất một phần.
  - Tuy nhiên, số lượng FP vẫn ở mức cao (91 FP). Qua kiểm tra chẩn đoán, lỗi này chủ yếu đến từ tầng **Detector** (YOLO26n bắt nhầm vật thể tĩnh bên đường như ID 7 tồn tại suốt từ frame 16–116 tích lũy 43 frame FP, hoặc ID 27 tồn tại 16 frame). Ngược lại, các lỗi phân mảnh track ở frame 87 và 113 thuộc về tầng **Association**.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

- **ID 7 của mô hình ReID (Frame 16–116, 43 frame FP)**: Mô hình ReID liên tục phát hiện và bám theo một vật thể tĩnh bên lề đường (quầy hàng/bốt điện ven đường) và gán nhãn là `vehicle`. Nhãn tay của tôi hoàn toàn chính xác khi bỏ qua đối tượng này vì đây không phải phương tiện giao thông 4 bánh ngoài đời thực.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

- **Frame 108–111 (ID 29 của ReID, tương ứng với track gold 7)**: Mô hình ReID bắt đầu tạo track cho chiếc xe tiến vào từ hậu cảnh từ frame 108, trong khi nhãn của tôi bắt đầu từ frame 112 (chậm hơn 4 frame). Khi soi lại video ở frame 108–111, phần đầu xe thực sự đã xuất hiện trong khung hình nhưng kích thước nhỏ và hơi mờ nên tôi đã bỏ sót. Đây là trường hợp mô hình giúp annotator rà soát lại các frame biên (entry boundary).

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

1. **Bổ sung quy chuẩn rõ ràng cho xe đỗ**: Ghi rõ ngay từ đầu trong guideline rằng mọi phương tiện 4 bánh đang đỗ (kể cả ở góc khuất rìa ảnh) đều phải gán liên tục và giữ nguyên ID.
2. **Quy trình gán 2 chặng (Coarse-to-Fine)**: Gán lướt các keyframe lớn cách nhau 20-30 frame trước để định hình quỹ đạo, sau đó tua lại ở tốc độ 0.5x để tinh chỉnh tại các điểm giao cắt và điểm ra/vào khung hình.
3. **Thao tác an toàn**: Luôn luôn click trực tiếp lên bbox trước khi resize để tránh lỗi trôi canvas, và tạo thói quen bấm lưu bằng biểu tượng Save trên toolbar sau mỗi 2 xe hoàn thành.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
- [x] `TEAM.md` (đã khai báo thông tin cá nhân và phần việc tự sở hữu)

