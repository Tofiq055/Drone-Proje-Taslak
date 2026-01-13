# 🔄 Simülasyondan Gerçeğe Geçiş

> **"Simülasyonda her şey mükemmel çalışır - gerçek dünya çok daha ilginç."**

---

## 📋 İçindekiler

- [Sim2Real Gap](#-sim2real-gap)
- [Sensör Kalibrasyon Farkları](#-sensör-kalibrasyon-farkları)
- [Fizik Farklılıkları](#-fizik-farklılıkları)
- [Geçiş Protokolü](#-geçiş-protokolü)
- [İlk Uçuş Checklist](#-ilk-uçuş-checklist)

---

## 🌉 Sim2Real Gap

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e', 'lineColor': '#a8dadc'}}}%%
flowchart LR
    subgraph Sim["🎮 Simülasyon"]
        S1[Mükemmel sensör]
        S2[İdeal fizik]
        S3[Tutarlı ortam]
    end
    
    subgraph Real["🌍 Gerçek Dünya"]
        R1[Gürültülü sensör]
        R2[Karmaşık fizik]
        R3[Değişken ortam]
    end
    
    Sim -->|Gap| Real
    
    style Sim fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style Real fill:#e63946,stroke:#9d0208,color:#fff
```

| Simülasyon | Gerçek Dünya |
|------------|--------------|
| ✅ Sensör gürültüsü yok | ⚠️ Her sensör gürültülü |
| ✅ Mükemmel GPS | ⚠️ GPS drift, multipath |
| ✅ Sabit rüzgar | ⚠️ Türbülans, ani rüzgar |
| ✅ Aynı ışık | ⚠️ Gölge, parlama |

---

## 📡 Sensör Kalibrasyon Farkları

### IMU Kalibrasyonu

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#f4a261', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[🔧 IMU Kalibrasyon] --> B[Gyro Bias]
    A --> C[Accel Bias]
    A --> D[Sıcaklık Kompanzasyon]
    
    B --> E[Statik kalibrasyon]
    C --> F[6-nokta kalibrasyon]
    D --> G[Termal model]
    
    style A fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style E fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style F fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style G fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
```

| Parametre | Simülasyon | Gerçek | Etki |
|-----------|------------|--------|------|
| Gyro bias | 0 | ±0.5 °/s | Drift |
| Accel bias | 0 | ±0.1 m/s² | Yükseklik hatası |
| Noise | Minimal | Yüksek | Titreme |

### Kamera Kalibrasyonu

```bash
# RealSense kalibrasyon kontrolü
rs-enumerate-devices --calib

# İç parametreler (intrinsics)
# - Focal length (fx, fy)
# - Principal point (cx, cy)
# - Distortion coefficients (k1, k2, p1, p2)
```

---

## 🌬️ Fizik Farklılıkları

### Aerodinamik

| Faktör | Simülasyon | Gerçek Dünya |
|--------|------------|--------------|
| Rüzgar | Sabit vektör | Türbülanslı, değişken |
| Ground effect | Basit model | Karmaşık |
| Prop wash | Yok | Önemli |
| Batarya ağırlık | Sabit | Düşüyor |

### Adaptif Kontrol

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#2a9d8f', 'primaryTextColor': '#fff'}}}%%
flowchart LR
    A[Sensor Input] --> B[State Estimation]
    B --> C{Model Error?}
    C -->|Büyük| D[Model Update]
    C -->|Küçük| E[Normal Control]
    D --> F[Adaptive Gain]
    F --> E
    
    style A fill:#457b9d,stroke:#1d3557,color:#fff
    style D fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style E fill:#2a9d8f,stroke:#264653,color:#fff
```

---

## 📋 Geçiş Protokolü

### Aşamalı Yaklaşım

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[1. SITL\nTam simülasyon] --> B[2. HITL\nHardware-in-loop]
    B --> C[3. Tethered\nBağlı uçuş]
    C --> D[4. Short Flight\nKısa uçuş]
    D --> E[5. Full Mission\nTam görev]
    
    style A fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style B fill:#a8dadc,stroke:#457b9d,color:#1a1a2e
    style C fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style D fill:#e9c46a,stroke:#f4a261,color:#1a1a2e
    style E fill:#2a9d8f,stroke:#264653,color:#fff
```

| Aşama | Açıklama | Süre | Risk |
|-------|----------|------|------|
| SITL | Tam simülasyon | - | Sıfır |
| HITL | Gerçek FCU, simüle sensör | 2 saat | Düşük |
| Tethered | İp bağlı, kısa uçuş | 30 dk | Orta |
| Short Flight | 1-2m yükseklik | 5 dk | Orta |
| Full Mission | Tam otonom | - | Yüksek |

---

## ✅ İlk Uçuş Checklist

### Pre-Flight

- [ ] Batarya %100 şarjlı
- [ ] Tüm vidalar sıkı
- [ ] Prop'lar hasarsız
- [ ] GPS lock (min 10 uydu)
- [ ] Compass calibrated
- [ ] RC bağlantısı test
- [ ] Failsafe ayarları kontrol
- [ ] Acil dur butonu hazır

### Ortam Kontrolü

- [ ] Rüzgar < 5 m/s
- [ ] Güneşli/bulutlu (yağmur yok)
- [ ] Açık alan (engel yok)
- [ ] İzin var (SHGM)
- [ ] Gözlemci hazır

### İlk Kalkış

1. **Arm** - Motorları aç (yerde)
2. **Throttle %10** - Motor dönüşü kontrol
3. **Kalkış** - Yavaşça 0.5m
4. **Hover** - 10 saniye bekle
5. **Yaw test** - Sağa/sola dön
6. **Pitch/Roll** - Hafif hareket
7. **İniş** - Yavaşça indir

### Acil Durumlar

| Durum | Eylem |
|-------|-------|
| ⚠️ Titreme | Hemen iniş |
| ⚠️ Drift | RTH aktif |
| 🔴 Motor arıza | Acil iniş |
| 🔴 Batarya kritik | Anında iniş |

---

> 💡 **Sonraki:** [05-Simulation/hardware-testing-protocol.md](./hardware-testing-protocol.md)
