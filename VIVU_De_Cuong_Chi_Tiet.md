# VIVU Web — Đề cương Chi tiết (v3.0, bản làm một mình trong 6 tuần)

| | |
|---|---|
| **Phiên bản** | 3.0 — thu hẹp từ v2.0 (mobile, đội 8–10 người) |
| **Ngày** | 10/10/2026 |
| **Trạng thái** | Bản nháp, chờ tác giả xác nhận các câu hỏi mở (README mục 4) |

---

## 1. Tổng quan

- **Tên:** VIVU — *Đi đâu cũng có bạn.*
- **Hình thái:** **ứng dụng web** responsive (ưu tiên màn hình điện thoại, chạy tốt trên máy tính). Không còn app iOS/Android.
- **Định nghĩa một câu:** web giúp người trẻ tìm **bạn đồng hành cho một hoạt động cụ thể ở Đà Nẵng**, trong đó **AI** làm ba việc nhìn thấy được: *gợi ý hoạt động hợp với bạn và giải thích vì sao*, *trợ lý ViVi trả lời có dẫn nguồn*, và *bộ lọc an toàn cho nội dung người dùng viết*.
- **Không phải:** ứng dụng hẹn hò, mạng xã hội đa dụng, nền tảng bán tour.

### Vì sao thu hẹp
v2.0 ước tính cần đội 8–10 người trong ~27 tuần. Một người có 6 tuần (~150–180 giờ làm việc) phải đổi cách làm: **giữ đúng một vòng lặp cốt lõi, làm sâu phần AI, bỏ mọi thứ chỉ cần cho vận hành quy mô lớn**.

## 2. Giá trị cốt lõi và điểm nhấn của đề tài

| Điểm nhấn | Cách thể hiện cho người xem/hội đồng |
|---|---|
| AI hoạt động thật, không phải "hộp đen" | Trang **AI Lab**: xem xếp hạng thay đổi khi chỉnh trọng số, xem ViVi đã truy hồi những đoạn nào, thử bộ lọc an toàn trực tiếp |
| AI có đo lường | Bốn bộ đánh giá (EV-1…EV-4) với bảng chỉ số, so sánh với baseline, nêu rõ hạn chế |
| AI có trách nhiệm | Nhãn "AI", giải thích lý do, từ chối khi thiếu dữ liệu, không suy đoán thuộc tính nhạy cảm |
| UI/UX chất lượng cao | Hệ thống thiết kế riêng, chuyển động có chủ đích, đạt tiêu chuẩn tiếp cận AA |

## 3. Đối tượng và persona (rút gọn)

Người Việt 18–30 tuổi, đang ở hoặc đến Đà Nẵng, muốn tham gia hoạt động cùng người mới.

| Persona | Dùng để |
|---|---|
| **Linh, 21, sinh viên** — thích cà phê yên tĩnh, chụp ảnh, hơi ngại | Kiểm chứng gợi ý theo phong cách giao tiếp |
| **Minh, 27, nhân viên văn phòng** — thích phượt, camping, sẵn sàng làm Host | Kiểm chứng luồng tạo hoạt động |
| **Hà, 25, khách du lịch một mình** — hỏi nhiều về địa điểm, lo an toàn | Kiểm chứng ViVi và các nhắc nhở an toàn |

Bộ 8 persona thử nghiệm (dùng cho tài khoản demo và bộ đánh giá EV-1) nằm ở `CAC_THUAT_TOAN_SU_DUNG.md` mục 8.

## 4. Phạm vi

### 4.1 Làm (Must) — vòng lặp cốt lõi + AI
1. Đăng nhập nhanh (email magic link + **tài khoản demo một chạm**), onboarding ngắn.
2. Khám phá hoạt động, **xếp hạng "Dành cho bạn" bằng AI-1 (Match Engine)**, kèm giải thích.
3. Xem chi tiết, **tạo hoạt động**, **tham gia/rút**, "Hoạt động của tôi".
4. **ViVi (AI-2):** hỏi đáp có dẫn nguồn, gọi công cụ tìm hoạt động, gợi ý câu bắt chuyện.
5. **Safety Guard (AI-3)** áp dụng lên mọi nội dung người dùng viết.
6. **Điểm uy tín (AI-4, minh bạch, không dùng học máy)** và màn giải thích.
7. **AI Lab** và **báo cáo đánh giá** (EV-1…EV-4).
8. Landing page, responsive, trạng thái tải/rỗng/lỗi, tiếp cận AA.

### 4.2 Nếu còn thời gian (Should/Could)
Chat nhóm theo hoạt động (realtime), bản đồ Leaflet, đa dạng hóa kết quả (MMR), độ tương hợp với người cùng tham gia, AI soạn mô tả hoạt động, Host điểm danh + đánh giá, nút báo cáo, xóa dữ liệu của tôi, đăng nhập Google, trang quản trị tối giản.

### 4.3 Bỏ khỏi bản này (đã có trong v2.0, chỉ tham chiếu)
| Bỏ | Lý do | Xử lý thay thế |
|---|---|---|
| App iOS/Android, Expo | Chọn thuần web | Web responsive |
| Feed, bài viết, bình luận, tim | Không phục vụ AI cốt lõi | — |
| Chat 1-1, message request, nhóm cộng đồng | Tốn công, ít giá trị demo | Chat theo hoạt động (Should) |
| OTP SMS, selfie/CCCD, eKYC | Chi phí, pháp lý, không liên quan AI | Email + nhãn "Demo" |
| SOS, chia sẻ chuyến, check-in GPS | Cần mobile/vị trí liên tục | Mẹo an toàn + Safety Guard + chọn địa điểm công cộng có sẵn |
| Push notification, admin web đầy đủ | Không cần cho demo | Thông báo trong trang; Supabase Dashboard để xem báo cáo |
| Nhiều thành phố, đa ngôn ngữ, chế độ tối | Phạm vi | Chỉ Đà Nẵng, tiếng Việt |

### 4.4 Ngoài phạm vi pháp lý
Đây là bản **demo học thuật**. Nếu sau này mở cho công chúng, phải thực hiện lại phần pháp lý, an toàn và vận hành ở `v2.0` (Kiến trúc mục 8; Kế hoạch An toàn). Bản này không thu thập SĐT, vị trí hay giấy tờ.

## 5. Tiêu chí thành công (đo được ở cuối tuần 6)

| Nhóm | Tiêu chí | Ngưỡng |
|---|---|---|
| Hoàn thành | Tất cả hạng mục Must chạy trên bản triển khai công khai | 100% |
| AI-1 | Hybrid so với baseline trên EV-1 (NDCG@5) | Cao hơn mọi baseline, hoặc phân tích trung thực nếu không |
| AI-2 | Retrieval hit@4 / groundedness / từ chối đúng (EV-2) | ≥ 0,85 / ≥ 0,90 / ≥ 0,80 |
| AI-3 | Macro-F1 / tỷ lệ chặn nhầm nội dung bình thường (EV-3) | ≥ 0,80 / ≤ 5% |
| UI/UX | Lighthouse trên điện thoại: Accessibility / Performance | ≥ 95 / ≥ 85 |
| Độ ổn định | Demo 7 phút chạy trọn vẹn 3 lần liên tiếp, có phương án dự phòng khi AI lỗi | Đạt |
| Tài liệu | Báo cáo đánh giá AI (số liệu, hạn chế) | Có |

Các ngưỡng là **mục tiêu ban đầu**, không phải cam kết; kết quả thật được báo cáo dù đạt hay không.

## 6. Mô hình kinh doanh
Ngoài phạm vi 6 tuần. Ý tưởng dài hạn (đối tác địa điểm có nhãn, gói Host) giữ nguyên ở v2.0 Đề cương mục 7.

## 7. Giả định chính
| ID | Giả định | Nếu sai |
|---|---|---|
| A1 | Làm một mình, khoảng 25–30 giờ/tuần, hạn chót ~22/11/2026 | Cắt theo "đường cắt" ở Kế hoạch Dự án mục 4 |
| A2 | Có thể gọi một API mô hình ngôn ngữ và một API embedding (có khóa, có hạn mức chi phí) | Dùng mô hình mở chạy cục bộ cho embedding; ViVi chuyển sang chế độ rút gọn |
| A3 | Dữ liệu là **dữ liệu minh họa** tự tạo (30 người dùng, ~40 hoạt động, ~60 đoạn kiến thức) | Ghi rõ trong báo cáo, gắn nhãn `is_seed` |
| A4 | Triển khai bằng Next.js + Supabase + Vercel (gói miễn phí/giá rẻ) | Xem ADR trong Kiến trúc |
| A5 | Chỉ Đà Nẵng, tiếng Việt | — |

## 8. Rủi ro chính

| ID | Rủi ro | Xác suất / Tác động | Giảm thiểu |
|---|---|---|---|
| RK-01 | Thiếu thời gian vì phạm vi vẫn rộng | Cao / Cao | Đường cắt rõ, đóng băng tính năng cuối tuần 5 |
| RK-02 | API AI chậm, lỗi hoặc hết hạn mức đúng lúc demo | TB / Cao | Bộ nhớ đệm phản hồi cho kịch bản demo, chế độ dự phòng theo luật, ghi hạn mức |
| RK-03 | Kết quả đánh giá không đẹp (bộ dữ liệu nhỏ, nhãn do tác giả gán) | Cao / TB | Báo cáo trung thực, nêu hạn chế, tách dev/test |
| RK-04 | Embedding tiếng Việt kém với văn bản ngắn/không dấu | TB / TB | Chuẩn hóa văn bản, thử 2 mô hình trong tuần 3, giữ phần cấu trúc trong công thức |
| RK-05 | ViVi "bịa" thông tin | TB / Cao | RAG chặt, ngưỡng tương đồng, bắt buộc trích nguồn, từ chối khi thiếu |
| RK-06 | Prompt injection qua nội dung người dùng | TB / TB | Tách dữ liệu và chỉ dẫn, công cụ có lược đồ kiểm tra, ViVi không truy cập dữ liệu riêng |
| RK-07 | Giao diện "đẹp nhưng chậm" | TB / TB | Ngân sách hiệu năng, kiểm Lighthouse hằng tuần |
| RK-08 | Tự đánh giá quá lạc quan | Cao / TB | Ước lượng có hệ số 1,5, kiểm tra tiến độ cuối mỗi tuần |

## 9. Lộ trình tóm tắt
Chi tiết ở `VIVU_Ke_Hoach_Du_An.md`.

| Tuần | Ngày | Mốc |
|---|---|---|
| W1 | 12–18/10 | Nền tảng, hệ thống thiết kế, CSDL, dữ liệu minh họa, đăng nhập |
| W2 | 19–25/10 | Onboarding, hoạt động (liệt kê, chi tiết, tạo, tham gia) |
| W3 | 26/10–01/11 | **AI-1 Match** + EV-1 |
| W4 | 02–08/11 | **AI-2 ViVi** + EV-2 |
| W5 | 09–15/11 | **AI-3 Guard**, điểm uy tín, **AI Lab**, EV-3 — *đóng băng tính năng cuối tuần* |
| W6 | 16–22/11 | Hoàn thiện UI, kiểm thử, báo cáo, triển khai, luyện demo |

## 10. Danh mục tài liệu v3.0
Xem `00_README_Muc_Luc.md`.
