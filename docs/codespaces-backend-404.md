# Sửa lỗi GitHub Codespaces: `Route not found: GET /`

Log dưới đây cho thấy backend **đã chạy thành công** và Redis **đã kết nối**:

```text
Server running on port 5000
Redis connected
GET / HTTP/1.1 404
```

Lỗi JSON:

```json
{"status":"fail","code":"NOT_FOUND","message":"Route not found: GET /"}
```

không phải lỗi Redis. Nghĩa là bạn đang mở trực tiếp URL của **backend API** tại đường dẫn `/`, nhưng code backend hiện chưa khai báo route `GET /`.

## Cách fix trong backend Express

Trong file khởi tạo Express, thường là `backend/src/server.js` hoặc `backend/src/app.js`, thêm route kiểm tra sức khỏe **trước middleware 404**:

```js
app.get('/', (req, res) => {
  res.status(200).json({
    status: 'success',
    message: 'Backend API is running',
    docs: 'Open the frontend port for the web UI, or call /api routes directly.',
  });
});

app.get('/health', (req, res) => {
  res.status(200).json({ status: 'ok' });
});
```

Ví dụ thứ tự đúng:

```js
app.use('/api/auth', authRoutes);
app.use('/api/users', userRoutes);

// Đặt trước catch-all 404
app.get('/', (req, res) => {
  res.status(200).json({ status: 'success', message: 'Backend API is running' });
});

app.use((req, res) => {
  res.status(404).json({
    status: 'fail',
    code: 'NOT_FOUND',
    message: `Route not found: ${req.method} ${req.originalUrl}`,
  });
});
```

Sau đó dừng terminal đang chạy `npm run dev` bằng `Ctrl+C`, rồi chạy lại:

```bash
npm run dev
```

Mở lại port `5000`; nếu thấy JSON `Backend API is running` thì backend đã ổn.

## Nếu bạn muốn mở giao diện web

Port `5000` thường chỉ là backend API. Nếu project có frontend, hãy mở terminal khác và chạy frontend, ví dụ:

```bash
cd frontend
npm install
npm run dev
```

Sau đó mở port frontend, thường là `3000`, `5173`, hoặc port mà Vite/React/Next in ra trong terminal.

## Lưu ý về MongoDB

Log này:

```text
MONGO_URI not defined; starting without MongoDB connection.
```

là cảnh báo riêng. Nếu app cần database thật, hãy tạo file `.env` trong `backend` và thêm `MONGO_URI`. Nếu chỉ kiểm tra server/Redis thì cảnh báo này không phải nguyên nhân gây `GET /` 404.
