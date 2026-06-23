# TrafficCounter 2-Stage Web

Ứng dụng web đếm xe offline theo pipeline 2-stage:

1. Stage-1 detector: phát hiện và tracking 5 họ xe thô (`xe_con`, `xe_may`, `xe_khach_bus`, `xe_tai`, `container`).
2. Stage-2 classifier: phân loại sâu hơn cho các họ khó để ra nhóm xe cuối.
3. Temporal fusion: gom phiếu theo `track_id` qua nhiều frame để ổn định nhãn.
4. Tripwire / gate transition: chỉ đếm khi xe đi qua line hoặc gate hợp lệ.
5. Web dashboard: upload video, theo dõi tiến độ, xem lịch sử, tổng hợp theo ngày, xuất Excel hoặc CSV.

## Repo này chứa gì

Repo GitHub nên chỉ chứa:

- Source code.
- File cấu hình trong `configs/`.
- Hướng dẫn cài đặt và chạy.

Repo GitHub không nên chứa:

- Model weights `.pt`.
- Video test và video lịch sử.
- Dataset train.
- Virtual environment như `.venv/`, `.venv-1/`, `env/`.
- Database sinh ra khi chạy.

Các file nặng được đặt ở Google Drive:

- Google Drive assets: https://drive.google.com/drive/folders/1-7qO1XDZ78QowVs7UlTEfp6M_wVuZZwt?usp=sharing

Trong Drive nên có ít nhất:

- Các model weights cần thiết.
- Video demo để test nhanh.
- Nếu cần, thêm file cấu hình mẫu hoặc bản backup dữ liệu minh họa.

## Cấu trúc cần có sau khi tải assets từ Drive

Sau khi tải code từ GitHub ZIP và tải assets từ Google Drive, thư mục làm việc tối thiểu nên có dạng:

```text
trafficcounter_2stage_web/
├── main.py
├── worker.py
├── requirements.txt
├── .env.example
├── configs/
├── models/
│   ├── detector/
│   │   └── best.pt
│   ├── cls_bus/
│   │   └── best.pt
│   ├── cls_truck/
│   │   └── best.pt
│   └── cls_container/
│       └── best.pt
└── demo.mp4  # co the de o bat ky dau, day chi la goi y de test nhanh
```

Model mặc định đang được app sử dụng nằm trong `configs/pipeline.yaml`:

- `models/detector/best.pt`
- `models/cls_bus/best.pt`
- `models/cls_truck/best.pt`
- `models/cls_container/best.pt`

Nếu bạn đổi tên file model, cần sửa lại đường dẫn tương ứng trong `configs/pipeline.yaml`.

## Cài đặt

### Windows

```powershell
cd path\to\trafficcounter_2stage_web
py -3 -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
Copy-Item .env.example .env
```

### macOS / Linux

```bash
cd /path/to/trafficcounter_2stage_web
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install -r requirements.txt
cp .env.example .env
```

## Cấu hình môi trường

File `.env.example` đã có cấu hình local mặc định với SQLite:

```text
DATABASE_URL=sqlite:///./data/traffic.db
PORT=8000
FLASK_DEBUG=0
MAX_PARALLEL_JOBS=2
```

Khi chạy local, bạn có thể dùng gần như nguyên bản file này. Nếu chưa có file database, app sẽ tự tạo bảng khi khởi động.

## Chạy ứng dụng

App cần 2 tiến trình riêng:

1. Web server để upload video và xem dashboard.
2. Worker để nhận job và chạy pipeline AI.

### Terminal 1: chạy web

Windows:

```powershell
cd path\to\trafficcounter_2stage_web
.venv\Scripts\activate
python main.py
```

macOS / Linux:

```bash
cd /path/to/trafficcounter_2stage_web
source .venv/bin/activate
python3 main.py
```

Mặc định app in ra địa chỉ web, thường là:

```text
http://127.0.0.1:8001
```

### Terminal 2: chạy worker

Windows:

```powershell
cd path\to\trafficcounter_2stage_web
.venv\Scripts\activate
python worker.py
```

macOS / Linux:

```bash
cd /path/to/trafficcounter_2stage_web
source .venv/bin/activate
python3 worker.py
```

Nếu chỉ chạy `main.py` mà không chạy `worker.py`, job sẽ đứng ở trạng thái `queued`.

## Test nhanh bằng video demo

1. Tải `demo.mp4` từ Google Drive ở trên.
2. Mở web app.
3. Upload `demo.mp4`.
4. Chọn file config JSON phù hợp trong `configs/`.
5. Bấm bắt đầu.
6. Theo dõi tiến độ ở dashboard hoặc trang chi tiết job.

Lưu ý: `demo.mp4` không bắt buộc phải nằm đúng ở thư mục gốc repo. Bạn có thể để ở đâu cũng được, miễn là upload được qua giao diện web.

## Chạy với video thật

1. Đặt file cấu hình camera/gate vào `configs/` nếu chưa có.
2. Vào web app và upload video.
3. Chọn config JSON tương ứng.
4. Có thể nhập tay ngày giờ hoặc để OCR đọc từ `clock_roi`.
5. Bấm bắt đầu.
6. Sau khi xử lý xong, xuất Excel hoặc CSV ZIP từ giao diện.

## Dataset build cho 2-stage

### Build detector 5 lớp từ dataset 13 lớp

```bash
cd /path/to/trafficcounter_2stage_web
PYTHONPATH=. python3 scripts/build_stage1_dataset.py \
	--source /path/to/fine_yolo_dataset \
	--output datasets/det_5cls \
	--classes-config configs/classes.yaml
```

### Build classifier crops

```bash
python3 scripts/build_stage2_crops.py \
	--source /path/to/fine_yolo_dataset \
	--output datasets \
	--classes-config configs/classes.yaml
```

## Lỗi thường gặp

| Thông báo | Cách xử lý |
|-----------|------------|
| `command not found: python` | Dùng `python3` thay cho `python` trên macOS hoặc Linux. |
| `No such file or directory: models/.../best.pt` | Bạn chưa tải model weights từ Google Drive hoặc đặt sai thư mục. |
| Job đứng ở `queued` | Bạn chưa chạy `python worker.py` ở terminal thứ hai. |
| `Address already in use` | Đổi cổng bằng biến môi trường `PORT` hoặc tắt tiến trình cũ. |
| Import lỗi sau khi cài | Kích hoạt đúng virtualenv rồi cài lại `requirements.txt`. |

## Ghi chú

- Repo này không kèm model weights trong Git.
- Repo này không kèm video lịch sử đã xử lý.
- Nếu muốn dùng schema camera mới có `gate_transition`, tham khảo `configs/example_camera_config.json`.