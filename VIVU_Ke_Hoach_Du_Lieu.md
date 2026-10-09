# Kế hoạch Dữ liệu (Database Schema Plan)

## 1. Users Collection/Table
- Cấu trúc: UserID, Tên, SĐT/Email, Mật khẩu, Mục tiêu, Mức độ giao tiếp, Danh sách Sở thích, Điểm uy tín, Settings (Quyền riêng tư).

## 2. Posts Collection
- Cấu trúc: PostID, UserID, Nội dung, Media (Ảnh/Video), Địa điểm, Thời gian, Số lượng người (Max), Hashtags.

## 3. Activities Collection
- Cấu trúc: ActivityID, HostID, Danh mục (Ăn uống, Phượt...), Thông tin lộ trình, Danh sách User tham gia, Trạng thái.

## 4. Groups Collection
- Cấu trúc: GroupID, Tên nhóm, CoverImage, AdminID, Mô tả, Danh sách UserID.

## 5. Messages Collection
- Cấu trúc: MessageID, SenderID, ReceiverID (hoặc GroupID), Loại (Text/Image/ViVi_Suggestion), Nội dung, Timestamp.