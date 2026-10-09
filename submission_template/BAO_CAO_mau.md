# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** Nguyen_Tuan_Khanh **Thành viên:** Nguyễn Tuấn Khanh - 2A202602819

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

Disclaimer: Tất cả các video đều chọn bytetrack do các tracker khác chạy quá lâu và bị timeout trên colab. Do đó ô "Đã thử nhưng loại" sẽ nêu một tracker khác mong muốn chọn nếu thời gian cho phép.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---:|---:|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | bytetrack | 0.30 | 0.50 | Phù hợp | strongsort, conf=0.30, iou=0.50 — loại vì Re-ID không cải thiện ID ổn định đáng kể so với ByteTrack |
| video_2 (phố đêm, tĩnh, rất đông) | bytetrack | 0.15 | 0.70 | Bytetrack không phù hợp do bỏ sót nhiều người ở khu vực nền gạch tối màu, đặc biệt là những người đi sát nhau, dù đã giảm conf và tăng IoU | strongsort, conf=0.15, iou=0.70 — mong muốn thử vì có Re-ID, có tiềm năng duy trì ID người ổn định hơn khi nhiều người đi sát nhau hoặc bị che khuất trong cảnh đông người ban đêm; chưa thử được do giới hạn thời gian và timeout trên Colab |
| video_3 (camera di động, ảnh nhỏ) | bytetrack | 0.15 | 0.40 | Bytetrack không tạo box ảo và tuy có bỏ sót người nhưng đó là những người ở vị trí xa, tỷ lệ xuất hiện không đáng kể. Tuy nhiên, ByteTrack dễ gặp tình trạng mất track khi nhiều người di chuyển cắt ngang nhau. Một người bị che khuất tạm thời rồi xuất hiện trở lại rất dễ bị gán nhầm ID. | botsort, conf=0.15, iou=0.40 — mong muốn thử vì có cơ chế bù chuyển động camera, có thể cải thiện việc liên kết detection giữa các frame khi camera di chuyển; chưa thử được do giới hạn thời gian và timeout trên Colab.|
| video_4 (trong nhà, camera di chuyển) | bytetrack | 0.5 | 0.50 | Video quay trong nhà nên tốc độ cắt nhau giữa người chậm hơn so với video 3, nên lựa chọn mức conf cao hơn do mọi người đứng xa nhau và dễ quan sát hơn. Nhìn chung, tình trạng tracking trong video_4 tương tự video_3, lỗi chủ yếu ở phase association gây ra bởi hiện tượng mất track và gán nhầm ID khi các đối tượng che khuất lẫn nhau.| strongsort, conf=0.50, iou=0.50 — mong muốn thử vì có Re-ID để hỗ trợ nhận diện lại người sau khi bị che khuất, giúp giảm tình trạng mất ID hoặc gán nhầm ID; chưa thử được do giới hạn thời gian và timeout trên Colab |
| video_5 (trên xe bus, giao lộ đông) | bytetrack | 0.50 | 0.70 | Bytetrack có tỷ lệ bỏ sót rất cao do hầu hết mọi human occurence đều ở khá xa và thường xảy ra va chạm với vật cản, mặc dù mức IoU đã được đặt lên cao nhất. Tình trạng ID switch và ID loss vẫn tương tự như 2 video trên| botsort, conf=0.25, iou=0.70 — mong muốn thử vì cơ chế bù chuyển động camera có thể hỗ trợ tracking trong cảnh quay rung lắc từ xe bus, đồng thời ngưỡng conf thấp hơn có thể giữ lại nhiều detection của người ở xa; chưa thử được do giới hạn thời gian và timeout trên Colab. |

## 2. Số liệu video_1

Dán bảng HOTA / MOTA / IDF1 do `scripts/evaluate_practice.py` in ra.

```
```

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

## 3. Phân tích

Ở video1, ByteTrack hoạt động phù hợp với cảnh quảng trường ngoài trời vào ban ngày, camera tĩnh và các đối tượng tương đối dễ quan sát. Qua quan sát video, cấu hình đã chọn đáp ứng được yêu cầu theo dõi người mà không cần sử dụng thêm Re-ID, trong khi StrongSORT chưa cho thấy lợi ích đủ rõ rệt so với thời gian xử lý tăng lên. Ngược lại, ở video2, ByteTrack bỏ sót nhiều người tại khu vực nền gạch tối màu, đặc biệt khi các đối tượng đi sát nhau trong cảnh đông người vào ban đêm, dù đã giảm conf xuống 0.15 và tăng iou lên 0.70. StrongSORT là phương án đáng thử hơn trong trường hợp này nhờ khả năng Re-ID, có tiềm năng duy trì ID khi người bị che khuất hoặc xuất hiện trở lại, nhưng chưa thể kết luận hiệu quả thực tế do chưa chạy thử được.

## 4. Nếu có thêm thời gian

Nếu có thêm thời gian, sẽ thử StrongSORT trên video_2 để đánh giá khả năng Re-ID trong cảnh đông người thiếu sáng, đồng thời quét các giá trị conf chi tiết hơn và xem lại những frame xảy ra bỏ sót để tìm cấu hình phù hợp nhất.
