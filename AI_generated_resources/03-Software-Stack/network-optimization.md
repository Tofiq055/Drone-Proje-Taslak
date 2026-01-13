# 📡 Ağ Optimizasyonu: Sunucu-Drone İletişimi

> **"En hızlı kod, asla çalışmayan koddur - ama en hızlı veri, asla gönderilmeyen veridir."**

---

## 📋 İçindekiler

- [İletişim Zorlukları](#-iletişim-zorlukları)
- [Bant Genişliği Optimizasyonu](#-bant-genişliği-optimizasyonu)
- [Latency Azaltma](#-latency-azaltma)
- [Protokol Seçimi](#-protokol-seçimi)
- [Offline Çalışma](#-offline-çalışma)

---

## ⚠️ İletişim Zorlukları

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#e63946', 'primaryTextColor': '#fff', 'lineColor': '#a8dadc'}}}%%
flowchart LR
    subgraph Challenges["Zorluklar"]
        A[📶 Sınırlı Bant\n4G: 10-50Mbps]
        B[⏱️ Yüksek Latency\n100-500ms]
        C[🔌 Bağlantı Kopması\nUçuşta sık]
        D[🔋 Güç Tüketimi\nRadyo pahalı]
    end
    
    style A fill:#e63946,stroke:#9d0208,color:#fff
    style B fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style C fill:#e9c46a,stroke:#f4a261,color:#1a1a2e
    style D fill:#457b9d,stroke:#1d3557,color:#fff
```

| Zorluk | Etki | Önlem |
|--------|------|-------|
| Düşük bant genişliği | Veri gecikmesi | Sıkıştırma |
| Yüksek latency | Kontrol gecikmesi | Edge processing |
| Bağlantı kopması | Veri kaybı | Offline buffer |
| Güç tüketimi | Uçuş süresi kısalır | Batch gönderim |

---

## 📦 Bant Genişliği Optimizasyonu

### Veri Sıkıştırma Stratejileri

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[Ham Veri\n100 MB] --> B{Sıkıştırma Tipi}
    B -->|Lossy| C[JPEG/WebP\n10 MB]
    B -->|Lossless| D[PNG/ZSTD\n40 MB]
    B -->|Delta| E[Fark Verisi\n5 MB]
    
    style A fill:#e63946,stroke:#9d0208,color:#fff
    style C fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style D fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style E fill:#2a9d8f,stroke:#264653,color:#fff
```

| Strateji | Oran | Kalite Kaybı | Kullanım |
|----------|------|--------------|----------|
| JPEG (Q80) | 10:1 | ⚠️ Az | RGB görüntü |
| WebP | 15:1 | ⚠️ Az | Modern sistem |
| ZSTD | 3:1 | ✅ Yok | Termal veri |
| Delta | 20:1 | ✅ Yok | Ardışık frame |

### Örnek: Delta Encoding

```python
import numpy as np
import zstandard as zstd

def delta_encode(current: np.ndarray, previous: np.ndarray) -> bytes:
    """
    İki frame arasındaki farkı sıkıştır.
    """
    delta = current.astype(np.int16) - previous.astype(np.int16)
    
    # ZSTD ile sıkıştır
    cctx = zstd.ZstdCompressor(level=3)
    compressed = cctx.compress(delta.tobytes())
    
    return compressed
```

---

## ⚡ Latency Azaltma

### Edge Processing

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart LR
    subgraph Before["❌ Merkezi İşleme"]
        A1[Sensör] --> B1[Sunucu] --> C1[Karar]
        B1 -.->|500ms RTT| B1
    end
    
    subgraph After["✅ Edge Processing"]
        A2[Sensör] --> B2[Jetson] --> C2[Karar]
        B2 -.->|10ms local| B2
    end
    
    style Before fill:#e63946,stroke:#9d0208,color:#fff
    style After fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
```

| İşlem | Merkezi | Edge |
|-------|---------|------|
| Nesne tespiti | 500-1000ms | 30-50ms |
| Engel kaçınma | 300-500ms | 10-20ms |
| Yol planlama | 200-400ms | 50-100ms |

### Kritik vs Non-Kritik Ayrımı

| Veri | Kritiklik | Latency Limit | Öncelik |
|------|-----------|---------------|---------|
| Acil dur komutu | 🔴 Kritik | <100ms | Yüksek |
| Telemetri | 🟡 Önemli | <500ms | Orta |
| Görüntü stream | 🟢 Normal | <2s | Düşük |
| FL gradient | 🟢 Normal | <5s | Düşük |

---

## 📡 Protokol Seçimi

### Protokol Karşılaştırması

| Protokol | Latency | Overhead | Güvenilirlik | Kullanım |
|----------|---------|----------|--------------|----------|
| **UDP** | Düşük | Düşük | ❌ | Telemetri |
| **TCP** | Orta | Orta | ✅ | Dosya transfer |
| **MQTT** | Düşük | Düşük | ⚠️ QoS | IoT mesaj |
| **WebSocket** | Orta | Düşük | ✅ | Real-time |
| **MAVLink** | Düşük | Düşük | ⚠️ | Uçuş kontrol |

### MQTT Konfigürasyonu

```python
import paho.mqtt.client as mqtt

client = mqtt.Client()
client.connect("broker.server.com", 1883)

# QoS seviyeleri
# 0: At most once (en hızlı, kayıp olabilir)
# 1: At least once (tekrar olabilir)
# 2: Exactly once (en yavaş, garantili)

# Telemetri için QoS 0
client.publish("drone/1/telemetry", payload, qos=0)

# Komut için QoS 1
client.publish("drone/1/command", payload, qos=1)
```

---

## 📴 Offline Çalışma

### Buffer Stratejisi

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
stateDiagram-v2
    [*] --> Online
    Online --> Buffering: Bağlantı koptu
    Buffering --> Buffering: Veri biriktir
    Buffering --> Syncing: Bağlantı geldi
    Syncing --> Online: Sync tamamlandı
    Buffering --> Overflow: Buffer dolu
    Overflow --> Purge: Eski veri sil
    Purge --> Buffering: Devam et
```

### Öncelik Tabanlı Buffer

| Öncelik | Veri Tipi | Buffer Süresi | Overflow |
|---------|-----------|---------------|----------|
| 1 | Kritik telemetri | Sınırsız | Asla sil |
| 2 | Termal anomali | 1 saat | FIFO |
| 3 | Video frame | 5 dakika | Düşük kalite |
| 4 | Debug log | 10 dakika | Sil |

---

## ✅ Optimizasyon Checklist

- [ ] Delta encoding aktif
- [ ] ZSTD sıkıştırma açık
- [ ] Edge inference çalışıyor
- [ ] MQTT QoS ayarlandı
- [ ] Offline buffer test edildi

---

> 💡 **Sonraki:** [05-Simulation/gazebo-to-real-transition.md](../05-Simulation/gazebo-to-real-transition.md)
