# Đặc tả Yêu cầu Chức năng (FRS) — VIVU Web (v3.0)

**Quy ước:** Ưu tiên **M**ust / **S**hould / **C**ould. Cột *Tuần* là tuần dự kiến hoàn thành (W1…W6, xem Kế hoạch Dự án). Mã `BR-xx` ở Nghiệp vụ & Kiến trúc; `AI-x`, `EV-x` ở Thuật toán; `PG-xx` ở UI/UX. AC = tiêu chí chấp nhận.

Tổng: **Must 34 · Should 14 · Could 7.** Chỉ Must được hứa cho hạn chót.

## 1. Xác thực & phiên (AUTH)
| ID | Yêu cầu | Ưu tiên | Tuần | Tiêu chí chấp nhận |
|---|---|---|---|---|
| FR-AUTH-01 | Đăng nhập bằng email (magic link) | M | W1 | Nhập email hợp lệ → nhận liên kết → bấm vào được đăng nhập; email sai/liên kết hết hạn hiện thông báo tiếng Việt, có nút gửi lại (cách 60 giây) |
| FR-AUTH-02 | **Tài khoản demo một chạm**: chọn 1 trong 8 persona để vào ngay | M | W1 | Không cần mật khẩu; có nhãn "Demo" ở đầu trang; mỗi persona có sẵn hồ sơ, lịch sử, điểm uy tín |
| FR-AUTH-03 | Đồng ý điều khoản + xác nhận từ 18 tuổi (BR-01) | M | W1 | Chưa tích thì không tạo được hồ sơ; lưu thời điểm đồng ý |
| FR-AUTH-04 | Đăng xuất; chặn trang cần đăng nhập | M | W1 | Truy cập trang riêng khi chưa đăng nhập → chuyển về `/login`, sau đăng nhập quay lại đúng trang |
| FR-AUTH-05 | Đăng nhập Google | S | W6 | Thành công tạo hồ sơ rỗng và đi qua onboarding |

## 2. Onboarding & hồ sơ (ONB/PRO)
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-ONB-01 | Chọn sở thích: 3–8 trong 12, có bộ đếm | M | W2 | Nút Tiếp tục tắt khi < 3; không chọn quá 8 |
| FR-ONB-02 | Chọn mức giao tiếp (4 mức, emoji kèm chữ) và mục tiêu (1–3) | M | W2 | Hiển thị đủ nhãn; lưu vào hồ sơ |
| FR-ONB-03 | Viết "bạn muốn gì ở chuyến đi" 20–300 ký tự; qua Safety Guard; tạo embedding | M | W2 | Bio vi phạm bị chặn có giải thích; bio hợp lệ có embedding trong ≤ 5 giây hoặc hiển thị "đang xử lý" |
| FR-ONB-04 | Xem trước "Hồ sơ AI của bạn": các chủ đề AI rút ra từ bio | S | W3 | Hiển thị 3–5 chủ đề, cho phép bỏ chủ đề sai; không suy đoán giới tính, tôn giáo, sức khỏe |
| FR-PRO-01 | Xem/sửa hồ sơ; sửa bio làm mới embedding | M | W2 | Lưu thành công; gợi ý được tính lại ở lần tải sau |
| FR-PRO-02 | Xem hồ sơ người khác (tên, avatar, band, sở thích, bio) | S | W5 | Không hiển thị email; chỉ hiển thị band, không hiển thị số điểm |
| FR-PRO-03 | Xóa dữ liệu của tôi | C | W6 | Hồ sơ, tham gia, tin nhắn, nhật ký AI của người dùng bị xóa/ẩn danh |

## 3. Hoạt động (ACT)
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-ACT-01 | Danh sách hoạt động + lọc (danh mục, thời gian, quận, miễn phí) | M | W2 | Lọc kết hợp được; trạng thái rỗng có gợi ý "nới bộ lọc" |
| FR-ACT-02 | Chi tiết hoạt động: Host + band, địa điểm, số chỗ (thanh tiến độ), người tham gia, mẹo an toàn | M | W2 | Hiển thị "Còn N chỗ / Đã đầy"; địa điểm là nơi công cộng (BR-04) |
| FR-ACT-03 | Tạo hoạt động (3 bước: nội dung → thời gian/địa điểm → xem trước) | M | W2 | Kiểm tra BR-03; địa điểm chọn từ danh sách có sẵn; văn bản qua Guard; tạo embedding |
| FR-ACT-04 | Tham gia / rút khỏi hoạt động | M | W2 | Hai người cùng tham gia chỗ cuối → chỉ một thành công; rút < 24 giờ trước giờ bắt đầu ghi sự kiện `late_cancel` |
| FR-ACT-05 | "Hoạt động của tôi": sắp tới, đã tạo, đã qua | M | W2 | Phân tab; có trạng thái rỗng |
| FR-ACT-06 | Sửa/hủy hoạt động của Host | S | W5 | Hủy cần lý do; người tham gia thấy trạng thái "Đã hủy" |
| FR-ACT-07 | Bản đồ địa điểm (Leaflet + OpenStreetMap) ở chi tiết và danh sách | S | W6 | Ghim có tên địa điểm; không hiển thị vị trí người dùng |
| FR-ACT-08 | Host điểm danh + người tham gia đánh giá Host (1–5) sau hoạt động | C | W6 | Chỉ khi hoạt động đã kết thúc; ghi `trust_events` |
| FR-ACT-09 | AI soạn mô tả hoạt động từ 1–2 câu gợi ý của Host | C | W6 | Bản nháp có thể sửa; qua Guard trước khi lưu |

## 4. Gợi ý bằng AI — Match Engine (MAT) · AI-1
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-MAT-01 | Mục "Dành cho bạn": top N hoạt động xếp theo AI-1 kèm Match % | M | W3 | Sắp xếp giảm dần theo điểm; thời gian phản hồi ≤ 1 giây với 100 hoạt động |
| FR-MAT-02 | Giải thích mỗi gợi ý: 2–3 lý do + thanh phân rã điểm | M | W3 | Lý do dựa trên thành phần điểm thật (không bịa); có tooltip giải thích từng thành phần |
| FR-MAT-03 | Bộ lọc cứng trước khi chấm điểm (BR-07) | M | W3 | Không bao giờ gợi ý hoạt động đã đầy/đã qua/của chính mình/đã tham gia |
| FR-MAT-04 | Lưu và làm mới embedding khi hồ sơ/hoạt động đổi | M | W3 | Embedding cũ không được dùng sau khi nội dung đổi (so sánh `embedding_model` và dấu thời gian) |
| FR-MAT-05 | Đa dạng hóa kết quả (MMR) | S | W5 | Không quá 2 hoạt động cùng danh mục liên tiếp trong top 6 |
| FR-MAT-06 | Độ tương hợp với người cùng tham gia | S | W5 | Hiển thị % và lý do trên thẻ người; chỉ dùng dữ liệu hồ sơ đã công khai |
| FR-MAT-07 | Phản hồi 👍/👎 cho mỗi gợi ý, ghi nhật ký | S | W5 | Lưu `ai_feedback`; không đổi xếp hạng tức thì |

## 5. Trợ lý ViVi (VIV) · AI-2
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-VIV-01 | Khung chat ViVi, trả lời dạng streaming | M | W4 | Token đầu tiên ≤ 3 giây (p95); hiển thị trạng thái "đang tìm trong kho kiến thức" |
| FR-VIV-02 | RAG: trả lời chỉ dựa trên đoạn truy hồi, **trích nguồn [n]** | M | W4 | Mỗi câu trả lời dùng kiến thức có ít nhất 1 nguồn bấm xem được |
| FR-VIV-03 | Từ chối khi không đủ thông tin hoặc ngoài phạm vi | M | W4 | Câu hỏi ngoài kho hoặc tương đồng < ngưỡng → "ViVi chưa có thông tin về điều này" kèm gợi ý hỏi lại; không bịa |
| FR-VIV-04 | Công cụ `find_activities`: ViVi tìm hoạt động theo yêu cầu và trả về thẻ hoạt động | M | W4 | "Cuối tuần có gì đi cà phê?" → ≥ 1 thẻ hoạt động bấm vào được; tham số được kiểm tra bằng lược đồ |
| FR-VIV-05 | Gợi ý câu bắt chuyện (3 phương án, chọn giọng điệu) từ sở thích chung và tên hoạt động | M | W4 | Mỗi phương án ≤ 25 từ; chỉ dùng dữ liệu hồ sơ công khai; qua Guard |
| FR-VIV-06 | Câu hỏi mẫu (chip) và lịch sử hội thoại trong phiên | S | W4 | Chip điền sẵn câu hỏi; lịch sử mất khi đóng tab (không lưu máy chủ) |
| FR-VIV-07 | Giới hạn tần suất, thời gian chờ, phương án dự phòng | M | W4 | Quá 20 lượt/giờ → thông báo thân thiện; lỗi/timeout 15 giây → câu trả lời dự phòng, không treo giao diện |
| FR-VIV-08 | Nút "ViVi đã dùng nguồn nào?" mở phần truy vết | S | W5 | Liệt kê đoạn kiến thức, điểm tương đồng, thứ tự xếp hạng |

## 6. Safety Guard (GRD) · AI-3
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-GRD-01 | Mọi văn bản người dùng viết (bio, hoạt động, tin nhắn, câu hỏi ViVi) đi qua Guard trước khi lưu/gửi | M | W5 | Không có đường lưu nội dung nào bỏ qua Guard (kiểm tra bằng test) |
| FR-GRD-02 | Lớp luật: SĐT, số tài khoản, liên kết, từ khóa chuyển tiền/đặt cọc (có chuẩn hóa không dấu) | M | W5 | Phát hiện ≥ 95% mẫu PII trong bộ EV-3; thời gian ≤ 20 ms |
| FR-GRD-03 | Lớp mô hình ngôn ngữ: phân loại 6 nhãn, độ tin cậy, lý do | M | W5 | Trả JSON hợp lệ (có kiểm tra lược đồ); lỗi định dạng → thử lại 1 lần → dùng kết quả của lớp luật |
| FR-GRD-04 | Quyết định Cho phép / Cảnh báo / Chặn kèm giải thích tiếng Việt | M | W5 | Người dùng thấy lý do và cách sửa; không hiển thị lỗi kỹ thuật |
| FR-GRD-05 | Nút báo cáo nội dung (hoạt động, tin nhắn, người dùng) | S | W6 | Ghi vào `reports`; hiển thị cảm ơn |

## 7. Điểm uy tín (TRU) · AI-4
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-TRU-01 | Tính và hiển thị band công khai, số điểm chỉ cho chủ tài khoản | M | W5 | 4 ví dụ kiểm thử trong Thuật toán mục 5.5 khớp ±0,5 |
| FR-TRU-02 | "Điểm được tính thế nào?": 4 thành phần, cách cải thiện | S | W5 | Hiển thị đúng giá trị từng thành phần |
| FR-TRU-03 | Thành viên mới (< 3 sự kiện) hiển thị "Thành viên mới", không số | M | W5 | Đúng quy tắc BR-12 |

## 8. Chat theo hoạt động (CHT)
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-CHT-01 | Chat nhóm realtime cho người đã tham gia | S | W5 | Tin mới hiện ≤ 1 giây; người chưa tham gia không đọc/gửi được (RLS) |
| FR-CHT-02 | Chèn gợi ý câu bắt chuyện của ViVi vào khung chat | S | W5 | Bấm gợi ý điền vào ô nhập, người dùng gửi |
| FR-CHT-03 | Guard áp dụng cho tin nhắn | S | W5 | Tin bị chặn không được lưu |

## 9. AI Lab và đánh giá (LAB)
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-LAB-01 | Tab **Match**: chọn persona, xem top 10, kéo thanh trọng số → xếp hạng đổi ngay | M | W5 | Đổi trọng số không gọi lại AI (tính lại phía trình duyệt từ các thành phần điểm) |
| FR-LAB-02 | Tab **ViVi**: nhập câu hỏi → thấy truy vấn, các đoạn truy hồi + điểm, lời nhắc đã dựng, câu trả lời | M | W5 | Hiển thị đầy đủ 4 phần; đoạn dưới ngưỡng bị làm mờ |
| FR-LAB-03 | Tab **Safety**: nhập văn bản thử → luật nào kích hoạt, nhãn, độ tin cậy, quyết định | M | W5 | Có 6 ví dụ bấm nhanh (1 cho mỗi nhãn) |
| FR-LAB-04 | Tab **Đánh giá**: bảng chỉ số EV-1…EV-4, biểu đồ so sánh baseline, hạn chế | M | W6 | Số liệu lấy từ `eval_runs`, ghi ngày chạy và phiên bản cấu hình |
| FR-LAB-05 | Nhật ký AI: độ trễ, số token, tỷ lệ lỗi, tỷ lệ dùng phương án dự phòng | S | W6 | Biểu đồ theo ngày |

## 10. Hệ thống (SYS)
| ID | Yêu cầu | Ưu tiên | Tuần | AC |
|---|---|---|---|---|
| FR-SYS-01 | Landing page giới thiệu, nút "Dùng thử với tài khoản demo" | M | W1/W6 | Lighthouse đạt ngưỡng ở Đề cương mục 5 |
| FR-SYS-02 | Responsive 3 mốc (điện thoại, máy tính bảng, máy tính) | M | W1→W6 | Không cuộn ngang; đạt ở 360, 768, 1280 px |
| FR-SYS-03 | Trạng thái tải (skeleton), rỗng, lỗi, mất mạng cho mọi danh sách | M | W2→W6 | Theo UI/UX mục 7 |
| FR-SYS-04 | Tiếp cận AA: tương phản, bàn phím, trình đọc màn hình, giảm chuyển động | M | W6 | axe không còn lỗi mức nghiêm trọng/cao |
| FR-SYS-05 | **Chế độ dự phòng demo**: phản hồi AI đã ghi sẵn khi API lỗi | S | W6 | Bật bằng biến môi trường; có nhãn "phản hồi ghi sẵn" ở AI Lab |
| FR-SYS-06 | Nhãn "AI" trên mọi nội dung do AI tạo; nhãn "Dữ liệu minh họa" ở nơi cần | M | W3 | Có ở Match, ViVi, Guard, mô tả AI soạn |

## 11. Kịch bản chấp nhận (Given–When–Then)

**KB-1 Gợi ý và giải thích**
- *Given* Linh (thích cà phê, chụp ảnh, mức giao tiếp 1) đã đăng nhập,
- *When* mở `/discover`,
- *Then* thấy mục "Dành cho bạn" có ít nhất 5 hoạt động, mỗi thẻ có Match %, ≥ 2 lý do và không có hoạt động đã đầy hay của chính mình.

**KB-2 Tạo hoạt động bị Guard chặn**
- *Given* Minh đang tạo hoạt động,
- *When* mô tả có "chuyển khoản đặt cọc 500k vào STK 0123456789",
- *Then* nút Lưu bị chặn, hiển thị lý do "nhắc đến chuyển tiền" và gợi ý bỏ thông tin, hoạt động không được lưu.

**KB-3 Tham gia chỗ cuối**
- *Given* hoạt động còn 1 chỗ và hai người cùng bấm Tham gia,
- *Then* chỉ một người thành công, người còn lại thấy "Hoạt động vừa đủ chỗ".

**KB-4 ViVi có dẫn nguồn và biết từ chối**
- *When* hỏi "Quán bún chả cá ngon gần Cầu Rồng?" (có trong kho) → câu trả lời có [1], [2] bấm xem được.
- *When* hỏi "Giá vé máy bay đi Tokyo?" (ngoài kho) → "ViVi chưa có thông tin về điều này", không đưa số liệu.

**KB-5 ViVi gọi công cụ**
- *When* nhắn "Cuối tuần này có hoạt động chụp ảnh nào không?",
- *Then* ViVi trả lời ngắn kèm thẻ hoạt động từ `find_activities`, bấm vào mở đúng trang chi tiết.

**KB-6 AI Lab**
- *When* kéo trọng số "Ngữ nghĩa" về 0 trong tab Match,
- *Then* thứ tự top 10 đổi ngay và thanh phân rã điểm cập nhật, không có yêu cầu mạng tới AI.

## 12. Truy vết yêu cầu → tài liệu
| Nhóm | Thuật toán | Dữ liệu | UI | Kiểm thử |
|---|---|---|---|---|
| MAT | AI-1, EV-1 | `profiles`, `activities` | PG-04, PG-05, PG-11 | Danh giá mục 3.1 |
| VIV | AI-2, EV-2, EV-4 | `kb_chunks`, `ai_logs` | PG-09, PG-11 | Danh giá mục 3.2 |
| GRD | AI-3, EV-3 | `reports`, `ai_logs` | PG-06, PG-11 | Danh giá mục 3.3 |
| TRU | AI-4 | `trust_events`, `trust_scores` | PG-10 | Danh giá mục 3.4 |
