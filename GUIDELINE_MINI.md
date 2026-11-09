# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, sedan, SUV, taxi và xe bán tải. | Xe bán tải thuộc `car`; xe đang đỗ vẫn được gán theo loại xe. |
| `truck` | Xe tải chở hàng và xe đầu kéo. | Có cabin và thùng chở hàng hoặc kết cấu đầu kéo; không bao gồm xe bán tải thuộc `car`. |
| `bus` | Xe buýt và minibus chở khách. | Xe buýt có thân dài, nhiều cửa sổ và cửa lên xuống; van/minivan thuộc `van`. |
| `van` | Xe van, minivan chở hàng hoặc chở người. | Thân xe dạng hộp, khoang hàng hoặc khoang hành khách liền với cabin; phân biệt với xe con và xe buýt. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: mỗi xe có một box ôm sát phần nhìn thấy, không lấy thừa nền và không gộp nhiều xe vào một box.
- Vật thể bị che khuất: vẫn gán nếu phần nhìn thấy đủ nhận diện lớp; box bao quanh phần nhìn thấy, không suy đoán phần bị che.
- Vật thể bị cắt bởi rìa ảnh: gán phần còn nhìn thấy nếu nhận diện được lớp; box dừng đúng rìa ảnh, không vượt biên.
- Vật thể rất nhỏ/mơ hồ: không tự đoán lớp khi chưa đủ dấu hiệu nhận diện; ghi tên ảnh, vị trí và hỏi giảng viên, không tự đặt ngưỡng kích thước.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001.jpg` | Xe trắng ở nửa phải ảnh bị cột đèn che một phần thân. | Gán `car`, vẽ một box bao quanh phần xe nhìn thấy, không suy rộng phần bị che. | Phần còn thấy đủ nhận diện xe con; xe bị che một phần vẫn được gán nhãn. |
| `drive_020.jpg` | Xe tối màu ở mép phải, nửa dưới ảnh, bị cắt khung và nhòe chuyển động. | Gán `car` cho phần xe còn thấy; cạnh phải của box dừng ở rìa ảnh. | Phần thân và đuôi xe còn đủ nhận diện xe con; không suy đoán phần nằm ngoài ảnh. |
| `drive_040.jpg` | Xe màu sáng, thân cao, ngay phía dưới xe tải đỏ ở gần giữa ảnh, dễ nhầm với minibus. | Gán `van`, vẽ box riêng ôm sát xe. | Xe có dạng minivan với thân hộp và khoang hành khách liền cabin; minivan thuộc `van` theo schema. |
