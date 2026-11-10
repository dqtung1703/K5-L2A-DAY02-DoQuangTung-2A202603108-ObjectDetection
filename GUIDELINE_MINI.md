# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe con,sedan,SUV,taxi,xe bán tải |Bán tải/cabin nhỏ vẫn là car |
| `truck` |Xe tải chở hàng,xe đầu kéo |Có thùng hàng/rơ móc lớn |
| `bus` |Xe buýt,minibus chở khách |Thân dài,nhiều cửa sổ,chở khách |
| `van` |Van,minivan chở hàng hoặc chở người |Nhỏ hơn bus, lớn hơn car thường |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: ôm sát phần thân xe nhìn thấy, không lấy mặt đường, trời, xe bên cạnh.
- Vật thể bị che khuất: vẫn gán box quanh phần nhìn thấy nếu nhận diện được lớp.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa, không đoán phần ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: không tự đoán, ghi case ID và hỏi lab coach.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_024` |Xe 3 bánh |Không gán. |Schema chỉ có 4 lớp, mô tô/xe máy không gán. |
| `drive_009` |Xe xanh lá thân ngắn có cửa sổ ở bên trái: minibus hay van? |Không tự đoán, không gán nếu không xác định chắc lớp |Schema: minibus chở khách là bus, van/minivan là van; ca van/minivan mơ hồ với bus thì ghi và hỏi |
| `drive_041` |Xe trắng có thùng phía sau ở giữa ảnh: xe bán tải hay xe tải nhỏ? |Không tự đoán, không gán nếu không xác định chắc lớp |Schema: bán tải/cabin nhỏ thuộc car, xe tải chở hàng thuộc truck |
