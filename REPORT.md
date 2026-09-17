# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602259
- Ngày / CVAT local: 17/09/2026 — CVAT chạy local trên máy cá nhân (`http://localhost:8080`), trình duyệt Chrome
- Công cụ đã dùng: gợi ý tự động có sẵn trong CVAT (auto-annotation / SAM) để tạo mask ban đầu, sau đó tự kiểm và sửa lại mask trong editor

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

Không có task nào export lỗi. Format đúng theo hợp đồng của starter: `easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage` xuất **Segmentation mask 1.1** (có `labelmap.txt`, `SegmentationClass/`, `SegmentationObject/`); `medium_instance`, `hard_panoptic`, `cp1_holes`, `cp2_slice`, `cp5_occlusion` xuất **COCO 1.0** (`annotations/instances_default.json`, mask dạng RLE). Đã chạy `python scripts/inspect_submissions.py`: cả 9 ZIP đều `[OK]`, tên lớp trong mọi file xuất khớp từng chữ với `classes.json` của đúng task (ví dụ `traffic sign` giữ dấu cách ở `cp3_thin`). Số object đã xuất: `medium_instance` 77, `hard_panoptic` 63, `cp1_holes` 7, `cp2_slice` 16, `cp5_occlusion` 24.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: `medium_instance` / `000000181542.jpg` — chiếc xe hai bánh ở **nửa dưới, hơi lệch phải khung hình**; trong file xuất nó là object `id=1` (object đầu tiên trong danh sách Objects của job), bbox `x≈361, y≈246, w≈167, h≈129`.
- Class và quy tắc tôi dùng để chọn biên: tôi chọn class **`motorcycle`** chứ không phải `bicycle` vì nhìn rõ khối động cơ và yếm xe nằm giữa hai bánh — đây là dấu hiệu tôi dùng để tách xe máy khỏi xe đạp trong danh sách class của task. Quy tắc biên: **chỉ vẽ phần nhìn thấy, không đoán phần bị che.** Phần đuôi xe bên phải bị vật phía trước che nên tôi dừng mask ngay tại ranh vật che thay vì kéo dài theo hình dung về chiếc xe đầy đủ — đối chiếu sau buổi cho thấy mask của tôi hẹp hơn vùng thật khoảng 25 px ở cạnh phải, đúng phía tôi đã chủ động dừng lại. Ở phía dưới, tôi cắt tại mép lốp tiếp đất, không lấn sang bóng đổ trên mặt đường.
- Nếu dùng gợi ý sau đó: sau khi tự chốt class và ranh nói trên, tôi mới bật gợi ý tự động cho các object còn lại. Gợi ý làm tốt ở vật lớn, tương phản rõ — các mask xe buýt, xe tải và ô tô gần máy quay khớp ground truth ở IoU 0.81–0.94, nên tôi giữ sau khi phóng to kiểm ranh và đối chiếu class trong danh sách Objects. Ngược lại, gợi ý **sai có hệ thống ở vật nhỏ, ở xa**: mask hay tràn sang nền và bóng đổ, và hay đếm nhầm số người trong nhóm đứng sát nhau. Chỗ tràn ra nền tôi xóa lại bằng eraser rồi Save. Nhìn lại, việc tôi tin gợi ý ở nhóm vật nhỏ vẫn chưa đủ chặt — hệ quả cụ thể ghi ở mục 4.
- Nếu không dùng gợi ý: không áp dụng — tôi có dùng gợi ý, nhưng chỉ sau khi tự vẽ và tự chốt object đầu tiên ở trên.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: `hard_panoptic` — file nộp `submissions/hard_panoptic.zip`. Lỗi nằm ở khâu đóng gói bản nộp, không nằm ở mask trên CVAT.
- Lỗi thuộc loại: **khác — sai nội dung gói nộp / export**.
- Bằng chứng tôi nhìn thấy: mở `hard_panoptic.zip` ra kiểm trước khi nộp thì ngoài `annotations/instances_default.json` còn có **5 file ZIP lạ nằm lồng bên trong**: `cp2_slice.zip`, `cp3_thin.zip`, `cp4_curb.zip`, `cp5_occlusion.zip`, `cp6_coverage.zip`. Kích thước file vì thế phồng lên 169 KB trong khi phần annotation thật chỉ khoảng 68 KB. Nguyên nhân là tôi đã nén nhầm nhiều file trong `submissions/` vào chung một gói thay vì đóng riêng từng task.
- Quy tắc và hành động sửa: quy tắc là **một ZIP = đúng một task, đúng một format**, giữ nguyên nội dung CVAT xuất ra chứ không tự thêm bớt. Tôi đóng gói lại `hard_panoptic.zip` chỉ còn `annotations/instances_default.json` và kiểm lại phần annotation: **không đổi** — vẫn 2 ảnh, 63 object, cùng class và cùng số lượng từng class như trước khi sửa.
- Sau sửa đã Save và export lại chưa? Rồi. Dữ liệu trên CVAT giữ nguyên trạng thái đã Save; gói nộp đã đóng lại và kiểm bằng `python scripts/inspect_submissions.py`, kết quả `[OK] hard_panoptic: hard_panoptic.zip` với 63 annotation dạng RLE.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): tôi đã chạy scorer trên máy sau khi nhận ground truth ba tier. Vì đây là lỗi đóng gói nên metric **không đổi** trước/sau sửa — `hard_panoptic` vẫn PQ 0.441 → 16.1/30. Tôi ghi lại để thấy rõ ranh giới: sửa gói nộp chỉ bảo đảm coach chấm được đúng file, không tự làm mask tốt hơn. Kết quả ba tier hiện tại: `easy_semantic` mIoU 0.808 → 18.1/20 (per-class IoU: road 0.918, sky 0.973, building 0.820, sidewalk 0.735, vegetation 0.594; coverage 91.6%); `medium_instance` mean matched IoU × recall = 0.557 → 11.2/32 (matched IoU 0.761, P@0.5 0.68, R@0.5 0.73); `hard_panoptic` PQ 0.441 → 16.1/30 (SQ 0.651, RQ 0.555); **tổng 45.4 / 82**. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `medium_instance` / `000000181542.jpg` — nhóm người đi bộ nhỏ ở nền sau, phía trái–giữa ảnh (quanh `x≈60, y≈104`, cao khoảng 24 px) | (a) mỗi bóng người nhỏ là **một instance riêng** và phải vẽ đủ; hay (b) ở kích thước này ranh giữa hai người kề nhau không còn đọc được nên **bỏ qua, không đoán** | Ở zoom lớn tôi vẫn tách được đầu và vai của vài người nên đã theo (a) và vẽ 16 `person` cho ảnh này. Đối chiếu ground truth: chỉ có 13 người thật, trong đó 8 mask của tôi khớp ở IoU ≥ 0.5, còn **8 mask thừa và 5 người bị bỏ sót**. Cùng kiểu lệch lặp ở hai ảnh còn lại (`000000373353.jpg` thiếu 5 người, `000000458325.jpg` thiếu 4) | Tôi **giữ hướng (a)** nhưng rút kinh nghiệm: phải phóng to và tự đếm trước khi chốt, thay vì tin gợi ý tự động ở vùng vật nhỏ. Hỏi coach: với người cao dưới ~25 px và bị chồng lấn, lớp ưu tiên **đủ số instance** (recall) hay **chỉ vẽ khi ranh còn đọc được** (precision)? Metric `mean matched IoU × recall` phạt cả hai chiều nên tôi cần một ngưỡng rõ |
| 2. `hard_panoptic` — dải vỉa hè ở cả `000000350023.jpg` và `000000460147.jpg`, tại chỗ vỉa hè tiếp giáp lòng đường | (a) đặt ranh `road`–`sidewalk` tại **bó vỉa / phần nền nâng cao** theo chức năng; hay (b) đặt theo **ranh đổi màu mặt nhựa**, vì dưới bóng râm màu vỉa hè và màu đường gần như nhau | Tôi đã theo (a) khi vẽ, nhưng kết quả chấm cho thấy mask `sidewalk` **không khớp ở cả hai ảnh**: PQ `sidewalk` = 0.000 (0 khớp, 2 mask thừa, 2 vùng bỏ sót), `road` cũng chỉ PQ 0.433. Tức là tôi chọn đúng nguyên tắc nhưng kéo dải vỉa hè **quá hẹp / lệch** so với vùng thật | Hỏi coach: chỗ bó vỉa bị xe đỗ hoặc bóng râm che, `sidewalk` nên dừng tại **mép bó vỉa còn nhìn thấy** hay phủ tiếp **phần vỉa hè suy ra sau vật che**? Quy tắc “chỉ vẽ phần nhìn thấy” và yêu cầu phủ hết vùng của panoptic đang kéo tôi về hai phía ngược nhau đúng ở vùng này |
| 3. `hard_panoptic` / `000000460147.jpg` — hàng xe cỡ lớn ở giữa ảnh; và `medium_instance` / `000000181542.jpg` — vật hai bánh ở góc dưới–trái (`x≈0, y≈507`, bị mép ảnh cắt) | (a) xe thân cao, thùng kín là `truck`; hay (b) là `bus`. Ở ca thứ hai: vật dưới góc là một `bicycle` riêng, hay chỉ là phần của `motorcycle` đã vẽ | Ở `000000460147.jpg` tôi gán 5 `truck` và 0 `bus`, ground truth có **4 `truck` và 2 `bus`** — tôi nhầm ít nhất một xe buýt thành xe tải; tôi cũng chia 19 `car` trong khi thật có 14, và **bỏ sót toàn bộ 3 `person` cùng 3 `motorcycle`** trong ảnh này. Ở `000000181542.jpg` tôi gán 1 `bicycle` ở góc dưới–trái nhưng ground truth **không có `bicycle` nào** trong ảnh đó | Tôi ghi nhận đây là **lỗi chọn class ở vật cỡ lớn và ở vật bị mép ảnh cắt**; chưa sửa lại trên CVAT vì đã qua thời điểm chốt ZIP, nên không ghi là đã sửa. Hỏi coach: khi chỉ thấy một phần thân xe (bị mép ảnh cắt hoặc bị che), lớp dựa vào dấu hiệu nào để chốt `bus` với `truck` — số hàng cửa sổ, chiều cao thùng — và nếu vẫn không chắc thì nên bỏ trống hay chọn class gần nhất? |
