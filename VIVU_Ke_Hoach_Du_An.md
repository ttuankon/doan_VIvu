# Kế hoạch Dự án — VIVU Web (v3.0, làm một mình, 6 tuần)

> **Lưu ý trung thực:** ước lượng bên dưới là **thô** và giả định bạn đã quen Next.js/React và Supabase. Tổng giờ Must ≈ **157 giờ**, năng lực ≈ **165 giờ** (27,5 giờ/tuần × 6). Gần như **không có dư**, nên kế hoạch dựa vào **đường cắt** (mục 4) chứ không dựa vào hy vọng. Nếu giờ làm của bạn khác, báo tôi để chỉnh (OQ-04).

## 1. Mốc thời gian

| Tuần | Ngày | Chủ đề | Kết quả cuối tuần |
|---|---|---|---|
| **W0** | 10–11/10 | Chốt câu hỏi mở, chuẩn bị tài khoản | OQ-01…OQ-04 đã trả lời; có khóa API, dự án Vercel/Supabase |
| **W1** | 12–18/10 | Nền móng | Bản "hello" chạy trên Vercel; đăng nhập demo; schema + dữ liệu minh họa nạp bằng 1 lệnh |
| **W2** | 19–25/10 | Vòng lặp hoạt động | Hồ sơ, danh sách, chi tiết, tạo, tham gia; embedding cho dữ liệu minh họa |
| **W3** | 26/10–01/11 | **AI-1 Match** | Dành cho bạn + giải thích; EV-1 có số liệu (**Checkpoint A**) |
| **W4** | 02–08/11 | **AI-2 ViVi** | RAG + công cụ + icebreaker; EV-2 có số liệu (**Checkpoint B**) |
| **W5** | 09–15/11 | **AI-3 Guard, AI-4, AI Lab** | Guard phủ mọi đường lưu; AI Lab 4 tab; EV-3; **đóng băng tính năng** (**Checkpoint C**) |
| **W6** | 16–22/11 | Hoàn thiện, đo, bàn giao | UI/tiếp cận/hiệu năng đạt; báo cáo đánh giá; demo 3 lần liên tiếp |

Hạn chót giả định ~22/11/2026 (OQ-04).

## 2. Ngân sách giờ (chỉ hạng mục Must)

| # | Hạng mục | Giờ | Tuần |
|---|---|---|---|
| 1 | Nền tảng: repo, Next.js, Tailwind, shadcn, Supabase local, CI | 8 | W1 |
| 2 | Hệ thống thiết kế: tokens, component nền | 8 | W1 |
| 3 | CSDL, RLS, dữ liệu minh họa (24 người, 30 hoạt động, 20 địa điểm) | 12 | W1 |
| 4 | Đăng nhập + tài khoản demo | 5 | W1 |
| 5 | Onboarding + hồ sơ | 6 | W2 |
| 6 | Hoạt động: danh sách, chi tiết, tạo, tham gia, của tôi | 16 | W2 |
| 7 | Lớp adapter AI + quy trình embedding | 5 | W2 |
| 8 | AI-1 Match + giải thích + giao diện | 12 | W3 |
| 9 | EV-1: gán nhãn 96 cặp + script + chạy | 6 | W3 |
| 10 | Kho kiến thức 40 đoạn + nạp | 8 | W2–W4 |
| 11 | ViVi: RAG, stream, công cụ, icebreaker, giao diện | 14 | W4 |
| 12 | EV-2 (30 câu) + EV-4 (10 tình huống) | 5 | W4–W6 |
| 13 | Guard: luật + LLM + giao diện | 8 | W5 |
| 14 | EV-3 (60 tin nhắn) | 4 | W5 |
| 15 | Điểm uy tín + giao diện | 5 | W5 |
| 16 | AI Lab (4 tab) | 10 | W5 |
| 17 | Landing, trạng thái, chuyển động, trau chuốt | 8 | W1, W6 |
| 18 | Kiểm thử: RLS, E2E, tiếp cận, hiệu năng | 6 | W2–W6 |
| 19 | Triển khai, chế độ demo cache, luyện demo | 5 | W6 |
| 20 | Báo cáo đánh giá + hoàn thiện tài liệu | 6 | W6 |
| | **Tổng Must** | **157** | |
| | Năng lực (27,5 × 6) | 165 | |
| | **Dư** | **8 (~5%)** | |

Should/Could (chat, bản đồ, MMR, tương hợp, AI soạn mô tả, điểm danh/đánh giá, báo cáo, Google, xóa dữ liệu, admin) **không nằm trong ngân sách**; chỉ làm nếu một mốc về sớm.

## 3. Việc từng tuần (Definition of Done)

**W0 (10–11/10) — Chuẩn bị:** trả lời câu hỏi mở; tạo tài khoản Vercel, Supabase, nhà cung cấp AI; đặt **trần chi phí AI**; thử một lệnh gọi embedding và một lệnh gọi LLM với tiếng Việt.

**W1:** triển khai trang trống lên Vercel ngay ngày đầu; tokens + 8 component nền; migrations + RLS + `seed`; magic link + `/api/demo-login`; khung PG-04, PG-05 (dữ liệu thật từ DB). *DoD:* `pnpm seed` nạp đủ dữ liệu; chọn persona vào được `/discover`.

**W2:** onboarding 3–4 bước; hồ sơ; danh sách + lọc; chi tiết; wizard tạo; `join_activity`; "Của tôi"; adapter AI; embed dữ liệu minh họa; viết 20 đoạn KB đầu. *DoD:* E2E-02 xanh; SEC-01…06 xanh; hồ sơ/hoạt động minh họa có embedding.

**W3:** AI-1 (SQL cosine + 6 thành phần + giải thích) → MatchBadge/WhyPanel; gán nhãn EV-1 **trước khi xem điểm** (~2 giờ/ngày); chạy EV-1 dev → chọn trọng số → khóa → chạy test. *DoD (Checkpoint A):* "Dành cho bạn" chạy trên production; có bảng NDCG@5 cho B0–B3 và Hybrid.

**W4:** hoàn tất KB 40 đoạn; `match_kb_chunks`; luồng ViVi (Guard đầu vào tạm thời bằng luật), stream, trích nguồn, từ chối không gọi LLM; `find_activities`; icebreaker; hiệu chỉnh `τ` bằng EV-2 dev → chạy test. *DoD (Checkpoint B):* KB-4, KB-5 đạt trên production; có số liệu EV-2.

**W5:** Guard hai lớp + phủ mọi đường lưu; EV-3; điểm uy tín + màn giải thích; AI Lab 4 tab (tab Đánh giá đọc `eval_runs`); thử lỗi (mục 8 Kế hoạch Đánh giá). **Cuối tuần: đóng băng.** *DoD (Checkpoint C):* mọi Must có test; 0 lỗi S1/S2; EV-1/2/3 test đã chạy đúng một lần.

**W6:** chỉ sửa lỗi, trau chuốt UI, tiếp cận, hiệu năng, EV-4, báo cáo, `DEMO_CACHE`, quay **video demo dự phòng**, luyện demo 3 lần. *DoD:* điều kiện bàn giao ở Kế hoạch Đánh giá mục 9.

## 4. Đường cắt (kích hoạt theo mốc, không chờ đến phút chót)

Mỗi cuối tuần dành 30 phút so tiến độ với DoD. Trễ **hơn 1 ngày** so với mốc → kích hoạt cấp cắt tương ứng ngay.

| Cấp | Kích hoạt khi | Cắt |
|---|---|---|
| 0 (đã áp dụng) | Từ đầu | Mọi Should/Could |
| 1 | Cuối W2 hoặc W3 trễ | FR-ONB-04 (hồ sơ AI), EV-1 giảm còn 8 persona × 10 cặp, KB còn 30 đoạn, wizard tạo hoạt động còn form 1 trang |
| 2 | Cuối W4 trễ | Icebreaker (dùng mẫu cố định), Guard chỉ giữ lớp luật + một lời gọi LLM đơn giản, AI Lab tab Safety chỉ chạy ví dụ có sẵn |
| 3 | Cuối W5 trễ | EV-4, biểu đồ AI Lab (chỉ bảng số), `FR-LAB-05`, kiểm thử tương thích ngoài Chrome/Safari |

**Không bao giờ cắt:** AI-1 có giải thích · ViVi có dẫn nguồn và biết từ chối · Guard lớp luật phủ mọi đường lưu · AI Lab (tối thiểu tab Match và ViVi) · RLS · triển khai + phương án dự phòng · báo cáo đánh giá có nêu hạn chế.

**Quy tắc 2 giờ:** kẹt một vấn đề kỹ thuật quá 2 giờ → ghi lại, chọn cách đơn giản hơn hoặc cắt, rồi đi tiếp.

## 5. Vai trò (RACI rút gọn)

Một người đảm nhiệm mọi vai kỹ thuật. Các vai còn lại:

| Hạng mục | Bạn (tác giả) | Người hướng dẫn / hội đồng (nếu có) | Bạn bè thử nghiệm |
|---|---|---|---|
| Chốt phạm vi và đường cắt | A/R | C | I |
| Thiết kế, xây dựng, triển khai | A/R | I | I |
| Gán nhãn dữ liệu đánh giá | A/R | I | C (chấm chéo 20 mẫu EV-3, EV-4) |
| Thử nghiệm người dùng (W6) | A | I | R |
| Báo cáo đánh giá AI | A/R | C | I |
| Nghiệm thu | R | A | I |

Nếu **không** có người hướng dẫn (dự án cá nhân), cột giữa bỏ đi và "Nghiệm thu" do bạn tự chịu trách nhiệm theo điều kiện bàn giao.

## 6. Chi phí và tài khoản cần chuẩn bị (W0)

| Hạng mục | Ghi chú |
|---|---|
| Vercel, Supabase | Dùng gói miễn phí/giá rẻ; **kiểm tra giới hạn hiện hành** của từng gói trước khi dựa vào |
| API mô hình ngôn ngữ + embedding | Đặt **trần chi phí ngày** (`AI_DAILY_BUDGET`), bật cảnh báo; ước lượng: số lượt EV + lượt demo + lượt thử của bạn bè |
| Tên miền | Tùy chọn |
| Công cụ | Node, pnpm, Docker (Supabase local), Playwright |

## 7. Chuẩn bị demo

### 7.1 Kịch bản 7 phút
| Phút | Nội dung | Điều cần thấy |
|---|---|---|
| 0:00–0:45 | Landing: bài toán, định vị "không phải hẹn hò", 3 AI | Câu chuyện rõ |
| 0:45–1:45 | Vào bằng persona Linh → "Dành cho bạn" → bấm **"Vì sao?"** | Match %, lý do, phân rã điểm |
| 1:45–2:45 | Chi tiết hoạt động → **gợi ý bắt chuyện** → Tham gia | AI hữu ích trong luồng thật |
| 2:45–4:15 | ViVi: (1) câu có trong KB → có [n]; (2) câu ngoài KB → từ chối; (3) "cuối tuần có hoạt động chụp ảnh?" → thẻ hoạt động | RAG, từ chối, công cụ |
| 4:15–5:15 | Tạo hoạt động có số tài khoản → **Guard chặn** có lý do | AI an toàn |
| 5:15–6:30 | AI Lab: kéo trọng số, truy vết ViVi, bảng đánh giá + **hạn chế** | AI đo được, trung thực |
| 6:30–7:00 | Làm/không làm, hướng phát triển (v2.0) | Tầm nhìn |

### 7.2 Danh sách kiểm tra trước mỗi buổi demo
- [ ] `/api/health` trên production trả OK; DB có dữ liệu minh họa.
- [ ] Hạn mức API AI còn đủ; thử 1 lượt ViVi, 1 lượt Guard.
- [ ] `DEMO_CACHE` đã sẵn sàng (bật được trong 10 giây).
- [ ] 8 persona đăng nhập được; tài khoản demo không bị đổi dữ liệu từ buổi trước (chạy `pnpm seed:reset`).
- [ ] E2E-01…05 xanh trên production.
- [ ] Video demo dự phòng và ảnh chụp AI Lab sẵn trong máy.
- [ ] Mạng dự phòng (điểm phát từ điện thoại) đã thử.
- [ ] Trình duyệt sạch (không tiện ích, đã đăng xuất), phóng chữ 100%.

## 8. Rủi ro điều hành

| ID | Rủi ro | Biện pháp |
|---|---|---|
| RK-01…RK-08 | Xem Đề cương mục 8 | — |
| RK-09 | Kiệt sức/mất nhịp giữa chừng | 1 ngày nghỉ/tuần; mục tiêu theo ngày nhỏ; không làm đêm trước checkpoint |
| RK-10 | Phụ thuộc một người (mất máy, mất dữ liệu) | Đẩy mã lên Git hằng ngày; xuất dữ liệu minh họa ra JSON; ghi lại cách dựng lại |

## 9. Nhịp làm việc đề xuất
- Mỗi ngày: 3 mục tiêu nhỏ; kết thúc bằng một commit chạy được.
- Mỗi tuần: 30 phút rà soát tiến độ (mục 4) + 3 giờ rà soát giao diện (UI/UX mục 10).
- Mọi thay đổi phạm vi: ghi một dòng "bỏ gì để thêm gì" vào `docs/CHANGELOG.md`.
