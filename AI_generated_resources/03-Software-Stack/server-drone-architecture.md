# 🏗️ Sunucu-Drone Mimarisi: Merkezi Koordinasyon Sistemi

> **"Tek bir karınca değil, koloni başarır."** - Bu prensip, sunucu-drone mimarimizin temelidir.

---

## 📋 İçindekiler

- [Mimari Genel Bakış](#-mimari-genel-bakış)
- [Katman 1: Bulut/Sunucu](#️-katman-1-bulutsunucu)
- [Katman 2: Edge (Jetson)](#-katman-2-edge-jetson)
- [Katman 3: Drone Platform](#-katman-3-drone-platform)
- [Veri Akışı](#-veri-akışı)
- [İletişim Protokolleri](#-iletişim-protokolleri)

---

## 🌐 Mimari Genel Bakış

```mermaid
flowchart TB
    subgraph CLOUD["☁️ Katman 1: Bulut/Sunucu"]
        SAT[🛰️ Uydu Verisi<br/>250-300GB]
        GPU[🖥️ RTX 5080/5090<br/>Model Training]
        COORD[🎯 Sürü<br/>Koordinatörü]
        FL_AGG[🔒 FL<br/>Aggregator]
    end
    
    subgraph EDGE["📡 Katman 2: Edge - Jetson"]
        J1[🧠 Jetson 1]
        J2[🧠 Jetson 2]
        J3[🧠 Jetson 3]
    end
    
    subgraph DRONE["🚁 Katman 3: Drone Platform"]
        D1[✈️ PX4<br/>Flight Controller]
        D2[✈️ PX4<br/>Flight Controller]
        D3[✈️ PX4<br/>Flight Controller]
    end
    
    SAT --> GPU
    GPU --> COORD
    COORD <-->|Görev| J1 & J2 & J3
    FL_AGG <-->|Gradient| J1 & J2 & J3
    
    J1 --> D1
    J2 --> D2
    J3 --> D3
    
    style CLOUD fill:#1a1a2e,stroke:#16213e,color:#fff
    style EDGE fill:#0f3460,stroke:#16213e,color:#fff
    style DRONE fill:#533483,stroke:#16213e,color:#fff
```

---

## 🖥️ Katman 1: Bulut/Sunucu

### Donanım Spesifikasyonu

| Bileşen | Spesifikasyon | Görev |
|---------|---------------|-------|
| **GPU** | RTX 5080/5090 | Model eğitimi, veri işleme |
| **CPU** | AMD EPYC / Intel Xeon | ETL, koordinasyon |
| **RAM** | 128-256 GB DDR5 | Büyük veri setleri |
| **Storage** | NVMe RAID | 250-300GB uydu verisi |
| **Network** | 10Gbps | Hızlı veri transferi |

### Yazılım Bileşenleri

```mermaid
flowchart LR
    subgraph Server["Sunucu Yazılım Stack"]
        OS[Ubuntu 22.04]
        DOCKER[Docker + K8s]
        ROS[ROS 2 Humble]
        CUDA[CUDA 12.x]
        TRT[TensorRT]
        FLOWER[Flower FL]
    end
```

| Bileşen | Sürüm | Rol |
|---------|-------|-----|
| Ubuntu Server | 22.04 LTS | Ana OS |
| Docker | 24.x | Container runtime |
| ROS 2 | Humble | Robot middleware |
| CUDA | 12.x | GPU computing |
| TensorRT | 8.6+ | Inference optimization |
| Flower | 1.x | Federated Learning |

### Sunucu Görevleri

1. **Uydu Verisi İşleme**
   - 250-300GB ham veri alımı
   - GeoTIFF → COG dönüşümü
   - Tile generation

2. **Model Eğitimi**
   - Termal anomali detection
   - Object detection models
   - Global model maintenance

3. **Sürü Koordinasyonu**
   - Görev dağıtımı
   - Bölge optimizasyonu
   - Çakışma önleme

4. **Federated Learning Aggregation**
   - Gradient toplama
   - Model güncellemesi
   - Edge dağıtımı

---

## 📡 Katman 2: Edge (Jetson)

### Her Jetson Orin Nano

| Görev | Açıklama |
|-------|----------|
| **Real-time Inference** | TensorRT ile nesne tespiti |
| **Sensör Füzyonu** | RealSense + Termal birleştirme |
| **Lokal Karar** | Engel kaçınma, yol planlama |
| **FL Local Training** | Lokal veriyle model güncelleme |

### ROS 2 Node Yapısı

```mermaid
flowchart TB
    subgraph Jetson["Jetson ROS 2 Nodes"]
        RS[realsense_node]
        TH[thermal_node]
        FUS[fusion_node]
        DET[detection_node]
        NAV[navigation_node]
        MAV[mavros_node]
    end
    
    RS -->|PointCloud| FUS
    TH -->|ThermalImage| FUS
    FUS --> DET
    DET --> NAV
    NAV --> MAV
```

---

## 🚁 Katman 3: Drone Platform

### Uçuş Kontrolü

| Bileşen | Seçenek | Açıklama |
|---------|---------|----------|
| **Firmware** | PX4 / ArduPilot | Otonom uçuş |
| **Protocol** | MAVLink | Komut iletişimi |
| **Companion** | Jetson Orin Nano | AI partner |

### MAVLink İletişimi

```mermaid
sequenceDiagram
    participant J as Jetson
    participant PX4 as Flight Controller
    participant GCS as Yer İstasyonu
    
    J->>PX4: MAVROS setpoint
    PX4->>PX4: Flight execution
    PX4->>J: Telemetry
    PX4->>GCS: Heartbeat
```

---

## 🔄 Veri Akışı

### Aşağı Akış (Sunucu → Drone)

```mermaid
flowchart LR
    SAT[Uydu] --> SRV[Sunucu]
    SRV --> TASK[Görev Paketi]
    TASK --> DRONE[Drone Fleet]
```

1. Uydu verisi işlenir (250GB → 500MB görev)
2. Görev koordinatları belirlenir
3. Tile'lar hazırlanır
4. Drone'lara dağıtılır

### Yukarı Akış (Drone → Sunucu)

```mermaid
flowchart LR
    DRONE[Drone] --> TEL[Telemetry]
    DRONE --> GRAD[FL Gradient]
    DRONE --> DATA[Termal Veri]
    TEL & GRAD & DATA --> SRV[Sunucu]
    SRV --> TWIN[Digital Twin]
```

1. Telemetri (konum, durum)
2. FL gradient'ları
3. Termal tarama sonuçları
4. Digital Twin güncelleme

---

## 📡 İletişim Protokolleri

### Protokol Seçimi

| Protokol | Kullanım | Bant Genişliği |
|----------|----------|----------------|
| **MQTT** | Telemetry | Düşük |
| **DDS** | ROS 2 | Orta |
| **HTTP/2** | Veri transfer | Yüksek |
| **MAVLink** | Uçuş komutları | Düşük |

### Ağ Topolojisi

```mermaid
flowchart TB
    SRV[Sunucu<br/>Statik IP] --> ROUTER[5G/WiFi Router]
    ROUTER --> D1[Drone 1]
    ROUTER --> D2[Drone 2]
    ROUTER --> D3[Drone 3]
    
    D1 <-->|Mesh| D2
    D2 <-->|Mesh| D3
```

### Latency Gereksinimleri

| İşlem | Max Latency | Kritiklik |
|-------|-------------|-----------|
| Acil dur | <100ms | 🔴 Kritik |
| Navigasyon | <500ms | 🟡 Önemli |
| Telemetry | <1000ms | 🟢 Normal |
| FL Sync | <5000ms | 🟢 Normal |

---

## 🔧 Kurulum Checklist

- [ ] Sunucu Ubuntu 22.04 + CUDA kuruldu
- [ ] Docker + ROS 2 container hazır
- [ ] Jetson JetPack 6.0 kuruldu
- [ ] PX4 SITL simülasyonu çalışıyor
- [ ] MAVLink bağlantısı test edildi

---

> 💡 **Sonraki:** [04-Roadmap/project-timeline.md](../04-Roadmap/project-timeline.md)
