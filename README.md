# PROJECT_DETECT

Repo nay dung de gioi thieu du an `PROJECT_DETECT` tren GitHub bang 2 thanh phan chinh:

- `README.md`: mo ta du an, cach tai source code va cach chay.
- `demo.mp4`: video demo nhanh de nha tuyen dung xem ngay.

Source code day du, model weights, config va cac tai nguyen nang duoc dat trong Google Drive:

- Google Drive: https://drive.google.com/drive/folders/1-7qO1XDZ78QowVs7UlTEfp6M_wVuZZwt?usp=sharing

## Du an nay la gi

`PROJECT_DETECT` la ung dung web dem xe theo pipeline 2-stage:

1. Stage-1 detector: phat hien va tracking 5 ho xe thô.
2. Stage-2 classifier: phan loai sau hon cho cac nhom xe kho.
3. Temporal fusion: on dinh nhan theo nhieu frame.
4. Tripwire / gate transition: chi dem khi xe di qua line hop le.
5. Web dashboard: upload video, theo doi tien do, xem lich su, xuat bao cao.

## Repo GitHub nay chua gi

- `README.md`
- `demo.mp4`

## Nhung gi khong dua len GitHub

De repo gon nhe hon, cac thanh phan sau khong dat tren GitHub hoac duoc dat trong Google Drive:

- Source code day du.
- Model weights `.pt`.
- Dataset train.
- Virtual environment.
- Database runtime.
- Video lich su va file trung gian khi xu ly.

## Cach lay source code va chay local

1. Mo link Google Drive ben tren.
2. Tai goi source code cua du an ve may va giai nen.
3. Mo thu muc code `trafficcounter_2stage_web`.
4. Tai them model neu trong goi code chua kem san.
5. Lam theo cac buoc ben duoi de cai dat va chay.

## Cau truc toi thieu sau khi tai code va model

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
└── web/
```

App mac dinh doc model tu cac duong dan sau:

- `models/detector/best.pt`
- `models/cls_bus/best.pt`
- `models/cls_truck/best.pt`
- `models/cls_container/best.pt`

Neu ban doi ten model, can sua lai `configs/pipeline.yaml` trong source code.

## Cai dat

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

## Chay ung dung

Can 2 terminal rieng.

### Terminal 1: web app

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

Mac dinh app chay tai:

```text
http://127.0.0.1:8001
```

### Terminal 2: worker

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

Neu khong chay `worker.py`, job upload se dung o trang thai `queued`.

## Dung video demo

Video `demo.mp4` trong repo nay dung de xem nhanh ket qua demo.

Ban co 2 cach dung:

1. Tai truc tiep `demo.mp4` tu repo nay.
2. Hoac mo tu Google Drive neu ban muon lay them cac video khac.

Sau do:

1. Mo web app.
2. Upload `demo.mp4`.
3. Chon file config JSON phu hop trong `configs/`.
4. Bat dau xu ly va theo doi tien do.

## Luu y cho nha tuyen dung

Repo nay duoc giu o dang showcase de de xem nhanh.

- Video demo co san trong repo.
- Huong dan chay du an co trong README.
- Source code va tai nguyen nang duoc de trong Google Drive de tranh lam repo qua lon.

## Lien ket

- Demo va assets: https://drive.google.com/drive/folders/1-7qO1XDZ78QowVs7UlTEfp6M_wVuZZwt?usp=sharing