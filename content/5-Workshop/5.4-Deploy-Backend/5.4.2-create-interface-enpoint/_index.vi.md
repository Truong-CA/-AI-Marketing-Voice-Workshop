---
title : "Kiểm tra VieNeu-TTS"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

VieNeu-TTS là mô hình chạy **local**, không cần API key, nhưng cần tự tải trọng số model từ Hugging
Face ở lần chạy đầu tiên. Bước này giúp bạn xác nhận việc tải model và tổng hợp giọng nói hoạt động đúng
trước khi test qua API đầy đủ.

#### Bước 1 — Khởi động backend và gọi thử endpoint danh sách giọng

```
uvicorn main:app --reload --port 8000
```

Ở một terminal khác:
```
curl http://localhost:8000/api/v1/voices
```

Lần gọi **đầu tiên** sẽ mất thời gian hơn (vài chục giây đến vài phút tuỳ tốc độ mạng) vì model đang được tải
về máy — theo dõi log ở terminal chạy `uvicorn` để thấy tiến trình. Các lần gọi sau sẽ nhanh vì model đã cache
sẵn trong bộ nhớ của tiến trình backend.


Kết quả trả về nên có dạng:
```json
{
  "success": true,
  "voices": [
    {"voice_id": "Xuân Vĩnh", "name": "Xuân Vĩnh", "description": "Nam · Bắc · Phong cách tự nhiên", ...},
    ...
  ]
}
```
![list giọng](/images/16.png)

#### Bước 2 — Tạo thử một mẫu giọng (preview)

```
curl http://localhost:8000/api/v1/voices/Xuân%20Vĩnh/preview
```

Endpoint này **miễn phí, không cần đăng nhập**, sinh ra một câu mẫu ngắn và cache lại file, nên các lần gọi
sau cho cùng giọng sẽ trả về ngay lập tức.

![tạo mẫu giọng](/images/15.png)

#### Kiểm tra lại
- [ ] `GET /api/v1/voices` trả về đủ 10 giọng đọc
- [ ] `GET /api/v1/voices/{voice_id}/preview` trả về `audio_url` và nghe được đúng nội dung tiếng Việt
- [ ] Không có lỗi tải model trong log terminal của `uvicorn`
