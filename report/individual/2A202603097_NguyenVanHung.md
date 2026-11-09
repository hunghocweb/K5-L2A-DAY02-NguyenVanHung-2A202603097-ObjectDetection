# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Văn Hùng
- MSSV: 2A202603097
- Email: hunghocweb2709@gmail.com
- Lớp: 2A
- Link repo bài làm: https://github.com/hunghocweb/K5-L2A-DAY02-NguyenVanHung-2A202603097-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1OO_eJMWWNSrfWW1WU5z1iAMpHoBjSRJ4

## 2. Schema và mini guideline

Schema gồm bốn lớp: `car=0`, `truck=1`, `bus=2`, `van=3`. Theo [mini guideline](../../GUIDELINE_MINI.md), mỗi xe có một box ôm sát phần nhìn thấy. Xe đỗ, bị che hoặc cắt khung vẫn được gán nhãn nếu còn đủ dấu hiệu nhận diện; box không vượt ra ngoài ảnh.

Ba ca mơ hồ đã ghi trong guideline:

- `drive_001.jpg`: xe trắng bị cột đèn che một phần vẫn được gán `car` vì còn đủ dấu hiệu nhận diện.
- `drive_020.jpg`: xe ở mép phải bị nhòe và cắt khung được gán `car`; box dừng ở rìa ảnh.
- `drive_040.jpg`: xe minivan sáng màu phía dưới xe tải đỏ được chọn là `van` vì có thân hộp, khoang hành khách liền cabin.

Phần cần giảng viên làm rõ thêm là cách phân biệt minibus và van khi xe ở xa hoặc bị che nhiều, không nhìn rõ cửa và khoang hành khách.

## 3. Annotation và self-QC

- Số file nhãn train: **50 file**, tổng cộng **1.050 box**.
- Kết quả checker: **50/50 file hợp lệ**; không có lỗi cấu trúc cần sửa. Checker chỉ kiểm tra định dạng và tọa độ, không xác nhận đúng loại xe hay đủ vật thể.
- Danh sách ảnh đã rà: `drive_001.jpg`, `drive_006.jpg`, `drive_012.jpg`, `drive_018.jpg`, `drive_020.jpg`, `drive_024.jpg`, `drive_028.jpg`, `drive_035.jpg`, `drive_040.jpg`, `drive_049.jpg`.
- Khi rà nhãn: `drive_028.jpg` có hai box gần trùng trên xe đỗ bên phải, nhưng mang hai lớp `car` và `truck`. Trường hợp này chưa được sửa trong bộ nhãn hiện tại.
- Commit khóa nhãn ban đầu: `5c7bcbb`.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 477 | — |
| FP | 573 | — |
| FN | 176 | — |
| Precision | 0.4543 | — |
| Recall | 0.7305 | — |
| F1 | 0.5602 | — |
| mean_iou_of_matches | 0.8502 | — |
| passed | false | — |

Kết quả chấm tại IoU 0.50 được lưu trong [annotation_evaluation.json](../../outputs/annotation_evaluation.json). Chưa rework bộ nhãn chính nên không có số liệu sau sửa. Mean IoU 0.8502 chỉ tính trên các box đã ghép đúng; FP và FN cần được đối chiếu với ảnh để xác định lỗi bỏ sót, sai lớp hay lệch box.

## 5. Kết quả YOLO

- Runtime: Google Colab Free, GPU Tesla T4; Ultralytics **8.4.208**, Torch **2.14.1+cu130**.
- Tham số: giữ nguyên notebook với `yolo26n.pt`, 50 epoch, `imgsz=640`, `batch=-1`, seed 20260909. AutoBatch chọn batch 45; `REPO_URL` trỏ tới repo bài làm.
- Best checkpoint: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.2796 |
| Recall | 0.3700 |
| mAP50 | 0.3497 |
| mAP50–95 | 0.2434 |

Các chỉ số lấy từ `model_eval_metrics.json` của lần chạy Colab, làm tròn đến bốn chữ số thập phân. Hai thư mục kết quả gồm [10 ảnh dự đoán eval](assets/eval_predictions/) và [log, biểu đồ YOLO](assets/yolo26_runs/), trong đó có [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv) ghi đủ 50 epoch.

**1. Lớp nào có recall thấp nhất? Ảnh nào minh họa?**

Theo bảng đánh giá trên Colab, `bus` có recall thấp nhất, bằng **0**. Trong [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg), xe buýt lớn ở giữa ảnh bị dự đoán thành `car 0.35`. Đây là ví dụ mô hình nhận sai xe buýt; tuy nhiên, [nhãn eval của ảnh](../../dataset/labels/eval/drive_052.txt) ghi xe này là ID 3 (`van`), nên ví dụ không giải thích trực tiếp recall của lớp `bus` trong bảng chấm.

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, box đang gặp vấn đề gì?**

Trong lần chạy này, mAP50 là **0.3497**, mAP50–95 là **0.2434**, chênh khoảng **0.1063**. Khi tăng ngưỡng IoU, box lệch vị trí, lấy thừa nền hoặc chỉ bao một phần xe dễ bị tính sai. Cần kiểm tra các cạnh box có bám sát phần xe nhìn thấy hay không, nhất là xe nhỏ, bị che và cắt khung. Chênh lệch hai chỉ số chưa đủ để kết luận nhãn train sai.

**3. Có lỗi phân lớp nào liên quan tới guideline không?**

Lỗi xe buýt bị nhận thành `car` ở `drive_052.jpg` là trường hợp cần rà theo guideline. Xe có thân dài, nhiều cửa sổ và đang ở trạm xe buýt; theo định nghĩa lớp, đây là `bus`. Cần kiểm tra cách áp dụng hai lớp `bus` và `car` trong nhãn train. Ảnh dự đoán cho thấy lỗi phân lớp, nhưng chưa đủ căn cứ khẳng định lỗi này bắt nguồn từ guideline.

**4. Vì sao không dùng 10 ảnh eval để train dù chúng có nhãn?**

Đưa eval vào train sẽ làm mô hình học từ chính những ảnh dùng để đánh giá, khiến kết quả không phản ánh đúng khả năng nhận diện ảnh mới. Notebook dùng 10 ảnh eval để validation mỗi epoch, chọn `best.pt`, rồi chạy val/predict. Vì tham gia chọn checkpoint, đây là tập validation, không phải test độc lập. Tập train giữ nguyên 50 ảnh.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Khởi động Colab | Báo “Failed to assign a backend”, “no backends available”; Colab thông báo đang gặp lỗi cấp runtime | Kết nối lại, chạy Run all sau khi có GPU T4 | Hoàn tất 50 epoch, đánh giá 10 ảnh eval và tải `day2_colab_outputs.zip` |

## 7. Tự đánh giá theo rubric

Đối chiếu [rubric](../../RUBRIC.md), bài đã có 50 file nhãn đúng định dạng, guideline với ba ca mơ hồ, kết quả gold QC và kết quả YOLO. Notebook đã chạy đủ 50 epoch, giữ nguyên 50 ảnh train và 10 ảnh eval; báo cáo có các chỉ số và ảnh dự đoán.

Về gold QC, mean IoU **0.8502** cao hơn mức 0.70, còn F1 **0.5602** thấp hơn mức 0.80 trong rubric. Chưa có kết quả rework. Việc rà 10 ảnh và chạy checker chưa đủ để khẳng định tất cả vật thể đều được gán đủ, đúng lớp và có box sát.

## 8. Công cụ AI và tài liệu tham khảo

Sử dụng ChatGPT/Codex để hỗ trợ đọc lỗi Colab, đối chiếu nhãn và số liệu, góp ý phân tích ảnh và biên tập báo cáo. Các nội dung đã kiểm tra lại gồm kết quả checker, JSON gold QC, bốn chỉ số model và loại xe trong ảnh minh họa.

Tài liệu tham khảo: [GUIDE.md](../../GUIDE.md), [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md), [RUBRIC.md](../../RUBRIC.md), [SUBMISSION.md](../../SUBMISSION.md) và [notebook của bài](../../notebooks/day2_annotation_to_yolo.ipynb). Không sử dụng tài liệu ngoài repo.
