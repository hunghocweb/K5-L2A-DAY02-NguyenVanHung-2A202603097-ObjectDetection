# Báo cáo cá nhân — Ngày 2 Object Detection

Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Văn Hùng
- MSSV: 2A202603097
- Email: hunghocweb2709@gmail.com
- Lớp: 2A
- Link repo bài làm: https://github.com/hunghocweb/K5-L2A-DAY02-NguyenVanHung-2A202603097-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1OO_eJMWWNSrfWW1WU5z1iAMpHoBjSRJ4

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md): `car=0`, `truck=1`, `bus=2`, `van=3`. Mỗi xe có một box riêng ôm sát phần nhìn thấy, dừng ở rìa ảnh khi xe bị cắt khung. Ba quyết định đã ghi trong guideline:

- `drive_001.jpg`: xe trắng bị cột đèn che một phần vẫn gán `car`, không suy rộng phần bị che.
- `drive_020.jpg`: xe tối màu bị cắt ở mép phải và nhòe chuyển động được gán `car` cho phần còn nhìn thấy.
- `drive_040.jpg`: xe màu sáng dưới xe tải đỏ được xác định là `van` theo dạng thân minivan; cần giữ quyết định này nhất quán trong nhãn.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file, tương ứng 50 ảnh train, tổng cộng 1.050 bounding box.
- Kết quả checker và lỗi đã xử lý: Chạy `python -X utf8 -B tools/check_yolo_labels.py`, kết quả đạt cấu trúc 50/50 file nhãn hợp lệ; không có lỗi định dạng cần sửa.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001.jpg`, `drive_006.jpg`, `drive_012.jpg`, `drive_018.jpg`, `drive_020.jpg`, `drive_024.jpg`, `drive_028.jpg`, `drive_035.jpg`, `drive_040.jpg`, `drive_049.jpg`; rà trực tiếp ảnh có hiển thị bounding box và ID lớp.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: `drive_020.jpg` có box ở khu vực xe đỗ dưới cầu thang lấy thừa nền và chồng lên xe bên cạnh. `drive_040.jpg` có xe minivan màu sáng phía dưới xe tải đỏ đang được gán `car`, chưa nhất quán với quyết định `van` trong mini guideline. Chưa sửa nhãn trong lần rà này.
- Commit chứa bộ 50 nhãn train và mini guideline: `5c7bcbb` — `Add 50 train annotations and completed mini guideline`. Commit này có trong lịch sử trước commit lưu kết quả gold QC `e6fdd94`.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 477 | Chưa có kết quả rework |
| FP | 573 | — |
| FN | 176 | — |
| Precision | 0.4543 | — |
| Recall | 0.7305 | — |
| F1 | 0.5602 | — |
| mean_iou_of_matches | 0.8502 | — |
| passed | false | — |

Nguồn: [annotation_evaluation.json](../../outputs/annotation_evaluation.json), đã được lưu trong commit `e6fdd94`. Chưa có kết quả sau rework.

## 5. Kết quả YOLO

- Nguồn số liệu: `model_eval_metrics.json` trong ZIP kết quả Colab, đã đối chiếu với output notebook ngày 10/11/2026 và chép đủ bốn chỉ số vào bảng dưới đây.
- Runtime: Colab Free, Tesla T4 14913 MiB; Python 3.13.16; Ultralytics 8.4.208; Torch 2.14.1+cu130.
- Cấu hình: `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1` (AutoBatch chọn 45), `device=0`, `seed=20260909`; 50 ảnh train và 10 ảnh eval. Giữ các tham số train trong notebook; điền URL repo cá nhân vào `REPO_URL`. Cell chuẩn bị chuyển `path` trong bản `data.yaml` ở Colab thành đường dẫn tuyệt đối; giữ nguyên split.
- Repo được clone trong lần chạy: commit `e6fdd94`.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không đưa checkpoint vào Git).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.2796477438723719 |
| Recall | 0.3700354609929078 |
| mAP50 | 0.34968648738257563 |
| mAP50–95 | 0.2433633966344259 |

Output đánh giá theo lớp đã lưu trong notebook:

| Lớp | Số vật thể eval | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0.399 | 0.947 | 0.825 | 0.543 |
| truck | 5 | 0.225 | 0.200 | 0.303 | 0.246 |
| bus | 2 | 0 | 0 | 0.00651 | 0.00456 |
| van | 12 | 0.494 | 0.333 | 0.264 | 0.180 |

Đã lưu cả hai thư mục [eval_predictions](assets/eval_predictions/) và [yolo26_runs](assets/yolo26_runs/) từ ZIP kết quả. [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv) có đủ 50 epoch; [args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml) lưu cấu hình thực tế. Chỉ số trong bảng là đánh giá lại best checkpoint, không phải hàng epoch cuối của CSV.

- [Đường cong train](assets/yolo26_runs/vehicle_annotation/results.png).
- [Confusion matrix trên eval](assets/yolo26_runs/vehicle_eval/confusion_matrix.png).
- [PR curve trên eval](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png).
- [Ảnh nhãn eval](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg) và [ảnh dự đoán khi val](assets/yolo26_runs/vehicle_eval/val_batch0_pred.jpg).

Ảnh dự đoán đủ 10 ảnh, từ [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg) đến [drive_060.jpg](assets/eval_predictions/visuals/drive_060.jpg), với `conf=0.25`.

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp có recall thấp nhất:** `bus`, recall bằng 0 trên hai vật thể eval. Ở [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg), nhãn eval có một bus ở vùng trên bên phải, tâm chuẩn hóa khoảng `(0.828, 0.130)`, nhưng ảnh prediction không có box `bus` nào. Không thể dùng việc model có dự đoán `bus` ở ảnh khác để kết luận recall tốt: box còn phải khớp lớp và vị trí với nhãn eval.

2. **Chênh lệch mAP50 và mAP50–95:** mAP50 là 0.3497, còn mAP50–95 là 0.2434, thấp hơn khoảng 0.1063. Khi điều kiện IoU nghiêm ngặt hơn, các box chưa khớp sát sẽ mất tính đúng. Cần đối chiếu các cạnh box, phần nền thừa, box lệch tâm, xe nhỏ hoặc bị che/cắt khung. Ví dụ tại [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg), model vẽ một box `car 0.72` lên vùng nóc xe lớn, cho thấy có dự đoán vào một phần vật thể thay vì toàn bộ phần nhìn thấy. Chênh lệch mAP riêng lẻ chưa đủ để kết luận nhãn train sai.

3. **Lỗi class có thể liên quan guideline:** ở [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg), xe lớn màu vàng bên trái được model dự đoán `bus 0.86`, trong khi nhãn eval tại vùng này là `van` (tâm khoảng `(0.138, 0.509)`). [Confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png) cũng có tám vật thể thật `van` bị dự đoán `bus` tại thiết lập đánh giá của biểu đồ. Đây là dữ kiện cần rà tính nhất quán ở ranh giới `bus`/`van`; phần self-QC đã ghi một ca `van` bị gán `car` ở `drive_040.jpg`. Tuy nhiên, chưa thể quy toàn bộ lỗi model cho guideline: dữ liệu ít và mất cân bằng lớp cũng có thể ảnh hưởng. Tập eval chỉ có 2 bus, 5 truck, 12 van so với 94 car.

4. **Không đưa eval vào train:** nếu dùng 10 ảnh eval để cập nhật trọng số rồi chấm lại trên chính chúng, kết quả sẽ không còn phản ánh khả năng xử lý ảnh ngoài tập train. Trong notebook, eval được dùng làm validation mỗi epoch và chọn `best.pt`, sau đó dùng để val/predict. Vì đã tham gia chọn checkpoint, đây là validation set, không phải test set độc lập. Giữ nguyên 50 ảnh train và 10 ảnh eval.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Colab, 09–10/11/2026 | `Failed to assign a backend`; banner thông báo lỗi provisioning từ Colab | Kết nối lại và Run all sau khi được cấp T4 | Hoàn tất 50 epoch, val/predict trên 10 ảnh eval; tải ZIP và lưu ảnh/log vào assets |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): đã có 50 file nhãn đúng cấu trúc, mini guideline với ba ca mơ hồ, JSON gold QC, link Colab, bốn chỉ số model, hai thư mục ảnh/log và phần phân tích gắn với ảnh cụ thể. Chỉ số annotation được giữ nguyên theo JSON thực tế. Chưa xác minh quyền xem Colab cho người chấm hoặc việc nộp link trên LMS.

## 8. Công cụ AI và tài liệu tham khảo

Đã dùng ChatGPT/Codex để giải thích lỗi Colab, đối chiếu tài liệu bài, trích số liệu từ JSON/log, sắp xếp assets và hỗ trợ soạn phần phân tích qua đối chiếu ảnh prediction với nhãn eval. Phần nhận xét model có hỗ trợ AI; các quan sát được dẫn tới ảnh và file kết quả cụ thể để học viên kiểm tra lại. Các ghi chép self-QC nhãn train ở mục 3 được giữ từ bản báo cáo trước. Tài liệu tham khảo gồm README, GUIDE, GUIDELINE_MINI, RUBRIC và SUBMISSION trong repo. Không đính kèm gold train hoặc thông tin đăng nhập.
