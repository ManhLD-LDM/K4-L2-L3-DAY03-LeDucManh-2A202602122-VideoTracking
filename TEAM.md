# Khai báo nhóm — chỉ điền khi làm nhóm

> **Hình thức nộp**: Bài tập nộp dưới hình thức **Cá nhân (Solo)** bởi **Lê Đức Mạnh (MSSV: 2A202602122)**.
> Bước kiểm chéo (Peer Review) được thực hiện độc lập cùng bạn trong nhóm thực hành T030 (Pair ID: `T030-PAIR-01`) theo đúng quy định của Lab Day 3.

## 1. Thông tin nhóm thực hành

- Tên nhóm: Nhóm T030
- Kênh liên lạc dùng để phối hợp: Discord / Trực tiếp tại phòng lab
- Cách phân chia review và kiểm chứng evidence: Từng thành viên gán nhãn độc lập trên CVAT, sau đó xuất file MOT 1.1 và đổi chéo file cho nhau để rà soát.

| Họ và tên | MSSV | Vai trò / phần việc | Artifact tự sở hữu |
| --- | --- | --- | --- |
| Lê Đức Mạnh | 2A202602122 | Tác giả repo / Gán nhãn clip_01, clip_02 / Chạy model Colab / Viết báo cáo | Repo này và toàn bộ artifact trong `annotations/`, `evidence/`, `outputs/`, `reports/` |

## 2. Phần đóng góp và học được của người nộp repo này

- Họ và tên / MSSV: Lê Đức Mạnh / 2A202602122
- Tôi trực tiếp tạo hoặc chỉnh sửa những artifact nào: `annotations/clip_01/gt.txt`, `annotations/clip_02/gt.txt`, `evidence/pre-gold/clip_01/`, `GUIDELINE_MINI.md`, `reports/review_partner.md`, `reports/REPORT.md`, `outputs/model_*.txt`.
- Finding hoặc quyết định annotation tôi chịu trách nhiệm: Quyết định duy trì track liên tục cho xe đỗ ID 2 (190 frame) và bấm Outside dứt khoát tại frame 11 cho xe ID 1 ở clip_01.
- Tôi học được gì về identity, occlusion, MOT hoặc ReID: Hiểu rõ cơ chế duy trì ID khi bị che khuất tạm thời, sự khác biệt bản chất giữa MOTA (phạt nhẹ ID switch) và IDF1 (đo lường tính toàn vẹn danh tính cả quỹ đạo); hiểu vai trò của ReID appearance embedding giúp giảm FN và giữ tracklet khi xe bị che khuất.
- Điều tôi đã kiểm lại độc lập trước khi nộp: Chạy `check_mot_labels.py` (0 lỗi trên cả 2 clip), kiểm tra hash SHA-256 pre-gold bằng `lock_pre_gold.py`, đối chiếu và phân tích đủ 5 câu hỏi trong `reports/REPORT.md`.
