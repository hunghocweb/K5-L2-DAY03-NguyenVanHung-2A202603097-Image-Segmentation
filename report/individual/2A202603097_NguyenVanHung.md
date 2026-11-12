# Báo cáo thực hành Day 3 — Segmentation

- Mã học viên theo lớp: **2A202603097**.
- Ngày / CVAT local: **11–12/11/2026 / http://localhost:8080**.
- Công cụ đã dùng: CVAT, Polygon, YOLO26s-seg/sem, SAM2.1; bản gợi ý trước dùng YOLO26 detect và SegFormer-B5.

## 1. Bài đã nộp

| Task            | File ZIP đúng tên                                            | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --------------- | ------------------------------------------------------------ | -----------------: | ---------------------------: |
| easy_semantic   | [easy_semantic.zip](../../submissions/easy_semantic.zip)     |              3 / 3 |                           20 |
| medium_instance | [medium_instance.zip](../../submissions/medium_instance.zip) |              3 / 3 |                           32 |
| hard_panoptic   | [hard_panoptic.zip](../../submissions/hard_panoptic.zip)     |              2 / 2 |                           30 |
| cp1_holes       | [cp1_holes.zip](../../submissions/cp1_holes.zip)             |              1 / 1 |                            3 |
| cp2_slice       | [cp2_slice.zip](../../submissions/cp2_slice.zip)             |              1 / 1 |                            3 |
| cp5_occlusion   | [cp5_occlusion.zip](../../submissions/cp5_occlusion.zip)     |              1 / 1 |                            3 |
| cp3_thin        | [cp3_thin.zip](../../submissions/cp3_thin.zip)               |              1 / 1 |                            3 |
| cp4_curb        | [cp4_curb.zip](../../submissions/cp4_curb.zip)               |              1 / 1 |                            3 |
| cp6_coverage    | [cp6_coverage.zip](../../submissions/cp6_coverage.zip)       |              1 / 1 |                            3 |
| **Tổng tối đa** | **9 ZIP**                                                    |        **14 / 14** |                      **100** |

Đã Save và export đủ 9 task; semantic dùng Segmentation mask 1.1, instance/panoptic dùng COCO 1.0.

## 2. Một quyết định trước khi dùng gợi ý

- **Ảnh, vị trí và object Medium đầu tiên tự vẽ:** `medium_instance/000000458325.jpg`, người áo xám đứng trên ván trượt ở giữa tiền cảnh. Polygon quanh người này được tự vẽ trong CVAT trước khi xem bất kỳ gợi ý tự động nào cho object đó.
- **Class và quy tắc dùng để chọn biên:** `person`. Biên vẽ sát phần nhìn thấy của đầu, áo, tay, quần và giày, rồi dừng ở đế giày. Ván trượt và cái bóng dưới chân không phải người nên để ra ngoài, theo quy tắc chỉ vẽ phần nhìn thấy của vật, không lấn sang nền hay bóng.
- **Khi dùng gợi ý sau đó:** bản máy gợi ý vùng người này hơi tràn sang các vật bên cạnh, nên một chỗ bị tính cho hai vật cùng lúc. Người này được giữ là một vật riêng, phần tràn bị cắt khỏi vật bên cạnh, và đường biên đã vẽ được giữ nguyên vì bám đúng chỗ giày chạm ván. Lý do: mỗi chỗ trên ảnh chỉ nên thuộc về một vật.

## 3. Một lỗi phát hiện và sửa

- **Task/ảnh/vùng:** `cp1_holes/000000144300.jpg`, kính chắn gió phía đầu xe máy Honda.
- **Loại lỗi:** kính và khe bên trong xe bị khoét khỏi mask.
- **Dấu hiệu:** còn mảng trống bên trong đường bao kính xe.
- **Hành động:** tô lại kính/khe theo quy tắc cp1, bổ sung vào cùng mask `motorcycle`; giữ người và xe khác riêng.
- **Save/export:** đã lưu và xuất lại `cp1_holes.zip` bằng COCO 1.0.

Tự đánh giá ba tier sau đối chiếu reference: **79,7/82**. Sáu checkpoint chưa có reference riêng nên chưa có điểm.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí                                                         | Hai cách hiểu có thể                                    | Quy tắc/chứng cứ                                     | Quyết định hoặc câu hỏi cho coach                                |
| ------------------------------------------------------------------ | ------------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------- |
| `cp1_holes/000000144300.jpg`, xe máy Honda tiền cảnh               | Khoét kính/khe hoặc giữ trong mask xe                   | Quy tắc cp1 không coi kính/khe bên trong là lỗ nền.  | Giữ kính/khe trong cùng mask `motorcycle`; tách người khỏi xe.   |
| `cp4_curb/7d83710e-4697c3b2.jpg`, bó vỉa phải và nắp capo dưới ảnh | Chia theo màu hoặc bó vỉa; nắp capo là road hay vật che | Bó vỉa ngăn đường và lối đi; nắp capo che mặt đường. | Chia road–sidewalk theo bó vỉa; bỏ nắp capo khỏi road.           |
| `cp6_coverage/7daa6479-67988f3f.jpg`, xe buýt trắng MTA 2555       | Gán `car` hoặc để ngoài các lớp của task                | Đây là bus; task có `car` nhưng không có `bus`.      | Bỏ nhãn `car` sai trên thân bus; không ép vùng này vào lớp khác. |
