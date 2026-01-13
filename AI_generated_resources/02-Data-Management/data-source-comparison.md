# 🛰️ Uydu Veri Kaynakları Karşılaştırması

> **"Doğru veri kaynağı seçimi, projenin başarısını belirler."**

---

## 📋 İçindekiler

- [Karşılaştırma Tablosu](#-karşılaştırma-tablosu)
- [Açık Veri Kaynakları](#-açık-veri-kaynakları)
- [Ticari Kaynaklar](#-ticari-kaynaklar)
- [Kendi Veri Toplama](#-kendi-veri-toplama)
- [Seçim Kriterleri](#-seçim-kriterleri)

---

## 📊 Karşılaştırma Tablosu

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e', 'lineColor': '#a8dadc'}}}%%
quadrantChart
    title Veri Kaynağı Karşılaştırması
    x-axis Düşük Maliyet --> Yüksek Maliyet
    y-axis Düşük Çözünürlük --> Yüksek Çözünürlük
    quadrant-1 Premium Ticari
    quadrant-2 Özel Drone
    quadrant-3 Açık Veri
    quadrant-4 Entry-Level Ticari
    Sentinel-2: [0.1, 0.5]
    Landsat: [0.1, 0.4]
    MODIS: [0.05, 0.2]
    Planet: [0.5, 0.6]
    Maxar: [0.9, 0.95]
    Drone DIY: [0.3, 0.85]
```

### Ana Karşılaştırma

| Kaynak | Çözünürlük | Maliyet | Termal | Temporal | API |
|--------|------------|---------|--------|----------|-----|
| **Sentinel-2** | 10m | Ücretsiz | ❌ | 5 gün | ✅ |
| **Landsat-8/9** | 30m | Ücretsiz | ✅ 100m | 16 gün | ✅ |
| **MODIS** | 250m-1km | Ücretsiz | ✅ | Günlük | ✅ |
| **Planet** | 3m | $$ | ❌ | Günlük | ✅ |
| **Maxar** | 30cm | $$$ | ⚠️ | Değişken | ✅ |
| **Drone DIY** | 1-5cm | Düşük | ✅ | İsteğe bağlı | - |

---

## 🌍 Açık Veri Kaynakları

### Sentinel-2 (ESA/Copernicus)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#457b9d', 'primaryTextColor': '#fff'}}}%%
flowchart LR
    A[🛰️ Sentinel-2A/2B] --> B[📥 Copernicus Hub]
    B --> C[🔄 Ön İşleme]
    C --> D[📊 Analiz]
    
    style A fill:#457b9d,stroke:#1d3557,color:#fff
    style B fill:#a8dadc,stroke:#457b9d,color:#1a1a2e
    style C fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style D fill:#2a9d8f,stroke:#264653,color:#fff
```

| Özellik | Değer |
|---------|-------|
| Çözünürlük | 10m (RGB), 20m (RedEdge), 60m (Atm) |
| Bantlar | 13 spektral bant |
| Tekrar Süresi | 5 gün (2 uydu) |
| Kapsama | Global |
| Veri Boyutu | ~1GB / tile |

**Kullanım Alanları:**
- ✅ Arazi örtüsü sınıflandırma
- ✅ Bitki sağlığı (NDVI)
- ✅ Su kütleleri izleme
- ❌ Termal analiz (bant yok)

### Landsat-8/9 (NASA/USGS)

| Özellik | Değer |
|---------|-------|
| Çözünürlük | 30m (OLI), 100m (TIRS) |
| Bantlar | 11 bant (2 termal) |
| Tekrar Süresi | 16 gün (tek), 8 gün (çift) |
| Termal Bantlar | Band 10, 11 (TIRS) |

**Termal Kullanım:**
- ✅ Yüzey sıcaklığı (LST)
- ✅ Kentsel ısı adası
- ✅ Yangın tespiti
- ⚠️ 100m çözünürlük (kaba)

### MODIS (NASA)

| Özellik | Değer |
|---------|-------|
| Çözünürlük | 250m - 1km |
| Tekrar Süresi | 1-2 gün |
| Kapsama | Global |
| Termal | ✅ Günlük LST ürünleri |

**Avantajlar:**
- ✅ Günlük veri
- ✅ Uzun zaman serisi (2000+)
- ✅ Hazır ürünler (LST, NDVI)

---

## 💰 Ticari Kaynaklar

### Planet Labs

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[🛰️ 200+ Dove Uydusu] --> B[📸 Günlük Global]
    B --> C[🔍 3m Çözünürlük]
    C --> D[💰 Abonelik Modeli]
    
    style A fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style D fill:#e63946,stroke:#9d0208,color:#fff
```

| Özellik | Değer |
|---------|-------|
| Çözünürlük | 3-5m |
| Temporal | Günlük |
| Maliyet | ~$5-15 / km² |
| API | ✅ REST API |

### Maxar (DigitalGlobe)

| Özellik | Değer |
|---------|-------|
| Çözünürlük | 30-50cm |
| Maliyet | ~$15-25 / km² |
| Kullanım | Yüksek detay gereken projeler |

---

## 🚁 Kendi Veri Toplama

### Drone ile Termal Haritalama

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#ff6b6b', 'primaryTextColor': '#fff'}}}%%
flowchart LR
    A[🚁 Drone\nTermal Kamera] --> B[📸 R-JPEG\nGörüntüler]
    B --> C[🔄 Mozaikleme\nPix4D/ODM]
    C --> D[🗺️ Ortomozaik\nGeoTIFF]
    
    style A fill:#ff6b6b,stroke:#c0392b,color:#fff
    style B fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style C fill:#e9c46a,stroke:#f4a261,color:#1a1a2e
    style D fill:#2a9d8f,stroke:#264653,color:#fff
```

| Avantaj | Dezavantaj |
|---------|------------|
| ✅ Çok yüksek çözünürlük (cm) | ❌ Sınırlı kapsama alanı |
| ✅ Istenilen zamanda | ❌ Uçuş izinleri |
| ✅ Termal detay | ❌ İşlem süresi |

---

## 🎯 Seçim Kriterleri

### Karar Ağacı

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[Proje Gereksinimi] --> B{Termal gerekli?}
    B -->|Evet| C{Çözünürlük?}
    B -->|Hayır| D[Sentinel-2]
    C -->|>100m OK| E[Landsat TIRS]
    C -->|<10m gerekli| F{Bütçe?}
    F -->|Düşük| G[Drone DIY]
    F -->|Yüksek| H[Ticari Termal]
    
    style A fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style D fill:#457b9d,stroke:#1d3557,color:#fff
    style E fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style G fill:#2a9d8f,stroke:#264653,color:#fff
    style H fill:#e63946,stroke:#9d0208,color:#fff
```

### Bu Proje İçin Öneri

| Amaç | Önerilen Kaynak |
|------|-----------------|
| Geniş alan haritası | Sentinel-2 + Landsat |
| Termal detay | Drone termal kamera |
| Günlük değişim | MODIS |

---

> 💡 **Sonraki:** [02-Data-Management/data-pipeline.md](./data-pipeline.md)
