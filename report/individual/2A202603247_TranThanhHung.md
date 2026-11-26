# Báo cáo Day 13 — <Trần Thành Hùng>, <2A202603247>

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.


## 1. Kết luận trước

Bài thực hành  task `D13-2A202603247` trên CVAT, tài khoản `tranthanhhung1812`, hệ điều hành Windows, CVAT 2.83.0 và 10 frame:

`s01f04`, `s01f35`, `s02f20`, `s02f54`, `s03f04`, `s03f47`, `s04f12`, `s04f36`, `s05f12`, `s05f30`.

Bảng A có 104 cuboid. Frame có ít cuboid nhất là `s01f35` với 5 cuboid; frame này có ít vật rõ hơn. Phiếu tự kiểm ghi cảnh báo cho 1/104 cuboid. Phần tin cậy nhất là kích thước và hướng của các xe gần, rõ; phần còn yếu nhất là độ chính xác đáy và kích thước của vật xa, vật thưa và một số vật che khuất.

Trong CVAT, tôi xoay khung 3D sang góc nhìn từ trên xuống để xác định vị trí và yaw, sau đó chọn Top/Side/Front để chỉnh tâm, kích thước và đáy. Cuboid được tạo bằng Shape, bật Cuboid orientation để xem hướng mũi tên và đối chiếu bằng lệnh `preview`.


## 2. Đọc frame (Bước 2)

Ảnh: `t1-s01f04-3d.png`, `t1-s01f04-top.png`, `t1-s01f35-3d.png`, `t1-s01f35-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường | Vật xa nhất đọc được | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f04 | Phía trước là hướng −y, ở phía dưới ảnh nhìn từ trên xuống; +x sang trái, +y hướng ra sau | Đường tương đối phẳng, LiDAR cao khoảng 1,7 m | Car `s01f04,10`, cách 34,96 m | Xe con, xe tải, xe khác nhau theo hướng | Xe ở hai bên và phía sau chỉ hiện trong camera nhìn tương ứng | Một số cụm xe xa có ít điểm và khó xác định ranh giới |
| s01f35 | Phía trước là hướng −y, ở phía dưới ảnh; +x sang trái, +y hướng ra sau | Đường tương đối phẳng, mặt đường thấp hơn LiDAR khoảng 1,7 m | Car `s01f35,5`, cách 46,90 m | Car, Truck và Cyclist | Cyclist và xe ở hai bên đường không nằm trong khung cam01 | Vật ở vùng xa và một cụm điểm cạnh biên đường chưa đủ cơ sở |

Ba thông tin chỉ camera cho biết:

- Màu sắc và hình dáng bên ngoài của xe.
- Đầu xe, đuôi xe và hướng di chuyển có thể đối chiếu trực quan.
- Loại vật và trạng thái che khuất có thể nhận ra từ nhiều góc camera.

Ba thông tin chỉ LiDAR cho biết:

- Khoảng cách và tọa độ chính xác bằng mét.
- Hình dạng điểm 3D ở mọi hướng 360°.
- Vật được che hoặc chỉ còn vài điểm LiDAR ở xa.


Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ |
|---|---|---|
| s02f20 | Dưới ảnh, theo hướng −y | Có xe đi ngược chiều và các xe ở hai bên |
| s02f54 | Dưới ảnh, theo hướng −y | Xuất hiện xe ở khoảng xa trên 30 m |
| s03f04 | Dưới ảnh, theo hướng −y | Có nhiều xe chụm nhau ở gần tâm, cần tách từng cuboid |
| s03f47 | Dưới ảnh, theo hướng −y | Có nhiều xe hai bánh ở hai bên |
| s04f12 | Dưới ảnh, theo hướng −y | Có Bus ở xa và một vùng bị che ở bên trái |
| s04f36 | Dưới ảnh, theo hướng −y | Có nhiều xe xa hơn 30 m |
| s05f12 | Dưới ảnh, theo hướng −y | Có Car, Truck, Cyclist và Pedestrian phân bố rộng |
| s05f30 | Dưới ảnh, theo hướng −y | Truck ở 61,93 m, xa ngoài vùng ưu tiên 50 m |


## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| Xe con gần | s02f20,4 | Car | Có; yaw 92,5° và hướng xe phù hợp ảnh | Có; z đáy −1,840 m | Có | 4,321 × 2,058 × 1,701 m, phù hợp SUV/minivan |
| Xe buýt hoặc xe tải | s04f12,10 | Bus | Có; yaw 83,2° phù hợp hướng xe | Có; z đáy −1,964 m | Có | 12,500 × 3,200 × 3,687 m, phù hợp xe buýt thường |
| Người hoặc xe hai bánh | s05f12,19 | Pedestrian | Hướng 80,1° được căn theo hướng/đối tượng | Có; z đáy −1,462 m | Có | 0,600 × 0,694 × 1,800 m, phù hợp người lớn |

Ở vòng hiệu chuẩn, mức lệch chủ yếu là kích thước và tâm hộp ở vật xa, do khó xác định mép ngoài khi điểm thưa. Cả lớp thống nhất phải căn theo hình dạng điểm trong ba view và đối chiếu ảnh camera thay vì nhìn bằng cảm giác.

Các vật bị bỏ qua: không ghi nhận trường hợp đủ rõ ràng để phân loại là UNCERTAIN_CLASS hoặc LOW_VISIBILITY trong dữ liệu báo cáo hiện có. Không có vật nào được ghi thêm bằng hình thức ngoại lệ trong phần này.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán theo guideline |
|---|---|---|---|---|
| 1. Bóng che | s04f12 `t4-1-bong-che.png` | Xe Bus hoặc xe lớn có thể che khuất vật phía sau (**giả thuyết**) | Vùng phía sau xe có ít hoặc không có điểm, trong khi hai bên vẫn có cụm dày hơn | Bật occluded và ước lượng phần suy luận được thay vì cắt cuboid theo vùng trống |
| 2. Thưa ở xa | s04f36, `t4-2-thua-o-xa.png` | Điểm ít do khoảng cách 51,00 m làm cường độ và mật độ điểm giảm (**giả thuyết**) | Cuboid có 55 điểm trong phiếu tự kiểm | Chỉ vẽ khi có khoảng 15–20% thể tích hoặc đủ cơ sở từ camera; không đủ thì ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s01f35, idx 4; `t4-3-phan-xa-thap.png` | Vật tối màu, kính hoặc góc phản xạ có thể phản xạ kém (**giả thuyết**) | Cyclist cách 11,02 m nhưng chỉ có 402 điểm | Không thay kích thước theo màu; giữ kích thước theo hình dạng LiDAR và camera |
| 4. Điểm lạ | s01f04; `t4-4-diem-la.png` | Ở rìa point cloud của frame s01f04 có một cụm điểm rời, không thấy vật tương ứng trên camera; có thể là nhiễu hoặc sai số đo (giả thuyết), nên không gán thành cuboid | Không gán cụm điểm này thành vật; giữ cuboid trên vật thật hoặc bỏ qua |


## 5. Số từ cuboid của tôi (Bước 5)

Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

 Cuboid | z | h | z đáy | dist_m | r | yaw_deg | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| s01f35,3,Truck | −0,264 m | 2,448 m | −1,488 m | 15,01 m | 15,01 m | 90,2° | Phiếu tự kiểm ghi đáy thấp hơn mặt đường khoảng 0,52 m, có dấu hiệu chìm |
| s02f20,7,Car | −0,993 m | 1,741 m | −1,864 m | 28,05 m | 28,05 m | 94,8° | ground_gap_m 0,30 m, gần ranh giới lơ lửng/chạm đường |
| s03f04,9,Car | −0,702 m | 1,701 m | −1,553 m | 16,56 m | 16,57 m | 272,6° | ground_gap_m 0,28 m, nằm ở ranh giới ±0,30 m |

Cuboid gần nhất là `s03f04,6,Car`, `dist_m = 3,78 m`, tọa độ `(3,071; −2,211; −0,979) m`, nên:

`r = √(3,071² + (−2,211)² + (−0,979)²) ≈ 3,88 m`.

Cuboid xa nhất trong bảng A là `s05f30,12,Truck`, `dist_m = 61,93 m`, tọa độ `(−3,364; 61,835; 0,820) m`, nên:

`r = √((−3,364)² + 61,835² + 0,820²) ≈ 61,94 m`.

Vật đi bộ/xe hai bánh:

- `s02f20,9,Pedestrian`: dài 0,585 m, rộng 0,589 m, cao 1,569 m.
- Các kích thước đều nằm trong khoảng người lớn: dài 0,4–0,9 m, rộng 0,5–0,9 m, cao 1,50–1,95 m.

Xe con:

- `s02f20,4,Car`: dài 4,321 m, rộng 2,058 m, cao 1,701 m.
- Tất cả ba kích thước thuộc nhóm SUV/minivan trong bảng 4.3: dài 4,4–5,3 m, rộng 1,80–2,10 m, cao 1,65–2,10 m.
- Dài thực tế 4,321 m nhỏ hơn một chút so với ngưỡng 4,4 m của SUV, nhưng vẫn hợp lý với xe con nhìn theo điểm cloud.

Xe cùng chiều phía trước tiêu biểu `s04f36,3,Car` có yaw 272,6°, gần mức 270°. Xe ngược chiều tiêu biểu `s01f35,3,Truck` có yaw 90,2°, gần mức 90°.

Mật độ theo khoảng cách:

| Cuboid | dist_m | Số điểm |
|---|---:|---:|
| s02f20,4,Car | 3,91 m | 3627 |
| s05f30,2,Car | 6,34 m | 2659 |
| s02f54,3,Car | 10,33 m | 1146 |
| s01f04,3,Car | 23,01 m | 311 |

Số điểm giảm rõ khi khoảng cách tăng. Khoảng cách từ 3,91 m lên 23,01 m gần gấp 6 lần thì số điểm giảm từ 3627 xuống 311 điểm, gần 12 lần. Xu hướng này phù hợp với đường cong mật độ ở slide bài giảng: xe con còn khoảng 1968 điểm ở 0–10 m, 718 điểm ở 10–20 m, 214 điểm ở 20–30 m và 72 điểm ở 30–40 m.

Phiếu tự kiểm chỉ ghi cảnh báo đối với `s01f35,3,Truck`: đáy thấp hơn mặt đường 0,52 m. Cảnh báo này được giữ nguyên vì chưa có bằng chứng rõ từ hai frame hoặc ảnh cập nhật cho thấy cách chỉnh sẽ làm hộp chính xác hơn.


## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D được đo bằng mét vì model cần biết kích thước, vị trí và khoảng cách thực của vật để dự báo chuyển động hoặc va chạm, không thể thay bằng số pixel phụ thuộc độ phân giải và góc camera. Vật thưa điểm cần quy tắc riêng vì diện tích điểm nhìn thấy có thể không đại diện cho toàn bộ vật; vẽ hộp cần suy luận từ camera, hình dạng còn lại và mức tối thiểu đủ cơ sở.

Tôi không sử dụng AI để tạo cuboid, đo kích thước hoặc quyết định class.

Điểm còn chưa chắc là trạng thái đáy của một số cuboid khi mặt đường dốc hoặc ước lượng quanh hộp bị nhiễu. Một cuboid xa ngoài 50 m, `s05f30,12,Truck`, không thuộc vùng ưu tiên chấm và cần kiểm tra lại trước khi dùng làm bằng chứng vị trí.