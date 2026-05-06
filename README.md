# 🍜 Hệ Thống Nhận Diện Món Ăn Việt Nam & Tư Vấn Dinh Dưỡng Cá Nhân Hoá

<div align="center">

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-2.x-000000?style=for-the-badge&logo=flask&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![EfficientNet](https://img.shields.io/badge/EfficientNet--B0-Transfer%20Learning-green?style=for-the-badge)

**Ứng dụng AI nhận diện 30 món ăn Việt Nam từ ảnh, tự động tra cứu dinh dưỡng và tư vấn chế độ ăn theo mục tiêu cá nhân.**

🏫 Trường Đại học Đại Nam &nbsp;|&nbsp; 🖥️ Khoa Công nghệ Thông tin

[Demo](#demo) • [Tính năng](#tính-năng) • [Cài đặt](#cài-đặt) • [Huấn luyện](#huấn-luyện-model) • [API](#api-reference)

</div>

---

## 📋 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Tính năng](#tính-năng)
- [Kết quả](#kết-quả)
- [Kiến trúc hệ thống](#kiến-trúc-hệ-thống)
- [Công nghệ sử dụng](#công-nghệ-sử-dụng)
- [Cài đặt](#cài-đặt)
- [Cấu trúc thư mục](#cấu-trúc-thư-mục)
- [Huấn luyện model](#huấn-luyện-model)
- [API Reference](#api-reference)
- [Dữ liệu](#dữ-liệu)
- [Tác giả](#tác-giả)

---

## 🎯 Giới thiệu

Hệ thống sử dụng mô hình **EfficientNet-B0** được fine-tune trên bộ dữ liệu **30VNFoods** để nhận diện 30 món ăn Việt Nam phổ biến từ ảnh chụp. Sau khi nhận diện, hệ thống tự động tra cứu thông tin dinh dưỡng từ cơ sở dữ liệu của **Viện Dinh dưỡng Quốc gia Việt Nam**, đồng thời cá nhân hoá tư vấn theo hồ sơ sức khoẻ của từng người dùng (cân nặng, chiều cao, tuổi, mục tiêu).

---

## ✨ Tính năng

### 🔍 Nhận diện món ăn
- Upload ảnh JPG/PNG/WEBP → AI phân tích và trả về **Top-3 món** có khả năng cao nhất kèm **% độ tin cậy**
- Xử lý nhanh: **< 3 giây/ảnh**
- Hỗ trợ 30 món ăn Việt Nam phổ biến

### 📊 Thông tin dinh dưỡng
- Tra cứu đầy đủ: **Calories, Protein, Carbs, Fat, Fiber**
- Tính theo **khẩu phần thực tế** (½ tô, 1 tô, 1 gói...)
- Dữ liệu offline từ file CSV — **không cần gọi API ngoài**

### 👤 Hồ sơ cá nhân & TDEE
- Nhập thông tin cá nhân: cân nặng, chiều cao, tuổi, giới tính, mức độ vận động
- Tự động tính **TDEE (Total Daily Energy Expenditure)**
- Điều chỉnh mục tiêu calo theo: giảm cân / duy trì / tăng cân

### 🍱 Gợi ý món ăn & Lên thực đơn
- Tự động **gợi ý món ăn** phù hợp mục tiêu calo hàng ngày
- **Lên thực đơn theo tuần** (sáng/trưa/tối) dựa trên hồ sơ người dùng
- Tính toán dinh dưỡng tổng cho cả ngày / cả tuần
- Cảnh báo khi vượt quá calo mục tiêu

### 🤖 Chatbot tư vấn dinh dưỡng
- Trả lời câu hỏi về calories, chất dinh dưỡng của từng món
- Gợi ý món ăn thay thế ít calo hơn hoặc nhiều protein hơn
- Tư vấn chế độ ăn theo mục tiêu cá nhân

### 📅 Lịch sử ăn uống
- Lưu từng bữa ăn theo profile người dùng
- Biểu đồ thống kê **7 ngày gần nhất**
- Cảnh báo khi tổng calo vượt mục tiêu ngày

---

## 📈 Kết quả

| Chỉ số | Giá trị |
|--------|---------|
| 🎯 Top-1 Accuracy | **85.42%** |
| 🎯 Top-3 Accuracy | **95.30%** |
| ⚡ Thời gian xử lý | **< 3 giây/ảnh** |
| 🍜 Số món hỗ trợ | **30 món Việt Nam** |
| 🖼️ Tổng ảnh training | **25,136 ảnh** |
| ⏱️ Thời gian train | **~3 giờ (Colab T4 GPU)** |

---

## 🏗️ Kiến trúc hệ thống

```
┌─────────────────┐     ┌──────────────────────────────────────┐     ┌──────────────────┐
│   React 18      │────▶│           Flask REST API             │────▶│  EfficientNet-B0 │
│   Frontend      │◀────│                                      │◀────│  (.h5 model)     │
└─────────────────┘     │  /api/predict   → Nhận diện ảnh     │     └──────────────────┘
                        │  /api/nutrition → Tra cứu dinh dưỡng│              │
                        │  /api/profile   → Hồ sơ người dùng  │     ┌──────────────────┐
                        │  /api/menu      → Gợi ý thực đơn    │────▶│  CSV Dinh dưỡng  │
                        │  /api/chat      → Chatbot AI         │     │  (Viện DD Quốc  │
                        └──────────────────────────────────────┘     │   gia VN)        │
                                         │                           └──────────────────┘
                                         ▼
                              ┌──────────────────┐
                              │  JSON Storage    │
                              │  (Lịch sử ăn)   │
                              └──────────────────┘
```

**Luồng xử lý khi nhận diện ảnh:**

```
Upload ảnh → Resize 224×224 → Normalize → EfficientNet → Top-3 dự đoán
    → Tra CSV dinh dưỡng → Tính theo khẩu phần → Hiển thị + Lưu lịch sử
```

---

## 🛠️ Công nghệ sử dụng

| Nhóm | Công nghệ | Mục đích |
|------|-----------|----------|
| **AI / Model** | EfficientNet-B0 | Mạng CNN phân loại ảnh |
| | Transfer Learning (ImageNet) | Pretrained weights |
| | TensorFlow / Keras | Framework huấn luyện |
| | Google Colab T4 GPU | Môi trường train |
| **Backend** | Python / Flask | REST API server |
| | Pandas / NumPy | Xử lý dữ liệu CSV |
| | Pillow | Tiền xử lý ảnh |
| | JSON Storage | Lưu lịch sử ăn uống |
| **Frontend** | React 18 + Vite | Giao diện web |
| | Axios | Giao tiếp API |
| | CSS-in-JS | Responsive UI |
| | LocalStorage | Lưu hồ sơ người dùng |
| **Dữ liệu** | 30VNFoods (Kaggle) | Dataset training |
| | CSV Viện Dinh dưỡng VN | Dữ liệu dinh dưỡng |

---

## ⚙️ Cài đặt

### Yêu cầu hệ thống

- Python 3.10+
- Node.js 18+
- npm hoặc yarn
- (Tuỳ chọn) GPU với CUDA để inference nhanh hơn

### 1. Clone repository

```bash
git clone https://github.com/your-username/vn-food-recognition.git
cd vn-food-recognition
```

### 2. Cài đặt Backend (Flask)

```bash
cd backend

# Tạo virtual environment
python -m venv venv
source venv/bin/activate        # Linux/macOS
# hoặc: venv\Scripts\activate   # Windows

# Cài đặt dependencies
pip install -r requirements.txt
```

**requirements.txt:**
```
tensorflow==2.13.0
flask==2.3.3
flask-cors==4.0.0
pillow==10.0.0
numpy==1.24.3
pandas==2.0.3
```

### 3. Tải model đã train

```bash
# Tải file model (.h5) và đặt vào thư mục backend/model/
mkdir -p backend/model
# Đặt file efficientnet_food.h5 vào đây
```

> 📥 **Tải model tại đây:** [Google Drive – Model EfficientNet-B0](https://drive.google.com/drive/folders/1kKQF0AF3C2wxQlMI2ovL76uc4YSbstR3?usp=sharing)

> 💡 Hoặc tự train lại model theo hướng dẫn bên dưới.

### 4. Chạy Backend

```bash
cd backend
python app.py
# Server chạy tại: http://localhost:5000
```

### 5. Cài đặt & chạy Frontend (React)

```bash
cd frontend
npm install
npm run dev
# Giao diện tại: http://localhost:5173
```

---

## 📁 Cấu trúc thư mục

```
vn-food-recognition/
│
├── backend/
│   ├── app.py                  # Flask app chính
│   ├── requirements.txt
│   ├── model/
│   │   └── efficientnet_food.h5   # Model đã train
│   ├── data/
│   │   └── nutrition.csv          # Dữ liệu dinh dưỡng
│   ├── routes/
│   │   ├── predict.py             # Route nhận diện ảnh
│   │   ├── nutrition.py           # Route tra cứu dinh dưỡng
│   │   ├── profile.py             # Route hồ sơ người dùng
│   │   ├── menu.py                # Route gợi ý thực đơn
│   │   └── chat.py                # Route chatbot
│   ├── utils/
│   │   ├── preprocess.py          # Tiền xử lý ảnh
│   │   ├── tdee.py                # Tính TDEE
│   │   └── menu_generator.py      # Tạo thực đơn
│   └── storage/
│       └── history.json           # Lịch sử ăn uống
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── components/
│   │   │   ├── ImageUpload.jsx    # Upload & hiển thị kết quả
│   │   │   ├── NutritionCard.jsx  # Card thông tin dinh dưỡng
│   │   │   ├── Profile.jsx        # Form hồ sơ cá nhân
│   │   │   ├── MenuPlanner.jsx    # Lên thực đơn theo tuần
│   │   │   ├── FoodSuggest.jsx    # Gợi ý món ăn
│   │   │   ├── ChatBot.jsx        # Chatbot dinh dưỡng
│   │   │   └── History.jsx        # Lịch sử + biểu đồ
│   │   └── services/
│   │       └── api.js             # Axios API calls
│   ├── package.json
│   └── vite.config.js
│
├── training/
│   └── train_model.ipynb          # Notebook huấn luyện (Google Colab)
│
└── README.md
```

---

## 🏋️ Huấn luyện Model

### Dữ liệu

- **Dataset:** [30VNFoods trên Kaggle](https://www.kaggle.com/datasets/quandang/vietnamese-foods)
- **Tổng số ảnh:** 25,136 ảnh
- **Số lớp:** 30 món ăn Việt Nam
- **Phân chia:** Train 70% / Val 15% / Test 15%

### Quy trình train (6 bước)

#### Bước 1 — Tiền xử lý ảnh
```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=20,
    width_shift_range=0.2,
    height_shift_range=0.2,
    horizontal_flip=True,
    zoom_range=0.2
)

train_gen = train_datagen.flow_from_directory(
    'data/train',
    target_size=(224, 224),   # Chuẩn đầu vào EfficientNet
    batch_size=32,
    class_mode='categorical'
)
```

#### Bước 2 — Xây dựng model (Transfer Learning)
```python
from tensorflow.keras.applications import EfficientNetB0
from tensorflow.keras import layers, Model

# Load EfficientNet-B0 pretrained ImageNet, bỏ lớp đầu ra gốc
base_model = EfficientNetB0(weights='imagenet', include_top=False, input_shape=(224, 224, 3))
base_model.trainable = False   # Freeze toàn bộ base

# Thêm "head" mới cho 30 món Việt Nam
x = base_model.output
x = layers.GlobalAveragePooling2D()(x)
x = layers.Dense(256, activation='relu')(x)
x = layers.Dropout(0.3)(x)
output = layers.Dense(30, activation='softmax')(x)

model = Model(inputs=base_model.input, outputs=output)
```

#### Bước 3 — Phase 1: Train head (10 epochs)
```python
model.compile(
    optimizer='adam',           # LR mặc định = 1e-3
    loss='categorical_crossentropy',
    metrics=['accuracy']
)

history1 = model.fit(
    train_gen,
    epochs=10,
    validation_data=val_gen,
    callbacks=[EarlyStopping(patience=3, restore_best_weights=True)]
)
```

#### Bước 4 — Phase 2: Fine-tune (20 epochs)
```python
from tensorflow.keras.optimizers import Adam

# Mở khóa 30 lớp cuối của EfficientNet
base_model.trainable = True
for layer in base_model.layers[:-30]:
    layer.trainable = False

# Dùng learning rate rất nhỏ để không phá kiến thức cũ
model.compile(
    optimizer=Adam(1e-5),
    loss='categorical_crossentropy',
    metrics=['accuracy']
)

history2 = model.fit(
    train_gen,
    epochs=20,
    validation_data=val_gen,
    callbacks=[
        EarlyStopping(patience=5, restore_best_weights=True),
        ModelCheckpoint('best_model.h5', save_best_only=True)
    ]
)
```

#### Bước 5 — Đánh giá & Xuất model
```python
# Đánh giá trên tập Test
loss, acc = model.evaluate(test_gen)
print(f"Top-1 Accuracy: {acc:.4f}")   # → 85.42%

# Xuất model
model.save('model/efficientnet_food.h5')
```

> 🚀 Chạy toàn bộ trên **Google Colab** với GPU T4 miễn phí — mở file `training/train_model.ipynb`

---

## 🌐 API Reference

### `POST /api/predict`
Nhận diện món ăn từ ảnh.

**Request:** `multipart/form-data`
```
image: <file>
```

**Response:**
```json
{
  "predictions": [
    { "food": "Phở bò", "confidence": 0.87, "food_id": "pho_bo" },
    { "food": "Bún bò Huế", "confidence": 0.09, "food_id": "bun_bo_hue" },
    { "food": "Bún riêu", "confidence": 0.04, "food_id": "bun_rieu" }
  ],
  "process_time_ms": 1240
}
```

---

### `GET /api/nutrition/:food_id?serving=1`
Tra cứu dinh dưỡng theo khẩu phần.

**Query params:** `serving` = 0.5 | 1 | 1.5 | 2

**Response:**
```json
{
  "food": "Phở bò",
  "serving": 1,
  "serving_weight_g": 500,
  "nutrition": {
    "calories": 450,
    "protein_g": 25,
    "carbs_g": 55,
    "fat_g": 12,
    "fiber_g": 2
  }
}
```

---

### `POST /api/profile`
Lưu hồ sơ người dùng và tính TDEE.

**Request:**
```json
{
  "name": "Nguyễn Văn A",
  "age": 25,
  "gender": "male",
  "weight_kg": 70,
  "height_cm": 170,
  "activity_level": "moderate",
  "goal": "lose_weight"
}
```

**Response:**
```json
{
  "tdee": 2400,
  "target_calories": 1900,
  "bmr": 1700,
  "profile_id": "user_abc123"
}
```

---

### `GET /api/menu?profile_id=xxx&days=7`
Lấy thực đơn gợi ý theo hồ sơ.

**Response:**
```json
{
  "menu": [
    {
      "day": 1,
      "date": "2025-11-10",
      "meals": {
        "breakfast": { "food": "Bánh mì trứng", "calories": 350, "nutrition": {...} },
        "lunch":     { "food": "Cơm gà xối mỡ", "calories": 600, "nutrition": {...} },
        "dinner":    { "food": "Canh chua cá", "calories": 400, "nutrition": {...} }
      },
      "total_calories": 1350,
      "remaining_calories": 550
    }
    // ... 6 ngày tiếp theo
  ],
  "weekly_summary": {
    "avg_calories": 1870,
    "avg_protein_g": 85,
    "target_calories": 1900
  }
}
```

---

### `POST /api/menu/suggest`
Gợi ý món ăn phù hợp theo mục tiêu và calories còn lại.

**Request:**
```json
{
  "profile_id": "user_abc123",
  "meal_type": "lunch",
  "remaining_calories": 600,
  "preferences": ["ít dầu mỡ", "nhiều protein"]
}
```

**Response:**
```json
{
  "suggestions": [
    { "food": "Bún gà", "calories": 380, "protein_g": 30, "match_score": 0.95 },
    { "food": "Cơm tấm sườn", "calories": 550, "protein_g": 28, "match_score": 0.88 }
  ]
}
```

---

### `POST /api/chat`
Chatbot tư vấn dinh dưỡng.

**Request:**
```json
{
  "profile_id": "user_abc123",
  "message": "Tôi muốn ăn trưa nhẹ, khoảng 400 calo, nhiều đạm"
}
```

**Response:**
```json
{
  "reply": "Bạn có thể thử Bún gà (~380 kcal, 30g protein) hoặc Gỏi cuốn tôm thịt (~280 kcal, 20g protein). Cả hai đều ít dầu mỡ và phù hợp mục tiêu giảm cân của bạn!",
  "suggestions": ["Bún gà", "Gỏi cuốn tôm thịt"]
}
```

---

### `GET /api/history/:profile_id?days=7`
Lấy lịch sử ăn uống.

**Response:**
```json
{
  "history": [
    {
      "date": "2025-11-10",
      "meals": [...],
      "total_calories": 1850,
      "target_calories": 1900,
      "status": "on_track"
    }
  ],
  "chart_data": {
    "labels": ["T2", "T3", "T4", "T5", "T6", "T7", "CN"],
    "calories": [1850, 2100, 1780, 1900, 1650, 2000, 1750],
    "target": 1900
  }
}
```

---

## 🗃️ Dữ liệu

### Dataset ảnh — 30VNFoods

| Thuộc tính | Giá trị |
|-----------|---------|
| Nguồn | [Kaggle - quandang/vietnamese-foods](https://www.kaggle.com/datasets/quandang/vietnamese-foods) |
| Tổng ảnh | 25,136 |
| Số lớp | 30 món |
| Ảnh/lớp | ~838 |
| Định dạng | JPG, PNG |

**30 món ăn được hỗ trợ:**
> Bánh mì, Bánh xèo, Bún bò Huế, Bún chả, Bún gà, Bún riêu, Bún thịt nướng, Cơm gà, Cơm sườn, Cơm tấm, Chả giò, Gỏi cuốn, Hủ tiếu, Lẩu thái, Mì Quảng, Phở bò, Phở gà, Xôi, Bánh canh, Bò kho, Bánh cuốn, Cháo, Cao lầu, Cơm chiên dương châu, Bún đậu mắm tôm, Bánh chưng, Nem cuốn, Súp, Canh chua, Bánh ướt

### Dữ liệu dinh dưỡng

- **Nguồn:** Viện Dinh dưỡng Quốc gia Việt Nam
- **Format:** CSV, lưu offline trong `backend/data/nutrition.csv`
- **Các trường:** `food_id, food_name, serving_g, calories, protein_g, carbs_g, fat_g, fiber_g`

---

## 🚀 Hướng phát triển

- [ ] Mở rộng lên **100+ món ăn** Việt Nam
- [ ] Nhận diện **realtime qua webcam**
- [ ] **Ước tính khẩu phần tự động** từ ảnh (không cần chọn tay)
- [ ] Tích hợp với **thiết bị đeo thông minh** (smartwatch)
- [ ] **App mobile** (React Native)
- [ ] Hỗ trợ đa ngôn ngữ (Anh, Nhật)

---

## 👤 Tác giả

| | |
|--|--|
| **Họ tên** | [Nguyễn Văn Long] |
| **Trường** | Đại học Đại Nam |
| **Khoa** | Công nghệ Thông tin |
| **GitHub** | (https://github.com/Nguyenlong2004) |
| **Email** | nguyenvanlong978767@gmail.com |

---

## 📄 License

MIT License · Xem chi tiết tại [LICENSE](LICENSE)

---

<div align="center">

Made with ❤️ for Vietnamese Food & Health

⭐ Nếu project có ích, hãy để lại một Star!

</div>
