# 🦺 PPE Detection for Substation Safety

Real-time Personal Protective Equipment (PPE) detection for a 275 kV extra-high-voltage substation, built by a substation operator to solve a real safety problem on site.

> **Note:** All images and data in this repository are dummy or anonymized. No internal company data, camera addresses, or credentials are included.

---

## 📌 The Problem

In an EHV substation, every person entering the switchyard must wear a **safety helmet** and **safety shoes**. Operators monitor many CCTV feeds at once, so missing a violation is easy, especially during a 24-hour shift. A single unprotected entry near energized equipment is a serious risk.

## 💡 The Solution

A computer-vision system that watches existing CCTV cameras and:

- Detects **helmet / no_helmet** and **shoes / no_shoes** in real time
- Raises an alert only when a violation happens inside a defined **danger zone (ROI)**
- Sends a **Telegram notification** with a snapshot to the operator group
- Logs every violation to a database for later review
- Shows all cameras on one **web dashboard**

## 🖼️ Demo

| Detection | Dashboard |
|---|---|
| ![detection](docs/demo-detection.gif) | ![dashboard](docs/dashboard.png) |

*(Replace with your own anonymized screenshot / GIF.)*

---

## 🧠 Model

| Item | Detail |
|---|---|
| Architecture | YOLOv8n / YOLOv8s (Ultralytics) |
| Classes | `helmet`, `no_helmet`, `shoes`, `no_shoes` |
| Dataset | Custom dataset from on-site CCTV, labeled in Roboflow |
| Training | Google Colab (GPU) |
| Best model | YOLOv8n on dataset v6 |

### Results (best model)

| Class | mAP50 |
|---|---|
| helmet | `0.xx` |
| no_helmet | `0.xx` |
| shoes | `0.xx` |
| no_shoes | `0.xx` |

*(Fill in from your training results.)*

### Lessons learned
- Fixed a dataset split bug where the validation set had **0% data**, which made early metrics meaningless.
- Domain-matched data (snapshots from the actual CCTV angle) beats merging generic public datasets.
- The `shoes` class is the hardest: feet are small and often hidden at typical CCTV angles.

---

## 🏗️ System Architecture

```
CCTV (RTSP) ──► kamera.py (one process per camera)
                   │  ├─ YOLOv8 inference
                   │  ├─ ROI danger-zone check
                   │  ├─ Per-violation cooldown
                   │  └─ Heartbeat status
                   ▼
             SQLite database ◄──── Streamlit dashboard
                   │
                   ▼
             Telegram alert (photo + caption)
```

## ⚙️ Features

- **Multi-camera**: threaded RTSP reader (forced TCP for stable streams)
- **ROI zones**: draw polygon danger zones with a click-based calibration tool
- **Smart alerts**: async Telegram notifications with cooldown to prevent spam
- **Status monitoring**: heartbeat table shows ONLINE / OFFLINE per camera
- **Secure config**: credentials and camera URLs stored in `.env`, never in code

## 📂 Project Structure

```
├── kamera.py                  # Detection + alert service per camera
├── dashboard.py               # Streamlit multi-camera dashboard
├── kalibrasi_zona.py          # ROI calibration (mouse click)
├── kalibrasi_zona_web.html    # ROI calibration (browser version)
├── jalankan_semua_kamera.bat  # Launch all cameras at once
├── .env.example               # Template config (no real values)
├── requirements.txt
└── docs/                      # Screenshots and demo GIFs
```

## 🚀 Getting Started

```bash
git clone https://github.com/dpanggabean220-bot/ppe-detection-substation.git
cd ppe-detection-substation
pip install -r requirements.txt
cp .env.example .env    # fill in your own camera URL and Telegram token
python kamera.py
streamlit run dashboard.py
```

## 🛠️ Tech Stack

Python · YOLOv8 (Ultralytics) · OpenCV · Roboflow · Streamlit · SQLite · Telegram Bot API · Google Colab

## 🔭 Roadmap

- [ ] Guest-arrival notification from the gate camera
- [ ] Smoking detection in the control room
- [ ] Insulator damage detection
- [ ] Vegetation encroachment detection near transmission lines

---

## 👤 About

**David Panggabean**: substation operator at a 275 kV EHV substation, building AI tools for power-system operations.

[LinkedIn](https://linkedin.com/in/<your-profile>) · [GitHub](https://github.com/dpanggabean220-bot)
