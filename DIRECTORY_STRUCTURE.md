# Cấu trúc Thư mục Dự án VIVU (Expo)

```text
vivu-app/
├── assets/               # Chứa Logo, font chữ, icon ảnh gradient tím
├── src/
│   ├── components/       # Các UI components tái sử dụng (Button, Card, Rating, Avatar)
│   ├── navigation/       # Cấu hình chuyển trang (Stack, Bottom Tab)
│   ├── screens/          # Nơi chứa 28 màn hình giao diện
│   │   ├── Auth/         # (Screens 1 - 10)
│   │   ├── Home/         # (Screens 11 - 15)
│   │   ├── Match/        # (Screens 16 - 18)
│   │   ├── Group/        # (Screens 19 - 21)
│   │   ├── Chat/         # (Screens 22 - 23)
│   │   ├── Map/          # (Screens 24 - 25)
│   │   └── Profile/      # (Screens 27 - 28)
│   ├── services/         # Chứa logic gọi API, Socket.io, cấu hình AI ViVi
│   ├── store/            # Quản lý state toàn cục (Redux / Zustand)
│   ├── theme/            # Khai báo biến màu sắc, typography chung
│   └── utils/            # Helper functions (format ngày, valid form)
├── App.js                # Root component, chứa thẻ ⚡ 28 Màn hình
├── app.json              # Cấu hình dự án Expo
├── package.json          # Quản lý thư viện
└── README.md             # Hướng dẫn chạy (npx expo start)
```