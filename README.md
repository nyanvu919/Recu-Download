# Recu-Download

Tool Go để tải video từ các URL `.../play` (hỗ trợ tải song song, tuần tự, hybrid, hoặc tải từ file `.m3u8`).

## 1) Build / chạy nhanh

```bash
go build -o recu .
./recu
```

Lần chạy đầu sẽ tự tạo `config.json` **ở thư mục hiện tại (working directory nơi bạn chạy lệnh `./recu`)**.

> Repo không kèm sẵn file `config.json`; file này được tạo khi chạy chương trình lần đầu.

---

## 2) Cấu hình tối thiểu để tải (đơn giản nhất)

Chỉ cần điền **3 thứ bắt buộc** trong `config.json`:

- `urls`: link video dạng `https://.../video/.../play`
- `header.Cookie`
- `header.User-Agent`

Ví dụ tối thiểu:

```json
{
  "urls": [
    "https://recu.me/ten-user/video/xxxx/play"
  ],
  "header": {
    "Cookie": "...",
    "User-Agent": "..."
  },
  "options": {
    "Maximum Resolution (Height)": ""
  }
}
```

> `options` có thể để trống như trên.

---

## 3) Cách tải dễ nhất

Sau khi điền xong `config.json`, chỉ cần chạy:

```bash
./recu
```

Mặc định chương trình tải **song song** các URL trong `urls`.

---

## 4) Các chế độ chạy

```bash
./recu [duong_dan_json] [playlist|series|hybrid|parse] [tham_so_them]
```

- Không truyền gì: tải song song từ `config.json`
- `series`: tải tuần tự từng video
- `hybrid`: tuần tự theo từng server, nhưng nhiều server tải song song
- `playlist`: chỉ tải file `.m3u8`
- `playlist <file.m3u8>`: tải nội dung video từ file `.m3u8` có sẵn
- `parse <url-trang-html>`: quét HTML để thêm URL video vào `urls`

Ví dụ:

```bash
./recu config.json series
./recu config.json playlist
./recu config.json playlist ./abc.m3u8
./recu config.json hybrid
./recu config.json parse https://recu.me/ten-user/videos
```

---

## 5) Resume / đánh dấu hoàn thành (tự động)

Sau mỗi lần tải, chương trình tự cập nhật `urls` trong JSON:

- `"COMPLETE"` nếu video đã xong
- hoặc lưu chỉ số segment cuối để resume khi chạy lại

Bạn chỉ cần chạy lại lệnh cũ để tiếp tục.

---

## 6) Tải một đoạn video (tuỳ chọn nâng cao)

Phần tử trong `urls` có thể là mảng:

```json
[
  "https://recu.me/.../play",
  "55:00",
  "1:10:00",
  "1:30:00"
]
```

Ý nghĩa: `start`, `end`, `total length`.

---

## 7) Mẹo lấy Cookie/User-Agent

Mở DevTools trình duyệt (Network) khi đã đăng nhập trang, lấy từ request video/page:

- `Cookie`
- `User-Agent`

Dán vào `config.json` rồi chạy `./recu`.

---

## 8) Trợ giúp

```bash
./recu --help
```
