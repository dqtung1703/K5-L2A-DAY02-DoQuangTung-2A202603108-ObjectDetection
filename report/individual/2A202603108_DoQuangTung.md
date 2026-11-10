# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Đỗ Quang Tùng
- MSSV: 2A202603108
- Email: 26ai.tungdq@vinuni.edu.vn
- Lớp: K5-L2A
- Link repo bài làm: https://github.com/dqtung1703/K5-L2A-DAY02-DoQuangTung-2A202603108-ObjectDetection.git
- Link Colab đã chạy:https://colab.research.google.com/drive/10WfhZ2XuJpEXVnOHgJcumRPJu7t0hSRx?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: Đạt cấu trúc: 50/50 file nhãn hợp lệ.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_001,..,drive_050.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: 1 số ảnh bỏ xót xe nhỏ, box lỏng, xe van bị gán thành car. 
- Commit khóa nhãn ban đầu: eea68a7 ("Initial annotation labels (locked)").

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |483 |425 |
| FP |672 |280 |
| FN |170 |228 |
| Precision |0.4182 |0.6028 |
| Recall |0.7397 |0.6508 |
| F1 |0.5343 |0.6259 |
| mean_iou_of_matches |0.8488 |0.8561 |
| passed |false |false |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

Rework đã thực hiện:
    Lần 1: Rà soát lại toàn bộ ảnh, xóa những box xe nhỏ ở xa.Kết quả: FP giảm nhưng TP giảm từ 483 xuống 406, nghĩa là một phần box đã xóa là xe có trong gold.
    Lần 2 và 3: Rà soát lại toàn bộ ảnh.
    Dừng rework vì nguyên nhân chính của F1 thấp không nằm ở chất lượng box
Sau nhiều vòng rework, mean IoU ổn nhưng F1 không tăng. Kiểm tra theo từng lớp trên nhãn nộp:
Số box mỗi lớp  (bạn | gold | đúng lớp):
  car     566 |  533 |  423
  truck    25 |   46 |    0
  bus      76 |   20 |    0
  van      38 |   54 |    0

car ghép đúng 423/566 box, nhưng truck, bus, van đều có 0 box đúng lớp. Có 531 cặp box đúng vị trí (IoU ≥ 0.5, không tính lớp), trong đó 108 cặp sai lớp, và các lỗi xoay vòng một chiều: bus→van 47, van→truck 29, truck→bus 19 (car→truck 9, còn lại 4).

Kiểm tra nhãn của mình. data.yaml, README và CHECKPOINTS đều ghi 0 car, 1 truck, 2 bus, 3 van và "không đổi thứ tự ID lớp". Em kiểm tra file drive_029.txt: hai xe buýt đôi lớn đều có ID 2, đúng schema và khớp với CVAT.

Phép thử. Trên một bản sao tạm thời (không sửa nhãn nộp), em đổi ID 1→2, 2→3, 3→1 rồi chấm lại với gold:

| Chỉ số | Nhãn nộp | Bản thử đổi ID |
|---|---:|---:|
| TP |425 |520 |
| FP |280 |185 |
| FN |228 |133 |
| Precision |0.6028 |0.7376 |
| Recall |0.6508 |0.7963 |
| F1 |0.6259 |0.7658 |
| mean_iou_of_matches |0.8561 |0.8607 |
| Số cặp sai lớp |108 |13 |

Số box mỗi lớp  (bạn | gold | đúng lớp):
  car     566 |  533 |  423
  truck    38 |   46 |   29
  bus      25 |   20 |   19
  van      76 |   54 |   47

Sai lớp còn lại 13 box.Phép thử được chạy bằng một script nhỏ (không đưa vào repo), và nhãn nộp không bị thay đổi.
## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU Tesla T4 (14913 MiB, CUDA:0), Ultralytics 8.4.208, Torch 2.14.1+cu130, Python 3.13.16.
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi. Giữ nguyên `yolo26n.pt`, epochs=50, imgsz=640, batch=-1 (AutoBatch tự chọn batch 45), seed=20260909, optimizer=auto (Ultralytics chọn AdamW, lr=0.00125).
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.3156 |
| Recall |0.3066  |
| mAP50 |0.2740  |
| mAP50–95 |0.2006  |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
Lớp `bus` có recall thấp nhất (R = 0, P = 0, mAP50 = 0.008). Eval chỉ có 2 đối tượng bus, nên mỗi đối tượng đã chiếm 50% recall và số liệu rất không ổn định. Trong ảnh dự đoán, model xuất hiện nhiều box bus (drive_053 có 3, drive_058 có 2, drive_052/056/060 mỗi ảnh 1; tổng 8 box) trong khi gold chỉ có 2 bus. Do đó phần lớn box bus của model là sai vị trí hoặc sai lớp, ví dụ box `bus 0.85` ở drive_053.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
Khi đó model tìm đúng xe nhưng box chưa khít, nên IoU thấp ở các ngưỡng cao. Cần kiểm tra các cạnh box (lỏng/chặt, lệch), box gộp hai xe, và xe bị che hoặc cắt khung có tuân thủ quy tắc "ôm phần nhìn thấy". Trong bài này cả hai chỉ số đều thấp (0.274 và 0.201), nên vấn đề chính là bỏ sót và sai lớp hơn là độ khít box. Riêng lớp `car` thì mAP50 = 0.738 còn mAP50–95 = 0.521, tức tụt khoảng 0.22 khi siết ngưỡng IoU, cho thấy vẫn còn box chưa khít ở các ảnh đông xe và xe nhỏ như drive_057, drive_058.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
Lỗi car ↔ van là rõ nhất. Lớp van chỉ đạt P = 0.243, R = 0.250, mAP50 = 0.115 trên 12 đối tượng. Trong ảnh dự đoán, model gán `van 0.99` cho chiếc xe đỏ dạng sedan ở drive_051 và `van 0.98` cho chiếc xe đỏ ở drive_054, trông giống car hơn. Điều này liên quan quy tắc guideline "van/minivan mơ hồ với bus hoặc car thì không tự chọn, ghi case ID và hỏi". Nếu tôi gán van/car không nhất quán trong 50 ảnh train thì model sẽ học nhầm ranh giới này. Ngoài ra truck (R = 0.2) và bus (R = 0) rất ít mẫu trong eval, còn car chiếm 94/113 đối tượng (khoảng 83%), nên có mất cân bằng lớp.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
Nếu train bằng ảnh eval thì model đã "nhìn thấy" đáp án, số đo trở nên quá lạc quan và không phản ánh khả năng tổng quát hóa (rò rỉ dữ liệu). Trong notebook này, 10 ảnh eval còn làm validation ở mỗi epoch để tính mAP và chọn `best.pt`. Vì checkpoint được chọn dựa trên chính tập này nên nó không phải test set độc lập, và số liệu có thể lạc quan hơn thực tế.
## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
|Gán nhãn CVAT, 17:47 ngày 09/11 |Tooltip "Press undefined to draw again", phím N không chạy |Tìm trong Settings → Shortcuts, kiểm tra bộ gõ, focus |Dùng chuột để vẽ box, dùng Ctrl+1..4 đổi lớp |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.
Đã có: 50 nhãn, JSON đánh giá trước/sau rework, Colab đã chạy, chỉ số model, mean IoU đạt (0.856).
Chưa đạt: F1 = 0.6259 < 0.80, dù mean IoU đã đạt. Đây là bước kiểm tra annotation chưa hoàn thành.
Phân tích nguyên nhân: trước rework vẽ thừa rất nhiều (FP = 672); sau rework recall giảm (0.74 → 0.65), có thể do xóa nhầm box đúng.
Ghi rõ điều chưa kiểm được (nếu có): Chưa reivew được các ảnh khớp kém như drive_008, drive_009, drive_021, drive_042.

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.

Claude (Anthropic): hỏi cách dùng CVAT và Ultralytics, đọc lỗi, viết các script chẩn đoán nhỏ (so khớp theo lớp, hoán vị ID trên bản sao, đếm box lỏng) để phân tích kết quả chấm; góp ý cách diễn đạt báo cáo và guideline. Các script này không nộp. Phần tôi tự kiểm tra: đối chiếu ID trong file với CVAT, rà từng ảnh, quyết định sửa lớp và xóa hoặc thêm box, chạy evaluator và đọc kết quả.
Ultralytics yolo26x.pt: gán nháp 42 ảnh, sau đó tôi tự review và sửa trong CVAT.
CVAT (local), Ultralytics, Google Colab, GitHub.
Tài liệu tham khảo: README, GUIDE, RULES, CHECKPOINTS, RUBRIC, SUBMISSION của repo đề bài.
