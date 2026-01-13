# ⚠️ Güvenlik ve Etik Rehberi

> **"Teknoloji güçtür - güç sorumluluk getirir."**

---

## 📋 İçindekiler

- [Türkiye Drone Mevzuatı](#-türkiye-drone-mevzuatı)
- [Termal Kamera Etik Sınırları](#-termal-kamera-etik-sınırları)
- [Veri Güvenliği](#-veri-güvenliği)
- [Uçuş Güvenliği](#-uçuş-güvenliği)

---

## 🇹🇷 Türkiye Drone Mevzuatı

### SHGM (Sivil Havacılık Genel Müdürlüğü) Kuralları

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#e63946', 'primaryTextColor': '#fff', 'lineColor': '#a8dadc', 'secondaryColor': '#457b9d'}}}%%
flowchart TD
    A[🚁 Drone Uçuşu] --> B{Ağırlık?}
    B -->|< 500g| C[IHA0\nKayıt gereksiz]
    B -->|500g - 4kg| D[IHA1\nOnline kayıt]
    B -->|4kg - 25kg| E[IHA2\nEhliyet gerekli]
    B -->|> 25kg| F[IHA3\nÖzel izin]
    
    style A fill:#e63946,stroke:#9d0208,color:#fff
    style C fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style D fill:#f4a261,stroke:#e76f51,color:#1a1a2e
    style E fill:#e63946,stroke:#9d0208,color:#fff
    style F fill:#9d0208,stroke:#6a040f,color:#fff
```

### Drone Sınıfları

| Sınıf | Ağırlık | Gereklilikler |
|-------|---------|---------------|
| **IHA0** | < 500g | Kayıt gereksiz, temel kurallar |
| **IHA1** | 500g - 4kg | Online kayıt, pilot belgesi yok |
| **IHA2** | 4kg - 25kg | Pilot belgesi zorunlu |
| **IHA3** | > 25kg | Özel izin, sigorta zorunlu |

### Yasak Bölgeler

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#e63946', 'primaryTextColor': '#fff'}}}%%
mindmap
  root((❌ Yasak Bölgeler))
    Havalimanları
      9km yarıçap
      Kesinlikle yasak
    Askeri Bölgeler
      Tüm askeri tesisler
      Özel izin gerekli
    Kalabalık Alanlar
      Stadyumlar
      Konserler
      Protestolar
    Özel Alanlar
      Cezaevleri
      Nükleer tesisler
      Kritik altyapı
```

### Uçuş Kuralları Özeti

| Kural | Açıklama |
|-------|----------|
| ✅ Gündüz uçuşu | Gün doğumu - gün batımı |
| ✅ Görüş mesafesi | Pilot drone'u görmeli (VLOS) |
| ✅ Maksimum yükseklik | 120 metre AGL |
| ❌ İnsanların üzeri | Yasak |
| ❌ Şehir merkezleri | Özel izin gerekli |

### Kayıt Süreci

1. [shgm.gov.tr](https://iha.shgm.gov.tr/) adresine git
2. E-Devlet ile giriş yap
3. Drone bilgilerini gir (seri no, marka, model)
4. Sorumluluk beyanını kabul et
5. Kayıt belgesini indir

---

## 🔥 Termal Kamera Etik Sınırları

### Gizlilik Kaygıları

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#e63946', 'primaryTextColor': '#fff', 'lineColor': '#a8dadc'}}}%%
flowchart LR
    subgraph ALLOWED["✅ İzin Verilen"]
        A1[Endüstriyel denetim]
        A2[Tarım izleme]
        A3[Arama kurtarma]
        A4[Yangın tespiti]
    end
    
    subgraph FORBIDDEN["❌ Yasak"]
        B1[Ev içi gözetleme]
        B2[Kişi takibi]
        B3[İzinsiz kayıt]
    end
    
    style ALLOWED fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style FORBIDDEN fill:#e63946,stroke:#9d0208,color:#fff
```

### Etik Kullanım Kuralları

| Durum | İzin | Açıklama |
|-------|------|----------|
| Bina dışı enerji denetimi | ✅ | Ticari/endüstriyel amaçlı |
| Tarım bitki stresi | ✅ | Kendi arazinde |
| Yangın tespiti | ✅ | Kamu güvenliği |
| Konut içi görüntüleme | ❌ | Gizlilik ihlali |
| Kişileri tanımlama | ❌ | Etik ihlal |
| İzinsiz kayıt paylaşımı | ❌ | Yasal suç |

### Veri Minimizasyonu İlkesi

```
Topla → Sadece gerekli veriyi
Sakla → Minimum süre
Paylaş → Sadece yetkililere
Sil → Amaca ulaşınca
```

---

## 🔒 Veri Güvenliği

### Veri Sınıflandırması

| Kategori | Örnek | Koruma Seviyesi |
|----------|-------|-----------------|
| **Herkese Açık** | Proje kodu (açık kaynak) | 🟢 Düşük |
| **İç Kullanım** | Telemetri logları | 🟡 Orta |
| **Gizli** | Termal görüntüler | 🔴 Yüksek |
| **Kişisel Veri** | Konum verileri | 🔴 KVKK kapsamı |

### KVKK Uyumu

**Kişisel Verilerin Korunması Kanunu (6698)**

| Madde | Gereklilik |
|-------|------------|
| Açık Rıza | Veri toplamadan önce onay |
| Amaç Sınırlaması | Belirlenen amaç dışı kullanım yasak |
| Veri Minimizasyonu | Sadece gerekli veri toplanmalı |
| Saklama Süresi | Belirlenen süre sonrası silme |
| Güvenlik | Şifreleme, erişim kontrolü |

---

## ✈️ Uçuş Güvenliği

### Pre-Flight Checklist

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4ecdc4', 'primaryTextColor': '#1a1a2e'}}}%%
flowchart TD
    A[🔋 Batarya Kontrolü] --> B[📡 GPS Sinyal]
    B --> C[🌤️ Hava Durumu]
    C --> D[🗺️ Uçuş Bölgesi]
    D --> E[👥 Çevre Kontrolü]
    E --> F{Tümü OK?}
    F -->|Evet| G[✅ Uçuşa Hazır]
    F -->|Hayır| H[❌ Uçuş İptal]
    
    style A fill:#4ecdc4,stroke:#26a69a,color:#1a1a2e
    style G fill:#2a9d8f,stroke:#264653,color:#fff
    style H fill:#e63946,stroke:#9d0208,color:#fff
```

### Acil Durum Prosedürleri

| Durum | Eylem |
|-------|-------|
| 🔋 Batarya Düşük | Hemen RTH (Return to Home) |
| 📡 Sinyal Kaybı | Otomatik RTH bekle |
| ⚠️ Motor Arızası | Acil iniş, uzak dur |
| 🌧️ Ani Hava | Derhal iniş |

---

## ✅ Güvenlik Checklist

- [ ] SHGM kaydı yapıldı
- [ ] Uçuş bölgesi kontrol edildi
- [ ] Hava durumu uygun
- [ ] Batarya %100
- [ ] GPS lock var
- [ ] Termal kayıt onayı alındı
- [ ] Acil durum planı hazır

---

> 💡 **Resmi Kaynak:** [SHGM İHA Portalı](https://iha.shgm.gov.tr/)
