# 📝 Smart Note — Flutter App

> **BÀI THỰC HÀNH 2** | Môn: Lập trình Mobile (Flutter)  
> **Chủ đề:** CRUD Ghi chú & Local Storage (SharedPreferences)

Ứng dụng ghi chú thông minh được xây dựng bằng Flutter, hoạt động hoàn toàn **Offline** với khả năng lưu trữ vĩnh viễn trên thiết bị thông qua `SharedPreferences` và JSON Serialization.

---

## 🎯 Mục tiêu bài học

| # | Mục tiêu |
|---|----------|
| 1 | Áp dụng **Model & JSON Serialization** (`jsonEncode` / `jsonDecode`) |
| 2 | Làm chủ **SharedPreferences** để lưu trữ dữ liệu cục bộ vĩnh viễn |
| 3 | Xử lý **Navigation** (chuyển màn hình) và cập nhật **State bất đồng bộ** |

---

## 📱 Luồng ứng dụng (App Flow)

```
Khởi động App
    └─> Đọc dữ liệu từ SharedPreferences
          ├─> [Chưa có dữ liệu] → Hiển thị màn hình trống (Empty State)
          └─> [Đã có dữ liệu]   → Hiển thị danh sách ghi chú
                ├─> Bấm FAB (+)        → Màn hình Soạn thảo (tạo mới)
                ├─> Bấm vào thẻ       → Màn hình Soạn thảo (chỉnh sửa)
                ├─> Vuốt thẻ (Swipe)  → Xác nhận xóa → Xóa khỏi Storage
                └─> Gõ Search Bar     → Lọc real-time theo tiêu đề

Màn hình Soạn thảo
    └─> Gõ nội dung → Bấm Back (hoặc vuốt cạnh màn hình)
          └─> [Auto-save] Mã hóa JSON → Lưu xuống SharedPreferences
                └─> Quay về Home → Danh sách tự động làm mới
```

---

## ✅ Yêu cầu tính năng

### A. Màn hình chính (Home Screen)

#### 🔖 AppBar — Định danh bắt buộc
- Tiêu đề AppBar phải hiển thị đúng cú pháp:
  ```
  Smart Note - [Họ tên Sinh viên] - [Mã SV]
  ```

#### 🔍 Thanh tìm kiếm (Search Bar) — Bắt buộc
- [ ] Nằm ngay dưới AppBar, **bo góc tròn**, có **icon kính lúp**
- [ ] Khi gõ từ khóa → danh sách bên dưới tự động lọc **real-time** theo **Tiêu đề** ghi chú

#### 📋 Danh sách ghi chú (Note List) — Bắt buộc
- [ ] Hiển thị dạng **lưới 2 cột** (`StaggeredGridView` — ưu tiên Masonry Layout, độ cao thẻ khác nhau)
- [ ] Mỗi thẻ (Card): **bo góc, đổ bóng nhẹ**, nền màu ghi chú
- [ ] Nội dung mỗi thẻ:
  | Thành phần | Yêu cầu |
  |------------|---------|
  | **Tiêu đề** | In đậm, tối đa **1 dòng**, overflow `...` |
  | **Nội dung tóm tắt** | Màu nhạt hơn, tối đa **3 dòng**, overflow `...` |
  | **Thời gian** | Góc dưới thẻ, định dạng `dd/MM/yyyy HH:mm` |

#### 🈳 Trạng thái rỗng (Empty State)
- [ ] Khi danh sách trống: hiển thị **hình ảnh minh họa mờ** giữa màn hình
- [ ] Kèm dòng chữ: *"Bạn chưa có ghi chú nào, hãy tạo mới nhé!"*

#### ➕ Nút thêm mới
- [ ] **Floating Action Button** (FAB) dấu `+` ở góc dưới bên phải

---

### B. Màn hình Soạn thảo (Detail / Edit Screen)

#### 🎨 Giao diện tối giản
- [ ] Thiết kế giống **trang giấy trắng** — ẩn viền (`border`) của TextField
- [ ] `TextField` tiêu đề: font lớn, in đậm
- [ ] `TextField` nội dung: **đa dòng (multiline)**, tự động giãn chiều cao

#### 💾 Auto-save — Trọng tâm kỹ thuật
> [!IMPORTANT]
> **Tuyệt đối KHÔNG thiết kế nút "Lưu" (Save)**

- [ ] Ứng dụng **tự động lưu** khi người dùng thoát màn hình:
  - Bấm nút mũi tên `Back`
  - Vuốt cạnh màn hình để Back
- [ ] Quy trình: `Đọc TextEditingController` → `jsonEncode` → `SharedPreferences.setString()`
- [ ] Quay về Home → danh sách **tự làm mới**, hiển thị thẻ ghi chú mới

---

### C. Thao tác Xóa (Delete)

- [ ] Hỗ trợ **Swipe to Delete** (vuốt để xóa) trên từng thẻ ở màn hình chính
- [ ] Khi vuốt: hiển thị **nền đỏ** + **icon thùng rác** 🗑️
- [ ] **Bắt buộc** hiển thị `AlertDialog` xác nhận:
  > *"Bạn có chắc chắn muốn xóa ghi chú này không?"*
  - Bấm **OK** → xóa khỏi SharedPreferences
  - Bấm **Hủy** → giữ nguyên, đóng dialog

---

## 🔧 Yêu cầu kỹ thuật

| Yêu cầu | Chi tiết |
|---------|---------|
| **Offline** | Ứng dụng hoạt động hoàn toàn không cần Internet |
| **JSON Serialization** | Mọi dữ liệu phải được đóng gói qua Model → `jsonEncode` trước khi lưu, `jsonDecode` khi đọc |
| **SharedPreferences** | Lưu trữ dưới dạng `List<String>` (danh sách chuỗi JSON) |
| **Persistent Storage** | Kill App / khởi động lại thiết bị → dữ liệu vẫn còn nguyên ✅ |

> [!CAUTION]
> **Bài test bắt buộc của giảng viên:** Vuốt tắt hoàn toàn ứng dụng (Kill App) hoặc khởi động lại máy ảo → mở lại → danh sách dữ liệu phải còn nguyên vẹn.

---

## 🗄️ Mô hình dữ liệu (Data Model)

### Note Model
```dart
class Note {
  String id;           // UUID duy nhất (uuid package)
  String title;        // Tiêu đề ghi chú
  String content;      // Nội dung ghi chú
  int colorValue;      // Màu nền (lưu dưới dạng int: color.value)
  DateTime createdAt;  // Ngày tạo
  DateTime updatedAt;  // Ngày chỉnh sửa cuối

  // Chuyển đổi sang Map để jsonEncode
  Map<String, dynamic> toJson() => {
    'id': id,
    'title': title,
    'content': content,
    'colorValue': colorValue,
    'createdAt': createdAt.toIso8601String(),
    'updatedAt': updatedAt.toIso8601String(),
  };

  // Khôi phục từ Map sau jsonDecode
  factory Note.fromJson(Map<String, dynamic> json) => Note(
    id: json['id'],
    title: json['title'],
    content: json['content'],
    colorValue: json['colorValue'],
    createdAt: DateTime.parse(json['createdAt']),
    updatedAt: DateTime.parse(json['updatedAt']),
  );
}
```

### Cơ chế lưu trong SharedPreferences
```dart
// Lưu danh sách ghi chú
List<String> jsonList = notes.map((n) => jsonEncode(n.toJson())).toList();
prefs.setStringList('notes', jsonList);

// Đọc danh sách ghi chú
List<String>? jsonList = prefs.getStringList('notes') ?? [];
List<Note> notes = jsonList.map((s) => Note.fromJson(jsonDecode(s))).toList();
```

---

## 🏗️ Cấu trúc thư mục

```
smart_note/
├── lib/
│   ├── main.dart                     # Entry point, khởi tạo app
│   ├── models/
│   │   └── note.dart                 # Note model + toJson/fromJson
│   ├── screens/
│   │   ├── home_screen.dart          # Màn hình chính (danh sách + search)
│   │   └── note_editor_screen.dart   # Màn hình soạn thảo (auto-save)
│   ├── widgets/
│   │   ├── note_card.dart            # Widget thẻ ghi chú (Staggered)
│   │   ├── search_bar_widget.dart    # Thanh tìm kiếm
│   │   └── empty_state.dart          # Widget trạng thái rỗng
│   └── services/
│       └── note_service.dart         # CRUD + SharedPreferences logic
├── assets/
│   └── images/
│       └── empty_notes.png           # Ảnh minh họa empty state
├── test/
│   └── widget_test.dart
└── pubspec.yaml
```

---

## 📦 Dependencies (pubspec.yaml)

```yaml
dependencies:
  flutter:
    sdk: flutter

  # Local Storage — Bắt buộc
  shared_preferences: ^2.2.3

  # Layout ghi chú dạng Masonry
  flutter_staggered_grid_view: ^0.7.0

  # Tạo ID duy nhất cho mỗi ghi chú
  uuid: ^4.3.3

  # Định dạng ngày giờ (dd/MM/yyyy HH:mm)
  intl: ^0.19.0

  # Font chữ đẹp
  google_fonts: ^6.2.1

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0
```

---

## 🎨 Thiết kế UI/UX

### Theme (Material Design 3)
```dart
ThemeData(
  useMaterial3: true,
  colorSchemeSeed: Colors.amber,
  fontFamily: GoogleFonts.inter().fontFamily,
)
```

### Bảng màu ghi chú
| Màu | Hex | Ghi chú |
|-----|-----|---------|
| Vàng nhạt | `#FFF9C4` | Mặc định |
| Xanh lá nhạt | `#DCEDC8` | |
| Hồng nhạt | `#FCE4EC` | |
| Xanh dương nhạt | `#E3F2FD` | |
| Tím nhạt | `#EDE7F6` | |
| Cam nhạt | `#FFF3E0` | |
| Trắng | `#FFFFFF` | |

---

## 📋 Kỹ năng Flutter được rèn luyện

| Widget / Concept | Áp dụng trong |
|-----------------|---------------|
| `SharedPreferences` | Lưu & đọc dữ liệu ghi chú |
| `jsonEncode` / `jsonDecode` | Serialize model ↔ String |
| `StaggeredGridView` | Danh sách ghi chú 2 cột |
| `Dismissible` | Swipe to delete |
| `AlertDialog` | Xác nhận xóa ghi chú |
| `TextEditingController` | Soạn thảo tiêu đề & nội dung |
| `Navigator.push` + `await` | Chuyển màn hình & nhận kết quả |
| `WillPopScope` / `PopScope` | Bắt sự kiện Back để Auto-save |
| `setState` | Cập nhật UI khi thay đổi dữ liệu |
| `intl` (`DateFormat`) | Định dạng ngày `dd/MM/yyyy HH:mm` |

---

## 🚀 Hướng dẫn khởi chạy

```bash
# Tạo project mới
flutter create smart_note
cd smart_note

# Cài dependencies
flutter pub get

# Chạy ứng dụng trên emulator/thiết bị
flutter run
```

---

## 🧪 Kịch bản kiểm thử (Test Cases)

| # | Thao tác | Kết quả mong đợi |
|---|----------|-----------------|
| 1 | Mở app lần đầu | Hiển thị Empty State |
| 2 | Bấm FAB (+), gõ nội dung, bấm Back | Ghi chú xuất hiện ở Home |
| 3 | Gõ từ khóa vào Search | Danh sách lọc real-time |
| 4 | Bấm vào thẻ ghi chú | Mở màn hình soạn thảo với nội dung cũ |
| 5 | Sửa nội dung, bấm Back | Ghi chú được cập nhật |
| 6 | Vuốt thẻ sang trái/phải | Nền đỏ + icon thùng rác |
| 7 | Xác nhận xóa → OK | Thẻ biến mất; nếu hết → Empty State |
| 8 | **Kill App → Mở lại** | ✅ Dữ liệu còn nguyên vẹn |
| 9 | **Khởi động lại máy ảo → Mở lại** | ✅ Dữ liệu còn nguyên vẹn |

---

## 📅 Phân chia giai đoạn phát triển

| Giai đoạn | Nội dung | Ưu tiên |
|-----------|----------|---------|
| **Phase 1** | Setup project, Note Model, toJson/fromJson, NoteService (CRUD + SharedPreferences) | 🔴 Bắt buộc |
| **Phase 2** | Home Screen: StaggeredGrid, NoteCard, Empty State, FAB | 🔴 Bắt buộc |
| **Phase 3** | Note Editor Screen: TextField tối giản, Auto-save khi Back | 🔴 Bắt buộc |
| **Phase 4** | Search Bar real-time, Swipe to Delete + AlertDialog | 🔴 Bắt buộc |
| **Phase 5** | Chọn màu ghi chú, Dark Mode, Hoàn thiện UI/UX | 🟡 Nâng cao |

---

> **Ghi chú:** AppBar phải hiển thị đúng cú pháp `Smart Note - [Họ tên] - [Mã SV]` để được chấp nhận bài.

#   P T U D _ T H 2  
 