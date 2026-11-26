# Báo cáo Day 13 — Hà Thanh Tùng, 2A202603036

## 1. Kết luận trước
- Họ tên: Hà Thanh Tùng.
- Mã số: 2A202603036.
- Task: `D13-2A202603036` — http://localhost:8080/tasks/30.
- 10 frame của mình:s01f19, s01f37, s02f01, s02f55, s03f02, s03f33,
  s04f02, s04f56, s05f13, s05f42.
- Hệ điều hành:Windows 11.
- Trình duyệt: Microsoft Edge.
- Phiên bản CVAT: 2.81.0.  
- Tôi đã gán 60 cuboid trên 10 frame; các frame s02f01, s02f55, s04f56, s05f13 và s05f42 có ít nhất, mỗi frame 5 hộp. Tôi ưu tiên vật gần và rõ. Selfcheck cuối có 0/60 hộp bị cờ. Có 55 hộp trong 50 m; riêng s03f33 có 4 hộp trong phạm vi, chưa đạt mức 5 hộp/frame. Tôi tự tin về class và hướng, còn yếu về kích thước và đáy.

- Cách dùng CVAT: Tôi giữ chuột trái để xoay cảnh, chuột phải để dời cảnh và lăn chuột để phóng to, thu nhỏ; xoay khung 3D lớn để nhìn từ trên xuống.
- Vẽ và chỉnh một cuboid (Shape, nháy đúp, Top/Side/Front):Tôi chọn Cuboid → Shape, nháy đúp để đặt hộp; chỉnh tâm, dài, rộng và yaw trong Top, rồi kiểm đáy và chiều cao trong Side/Front.
- Bật mũi tên hướng (Cuboid orientation) và xem cuboid trên ảnh camera (`preview`):Tôi bật Appearance → Cuboid orientation để xem hướng hộp, rồi chạy python scripts/lab.py preview và mở ảnh trong work/xem/ để đối chiếu cuboid với ảnh camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f19-3d`, `t1-s01f19-top` và `t1-s01f37-top` ,`t1-s01f37-3d`(ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f19 | Trong ảnh top mới: phía trước chếch trái lên; +x chếch trái xuống; +y chếch phải xuống | Có vẻ tương đối phẳng; khoảng 1,7 m dưới LiDAR là mức tham khảo, chưa đo riêng | Xe có hộp ở khoảng 53,49 m; cần xác nhận lại trên ảnh | Xe con, xe buýt | Có xe con phía sau và hai bên | Các cụm điểm xa, thưa chưa xác định rõ loại vật |
| s01f37 | Trong ảnh top mới: phía trước chếch trái lên; +x chếch trái xuống; +y chếch phải xuống | Có vẻ tương đối phẳng; khoảng 1,7 m dưới LiDAR là mức tham khảo, chưa đo riêng | Xe có hộp ở khoảng 55,69 m; cần xác nhận lại trên ảnh | Xe con, xe lớn ở xa trên camera phía trước | Có xe con phía sau và bên cạnh | Các cụm điểm xa, thưa khó nhận dạng |

3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên:
- Camera cho biết: (1) màu sơn của xe; (2) chi tiết bề ngoài như kính và đèn xe; (3) màu sắc, hình dạng vạch kẻ và mũi tên trên mặt đường.

- LiDAR cho biết: (1) khoảng cách tới vật theo mét; (2) hình dạng và kích thước 3D của vật; (3) độ cao tương đối của vật và mặt đường so với cảm biến.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f01 | Đầu xe hướng về mép trên ảnh. | Xe buýt sát bên trái che một phần cảnh; các xe phía trước có thể che nhau. |
| s02f55 | Đầu xe hướng về mép trên ảnh. | Lan can nằm trước một phần thân xe ở làn đối diện, cần phân biệt điểm của lan can và xe. |
| s03f02 | Đầu xe hướng về mép trên ảnh. | Nhiều xe gần nhau, xe phía trước che khuất một phần xe phía sau. |
| s03f33 | Đầu xe hướng về mép trên ảnh. | Xe hai bánh có cụm điểm nhỏ; các vật xa khó nhận dạng hơn vật gần. |
| s04f02 | Đầu xe hướng về mép trên ảnh. | Xe lớn sát bên phải bị cắt ở mép một số ảnh camera, cần đối chiếu nhiều góc. |
| s04f56 | Đầu xe hướng về mép trên ảnh. | Hai xe phía trước nằm gần nhau; xe sát bên phải chỉ hiện một phần ở một số camera. |
| s05f13 | Đầu xe hướng về mép trên ảnh. | Có xe đi ngang ở giao lộ; cây bụi che một phần thân dưới của một số xe. |
| s05f42 | Đầu xe hướng về mép trên ảnh. | Xe rất gần bị cắt ở mép ảnh; xe phía sau che nhau một phần, cần xem thêm camera bên. |
## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f19, 1 | Car | Gần hướng −y, phù hợp xe cùng chiều trên camera. | Đáy cao hơn mặt đường ước tính 0,01 m. | Đã đối chiếu preview và phiếu tự kiểm. | 4,100 × 1,812 × 1,488 m. Cả ba chiều trong khoảng sedan/hatchback. |
| (b) xe buýt hoặc xe tải | s01f19, 8 | Bus | Phù hợp xe cùng chiều trên cam01, cam03. | Đáy cao hơn mặt đường ước tính 0,11 m. | Đã đối chiếu cam01, cam03 và phiếu tự kiểm. | 11,980 × 2,586 × 3,109 m. Cả ba chiều trong khoảng xe buýt thường. |
| (c) người đi bộ hoặc xe hai bánh | s03f33, 6 | Cyclist | Gần hướng −y; đầu/đuôi khó phân biệt trên ảnh. | Đáy cao hơn mặt đường ước tính 0,06 m. | Đã đối chiếu cam03 và phiếu tự kiểm. | 1,834 × 0,790 × 1,541 m. Cả ba chiều trong khoảng xe máy có người. |



## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f01 / t4-1-bong-che.png | Xe buýt gần có thể chắn tia LiDAR. | Vùng phía sau xe buýt bên trái thưa điểm. | Bật occluded cho vật bị che; bao phần suy luận được. |
| 2. Thưa ở xa | s03f33 / t4-2-thua-o-xa.png | Khoảng cách lớn làm mật độ điểm giảm. | Xe ở 33,53 m có 95 điểm; xe ở 4,83 m có 3.803 điểm. | Chỉ vẽ khi đủ cơ sở; nếu không thì ghi LOW_VISIBILITY. |
| 3. Phản xạ thấp / ít điểm bất thường | s05f13 / t4-3-phan-xa-thap.png | Phản xạ, góc chiếu hoặc che khuất có thể làm giảm điểm. | Hai xe cùng tầm 15,5 m có 591 và 1.343 điểm. | Đối chiếu camera và cụm điểm trước khi chỉnh hộp. |
| 4. Điểm lạ | s01f37 / t4-4-diem-la.png | Có thể do nhiễu hoặc phản xạ gián tiếp. | Có điểm ở z ≈ −9,17 m, sâu dưới mặt đường tham khảo. | Xoay nghiêng để kiểm tra; không gán thành vật. |


## 5. Số từ cuboid của tôi (Bước 5)
Số liệu từ `submission/D13-2A202603036-bangA.csv` và `submission/D13-2A202603036-tukiem.csv`. Đơn vị khoảng cách và kích thước: m; yaw: °. Khoảng cách 3D r tính từ x, y, z trong CSV và làm tròn đến 0,01 m.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f19, 1, Car | −1,081 | 1,488 | −1,825 | 4,75 | 4,88 | 267,6 | Gần mặt đường; ground_gap = 0,01 m. |
| s01f19, 2, Car | −0,893 | 1,516 | −1,651 | 9,83 | 9,88 | 267,6 | Gần mức đường tham khảo −1,7 m. |
| s01f19, 3, Car | −1,165 | 1,455 | −1,893 | 16,00 | 16,04 | 92,5 | Gần mức đường tham khảo −1,7 m. |

(a) Cuboid gần nhất trong bảng A là Car `s03f02`, idx 1, có `dist_m` = 3,76 m và r ≈ 3,89 m. Cuboid xa nhất là Car `s03f33`, idx 3, có `dist_m` = 68,01 m và r ≈ 68,02 m. Hộp xa nhất này ngoài phạm vi 50 m; nếu chỉ xét trong phạm vi bài, xa nhất là Car `s01f37`, idx 2, có `dist_m` = 37,71 m và r ≈ 37,72 m.

(b) Car `s01f19`, idx 1 có kích thước 4,100 × 1,812 × 1,488 m, cả ba chiều nằm trong khoảng tham chiếu của sedan/hatchback. Cyclist `s03f33`, idx 6 có kích thước 1,834 × 0,790 × 1,541 m, cả ba chiều nằm trong khoảng xe máy có người của bảng 4.3.

(c) Car `s01f19`, idx 1 ở phía trước, cùng chiều có yaw 267,6°, lệch 2,4° so với 270°. Car `s01f19`, idx 3 ở phía trước, ngược chiều có yaw 92,5°, lệch 2,5° so với 90°.

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (points_in) |
|---|---|---|
| s03f33, 1, Car | 4,83 | 3.803 |
| s01f19, 7, Car | 13,97 | 718 |
| s02f55, 1, Car | 20,34 | 272 |
| s03f33, 2, Car | 33,53 | 95 |

Ở s03f33, xe xa gấp khoảng 7 lần có số điểm chỉ bằng 2,5% xe gần. Điều này phù hợp xu hướng càng xa càng ít điểm trong slide.

Sau tự kiểm, tôi đã hạ đáy hộp bị lơ lửng và chỉnh chiều cao hộp quá thấp. Chạy lại sync còn 0/60 hộp bị cảnh báo, không có cờ giữ nguyên. Có 5 hộp ngoài phạm vi 50 m; riêng s03f33 còn thiếu 1 hộp trong phạm vi theo yêu cầu.
Đổi kiểm: chưa thực hiện; chưa có MSSV và kết quả đối chiếu để ghi.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a) Nhãn 3D dùng mét để mô hình học vị trí và kích thước thật của vật; kích thước theo pixel thay đổi theo khoảng cách và góc nhìn. Vật thưa điểm cần quy tắc riêng để tránh tạo nhãn từ suy đoán thiếu cơ sở.

(b) Tôi dùng ChatGPT để hỗ trợ thao tác CVAT, hiểu hệ tọa độ LiDAR, ý nghĩa số liệu trong bảng A và cách xử lý cảnh báo. Tôi đối chiếu hướng dẫn với ảnh camera và point cloud trước khi chỉnh cuboid.

(c) Tôi còn khó xác định ranh giới của vật xa, thưa điểm và hướng đầu của xe hai bánh.
