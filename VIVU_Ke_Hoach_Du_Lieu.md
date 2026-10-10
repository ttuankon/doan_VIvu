# Kế hoạch Dữ liệu — VIVU Web (v3.0)

Rút gọn từ ~40 bảng (v2.0 / `02_Kien_Truc_Database.md`) xuống **16 bảng** đủ cho vòng lặp cốt lõi và AI. Giữ lại: Supabase, RLS, hàm tham gia nguyên tử (atomic). Bỏ: PostGIS, thiết bị, xác thực L1/L2, SOS, bài viết/bình luận, nhóm, kết nối, thông báo đẩy.

> Kích thước vector dùng `vector(1536)` làm ví dụ; **đổi theo mô hình embedding được chọn** (OQ-02).

## 1. Quy ước
- `snake_case`, số nhiều; khóa chính `uuid` (`gen_random_uuid()`), trừ bảng tra cứu dùng `slug`.
- Thời gian `timestamptz` lưu UTC, hiển thị `Asia/Ho_Chi_Minh`.
- Mọi bảng bật **RLS**. Thao tác cần quyền đặc biệt chạy qua hàm `security definer` hoặc Route Handler dùng Service Role (khóa chỉ ở máy chủ).
- Dữ liệu minh họa luôn có `is_seed = true` (BR-14).

## 2. Danh sách bảng

| Nhóm | Bảng | Mục đích |
|---|---|---|
| Tra cứu | `interests`, `places` | 12 sở thích; ~30 địa điểm công cộng |
| Người dùng | `profiles` | Hồ sơ + embedding |
| Hoạt động | `activities`, `activity_participants`, `activity_messages`, `activity_reviews` | Vòng đời hoạt động, chat, đánh giá |
| Uy tín | `trust_events`, `trust_scores` | AI-4 |
| An toàn | `reports` | Báo cáo nội dung |
| AI | `kb_chunks`, `ai_logs`, `ai_feedback`, `eval_runs`, `ai_usage` | Kho kiến thức, nhật ký, phản hồi, kết quả đánh giá, hạn mức |

## 3. Lược đồ SQL

```sql
create extension if not exists vector;
create extension if not exists unaccent;

-- ===== Tra cứu =====
create table interests (
  slug text primary key,                 -- food, coffee, roadtrip, camping, beach, trekking,
  name text not null,                    -- photo, culture, art_music, sport, citywalk, workshop
  emoji text,
  group_slug text                        -- nhóm liên quan (cho MMR / mở rộng)
);

create table places (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  district text not null,                -- Hải Châu, Sơn Trà, Ngũ Hành Sơn, Thanh Khê, Liên Chiểu, Cẩm Lệ, Hòa Vang
  category text not null,
  lat double precision not null,
  lng double precision not null,
  description text,
  is_public_venue boolean not null default true,
  is_seed boolean not null default true
);

-- ===== Người dùng =====
create table profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  display_name text not null check (char_length(display_name) between 2 and 50),
  avatar_seed text,
  bio text check (char_length(bio) between 20 and 300),
  comm_level smallint check (comm_level between 0 and 3),
  goals text[] not null default '{}',
  interests text[] not null default '{}',          -- slug; kiểm 3–8 ở tầng ứng dụng
  home_district text,
  age_confirmed_at timestamptz,
  tos_accepted_at timestamptz,
  role text not null default 'user' check (role in ('user','admin')),
  is_demo boolean not null default false,
  is_seed boolean not null default false,
  embedding vector(1536),
  embedding_model text,
  embedding_updated_at timestamptz,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);
create index on profiles using gin (interests);

-- ===== Hoạt động =====
create table activities (
  id uuid primary key default gen_random_uuid(),
  host_id uuid not null references profiles(id) on delete cascade,
  title text not null check (char_length(title) between 5 and 80),
  description text not null check (char_length(description) between 20 and 800),
  category text not null references interests(slug),
  tags text[] not null default '{}',                -- ≤ 3 slug sở thích bổ sung
  place_id uuid not null references places(id),
  start_at timestamptz not null,
  end_at timestamptz not null,
  max_participants smallint not null check (max_participants between 2 and 12),
  approved_count smallint not null default 1,       -- Host tính là 1
  vibe smallint not null default 1 check (vibe between 0 and 3),
  cost_vnd int not null default 0 check (cost_vnd between 0 and 1000000),
  status text not null default 'open' check (status in ('open','full','completed','cancelled')),
  cancel_reason text,
  guard_label text,
  guard_confidence real,
  embedding vector(1536),
  embedding_model text,
  is_seed boolean not null default false,
  created_at timestamptz not null default now(),
  check (end_at > start_at),
  check (end_at - start_at <= interval '12 hours')
);
create index on activities (status, start_at);
create index on activities using gin (tags);

create table activity_participants (
  activity_id uuid references activities(id) on delete cascade,
  user_id uuid references profiles(id) on delete cascade,
  status text not null default 'joined' check (status in ('joined','left','removed')),
  attendance text not null default 'pending'
    check (attendance in ('pending','attended','no_show','late_cancel')),
  joined_at timestamptz not null default now(),
  left_at timestamptz,
  primary key (activity_id, user_id)
);

create table activity_messages (                     -- Should: chat theo hoạt động
  id uuid primary key default gen_random_uuid(),
  activity_id uuid not null references activities(id) on delete cascade,
  sender_id uuid not null references profiles(id) on delete cascade,
  kind text not null default 'text' check (kind in ('text','system','vivi')),
  body text not null check (char_length(body) <= 1000),
  guard_label text,
  created_at timestamptz not null default now()
);
create index on activity_messages (activity_id, created_at desc);

create table activity_reviews (                      -- Could: đánh giá sau hoạt động
  activity_id uuid references activities(id) on delete cascade,
  reviewer_id uuid references profiles(id) on delete cascade,
  reviewee_id uuid references profiles(id) on delete cascade,
  rating smallint not null check (rating between 1 and 5),
  created_at timestamptz not null default now(),
  primary key (activity_id, reviewer_id, reviewee_id),
  check (reviewer_id <> reviewee_id)
);

-- ===== Uy tín (AI-4) =====
create table trust_events (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references profiles(id) on delete cascade,
  type text not null check (type in ('attended','late_cancel','no_show','host_cancel','review_received','violation')),
  activity_id uuid references activities(id) on delete set null,
  outcome numeric(3,2),                              -- o_e hoặc số sao
  occurred_at timestamptz not null default now(),
  algo_version text not null default '1.0'
);
create index on trust_events (user_id, occurred_at desc);

create table trust_scores (
  user_id uuid primary key references profiles(id) on delete cascade,
  score smallint not null default 0 check (score between 0 and 100),
  band text not null default 'new_member',
  components jsonb not null default '{"V":0,"R":0,"P":0,"C":10}',
  event_count int not null default 0,
  algo_version text not null default '1.0',
  updated_at timestamptz not null default now()
);

-- ===== An toàn =====
create table reports (
  id uuid primary key default gen_random_uuid(),
  reporter_id uuid not null references profiles(id) on delete cascade,
  target_type text not null check (target_type in ('user','activity','message')),
  target_id uuid not null,
  reason text not null check (reason in ('scam','solicit','harassment','pii','commercial','other')),
  details text check (char_length(details) <= 500),
  status text not null default 'open' check (status in ('open','actioned','dismissed')),
  created_at timestamptz not null default now()
);

-- ===== AI =====
create table kb_chunks (
  id text primary key,                               -- vd: food-001
  title text not null,
  kind text not null,                                -- food | sight | cafe | outdoor | transport | safety
  area text,
  tags text[] not null default '{}',
  body text not null,
  updated_at date not null,
  embedding vector(1536),
  embedding_model text
);
create index on kb_chunks using hnsw (embedding vector_cosine_ops);

create table ai_logs (                               -- không lưu nội dung chat (BR-15); xóa sau 30 ngày
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles(id) on delete set null,
  feature text not null check (feature in ('match','vivi','guard','icebreaker')),
  model text,
  algo_version text,
  latency_ms int,
  tokens_in int,
  tokens_out int,
  status text not null check (status in ('ok','fallback','error','blocked','demo_cache')),
  meta jsonb not null default '{}',                  -- vd: chunk_ids + điểm, nhãn Guard, input_hash
  created_at timestamptz not null default now()
);
create index on ai_logs (feature, created_at desc);

create table ai_feedback (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references profiles(id) on delete cascade,
  feature text not null,
  target_id text,
  vote smallint not null check (vote in (-1, 1)),
  created_at timestamptz not null default now()
);

create table eval_runs (
  id uuid primary key default gen_random_uuid(),
  suite text not null check (suite in ('EV-1','EV-2','EV-3','EV-4')),
  split text not null check (split in ('dev','test')),
  config jsonb not null,                             -- trọng số, ngưỡng, mô hình, algo_version
  metrics jsonb not null,
  notes text,
  created_at timestamptz not null default now()
);

create table ai_usage (                              -- hạn mức BR-10
  user_id uuid not null references profiles(id) on delete cascade,
  feature text not null,
  window_start timestamptz not null,                 -- cắt theo giờ
  count int not null default 0,
  primary key (user_id, feature, window_start)
);
```

## 4. Hàm cơ sở dữ liệu

### 4.1 Tìm kiếm vector cho ViVi
```sql
create or replace function match_kb_chunks(
  query_embedding vector(1536), match_count int default 4, min_similarity float default 0.0
) returns table (id text, title text, body text, similarity float)
language sql stable as $$
  select c.id, c.title, c.body, 1 - (c.embedding <=> query_embedding) as similarity
  from kb_chunks c
  where 1 - (c.embedding <=> query_embedding) >= min_similarity
  order by c.embedding <=> query_embedding
  limit match_count;
$$;
```

### 4.2 Tham gia hoạt động (nguyên tử, chống tranh chấp chỗ cuối)
```sql
create or replace function join_activity(p_activity uuid)
returns jsonb language plpgsql security definer set search_path = public as $$
declare a activities%rowtype; uid uuid := auth.uid();
begin
  if uid is null then return jsonb_build_object('result','unauthenticated'); end if;
  select * into a from activities where id = p_activity for update;      -- khóa dòng
  if not found or a.status <> 'open' or a.start_at <= now() then
    return jsonb_build_object('result','not_available'); end if;
  if a.host_id = uid then return jsonb_build_object('result','is_host'); end if;
  if a.approved_count >= a.max_participants then
    return jsonb_build_object('result','full'); end if;

  insert into activity_participants(activity_id, user_id, status)
  values (p_activity, uid, 'joined')
  on conflict (activity_id, user_id) do update set status = 'joined', left_at = null
    where activity_participants.status <> 'joined';
  if not found then return jsonb_build_object('result','already_joined'); end if;

  update activities
     set approved_count = approved_count + 1,
         status = case when approved_count + 1 >= max_participants then 'full' else 'open' end
   where id = p_activity;
  return jsonb_build_object('result','joined');
end $$;
```
`leave_activity(p_activity)` làm ngược lại và ghi `trust_events` (`late_cancel` nếu còn < 24 giờ).

## 5. Chính sách RLS (tóm tắt)

| Bảng | Đọc | Ghi |
|---|---|---|
| `interests`, `places` | Mọi người | Không (chỉ seed) |
| `profiles` | Người đã đăng nhập đọc cột công khai (qua view `profiles_public`: tên, avatar, bio, sở thích, mức giao tiếp, band); chỉ chủ sở hữu đọc đầy đủ | Chủ sở hữu sửa; **không** tự đổi `role`, `is_demo`, `is_seed` |
| `activities` | Mọi người đã đăng nhập (trừ `cancelled` của người khác) | Host tạo/sửa của mình; tham gia/rút chỉ qua `join_activity`/`leave_activity` |
| `activity_participants` | Host + người đã tham gia cùng hoạt động | Qua hàm; Host cập nhật `attendance` |
| `activity_messages` | Chỉ thành viên đang tham gia | Thành viên gửi (`sender_id = auth.uid()`) |
| `trust_events`, `trust_scores` | Chủ sở hữu đọc đầy đủ; người khác chỉ đọc band qua `trust_public` | Chỉ máy chủ (Service Role) |
| `reports` | Người báo cáo đọc của mình | Người dùng thêm; xử lý bằng Dashboard |
| `kb_chunks` | Mọi người đã đăng nhập | Chỉ máy chủ |
| `ai_logs`, `ai_usage`, `eval_runs` | `eval_runs` đọc công khai (hiển thị ở AI Lab); còn lại không client nào đọc | Chỉ máy chủ |
| `ai_feedback` | Chủ sở hữu | Chủ sở hữu thêm |

Ví dụ mẫu:
```sql
alter table activity_messages enable row level security;
create policy "msg_select_member" on activity_messages for select using (
  exists (select 1 from activity_participants p
          where p.activity_id = activity_messages.activity_id
            and p.user_id = auth.uid() and p.status = 'joined')
  or exists (select 1 from activities a
             where a.id = activity_messages.activity_id and a.host_id = auth.uid()));
create policy "msg_insert_member" on activity_messages for insert with check (
  sender_id = auth.uid() and (
    exists (select 1 from activity_participants p
            where p.activity_id = activity_messages.activity_id
              and p.user_id = auth.uid() and p.status = 'joined')
    or exists (select 1 from activities a
               where a.id = activity_messages.activity_id and a.host_id = auth.uid())));
```
Mọi chính sách đều có test (Kế hoạch Đánh giá mục 4).

## 6. Dữ liệu minh họa và nạp dữ liệu

| Bước | Công cụ |
|---|---|
| Tạo bảng, RLS, hàm | `supabase/migrations/*.sql` |
| Nạp tra cứu (12 sở thích, ~30 địa điểm) | `supabase/seed/*.sql` |
| Nạp 30 người dùng, ~40 hoạt động, ~150 sự kiện uy tín | `scripts/seed.ts` (từ `data/seed/*.json`) |
| Nạp 60 đoạn KB + tạo embedding | `scripts/ingest-kb.ts` (từ `data/kb/*.md`) |
| Tạo embedding cho hồ sơ/hoạt động minh họa | `scripts/embed-seed.ts` |
| Tính lại điểm uy tín | `scripts/recompute-trust.ts` |

Tài khoản demo là các hàng `auth.users` có `profiles.is_demo = true`; đăng nhập một chạm dùng route `/api/demo-login?persona=P1` (chỉ bật khi `DEMO_MODE=1`).

## 7. Lưu giữ và xóa dữ liệu

| Dữ liệu | Quy tắc |
|---|---|
| `ai_logs` | Xóa sau 30 ngày (tác vụ định kỳ hoặc xóa thủ công trước demo); không chứa nội dung chat |
| Tin nhắn hoạt động | Giữ 90 ngày sau khi hoạt động kết thúc (Should) |
| Xóa dữ liệu của tôi (Could) | Xóa `profiles` → các bảng liên quan xóa theo `on delete cascade`; hoạt động do mình tạo chuyển `cancelled` trước khi xóa |
| Dữ liệu minh họa | Giữ; đánh dấu `is_seed` |

## 8. Phân loại dữ liệu cá nhân
| Mức | Dữ liệu | Xử lý |
|---|---|---|
| Cơ bản | Email (của `auth.users`), tên hiển thị, bio, sở thích | Email không gửi cho nhà cung cấp AI; chỉ `user_text` (sở thích, phong cách, mục tiêu, bio) được embed |
| Hành vi | `trust_events`, `ai_feedback` | Dùng cho điểm uy tín/đánh giá; chủ sở hữu xem được |
| Không thu thập | SĐT, vị trí, ngày sinh, giấy tờ, ảnh | — |
