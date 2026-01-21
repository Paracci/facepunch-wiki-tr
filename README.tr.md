# 🎮 Facepunch Wiki Türkçe Çeviri Projesi

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Node.js](https://img.shields.io/badge/Node.js-14+-green.svg)](https://nodejs.org/)
[![Status](https://img.shields.io/badge/Status-Active-success.svg)]()

> Facepunch oyunları için Türkçe wiki kaynağı.

> Bu depo yalnızca dokümantasyon içermektedir.  
> Web sitesinin kaynak kodu herkese açık değildir.

**🌐 Canlı Site:** [wiki.gmodtr.com](https://wiki.gmodtr.com/)

**🇬🇧 English version:** [README.md](README.md)
## 📋 İçindekiler

- [Proje Hakkında](#-proje-hakkında)
- [Özellikler](#-özellikler)
- [Teknolojiler](#teknolojiler)
- [Ekran Görüntüleri](#-ekran-görüntüleri)
- [Mimari](#mimari)
- [Performans](#-performans-metrikleri)
- [Katkıda Bulunma](#katkıda-bulunma-yolları)

## 🎯 Proje Hakkında

Bu proje, [wiki.facepunch.com](https://wiki.facepunch.com) sitesindeki İngilizce wiki sayfalarını otomatik olarak Türkçe'ye çeviren ve güncel tutan bir sistemdir. Amaç, Türk oyuncu topluluğuna anadilde teknik dokümantasyon sunmaktır.

### Desteklenen Wikiler

- ✅ **Garry's Mod** (GLua API - 6000+ sayfa)
- ✅ **Rust** (Oyun mekaniği ve API)
- ✅ **Steamworks** (Steam entegrasyon API'ları)
- ✅ **Facepunch Genel Wiki**

## ✨ Özellikler

### 🤖 Akıllı Çeviri Sistemi

- **Diff-Tabanlı Güncelleme**: Tam sayfa yerine sadece değişen kısımları çevirir
  - Token kullanımında %50-90 tasarruf
  - Daha hızlı güncelleme döngüsü
  - Maliyet optimizasyonu

- **Hibrit AI Modeli**: 
  - Birincil: Google Gemini AI
  - Yedek: DeepL API
  - Otomatik failover sistemi

- **Teknik İçerik Koruması**:
  - Kod blokları değişmeden korunur
  - Fonksiyon isimleri çevrilmez
  - API parametreleri orijinal kalır
  - HTML yapısı bozulmaz

### 🔄 Otomatik Güncelleme

- Saatlik kontrol sistemi
- Kaynak wiki'deki değişiklikleri algılar
- Sadece güncellenmiş sayfaları işler
- Hata durumunda kaldığı yerden devam eder

### 🔍 Gelişmiş Arama

- Tam metin araması
- Gerçek zamanlı sonuçlar
- Vurgulama ile sonuç gösterimi
- Kategori bazlı filtreleme

### 💾 Veri Güvenliği

- Tüm içerikler şifreli saklanır
- Otomatik yedekleme sistemi
- Versiyon kontrolü
- Cloud backup desteği

## Teknolojiler

### Backend
- **Python 3.8+** - Ana bot mantığı ve çeviri motoru
- **Node.js/Express** - Web sunucusu ve API

### AI & Çeviri
- **Google Gemini AI** - Birincil çeviri motoru
- **DeepL API** - Yedek çeviri servisi
- **Custom Validation** - Çeviri kalite kontrol sistemi

### Frontend
- **Vanilla JavaScript** - SPA (Single Page Application)
- **Modern CSS** - Responsive tasarım
- **Client-Side Encryption** - Güvenli içerik dağıtımı

## 📸 Ekran Görüntüleri

<p align="center">
  <img src="screenshots/home.png" width="220" />
  <img src="screenshots/detail.png" width="220" />
  <img src="screenshots/search.png" width="220" />
  <img src="screenshots/404.png" width="220" />
</p>

## Mimari

### Sistem Akış Şeması

```mermaid
flowchart TB
    subgraph Source["📡 Kaynak"]
        FP[Facepunch Wiki]
        RC[Recent Changes API]
    end
    
    subgraph Bot["🤖 Çeviri Botu"]
        Monitor[Değişiklik Monitörü]
        DiffParser[Diff Parser]
        Translator[AI Çeviri Motoru]
        Validator[Kalite Kontrolü]
        Encryptor[Şifreleme]
    end
    
    subgraph Storage["💾 Depolama"]
        Local[Lokal Dosyalar]
        Backup[Yedek Sistem]
        Cache[Diff Cache]
    end
    
    subgraph Web["🌐 Web Katmanı"]
        Server[Express Server]
        API[REST API]
        Static[Statik Dosyalar]
    end
    
    subgraph Client["👥 Kullanıcı"]
        Browser[Tarayıcı]
        Decrypt[Client Decryption]
        UI[SPA Interface]
    end
    
    FP -->|Saatlik Kontrol| Monitor
    Monitor -->|Yeni Güncelleme| RC
    RC -->|Diff URL| DiffParser
    DiffParser -->|Değişiklikler| Translator
    
    Translator -->|Gemini AI| Validator
    Translator -.->|DeepL Fallback| Validator
    
    Validator -->|Onaylandı| Encryptor
    Encryptor --> Local
    Local --> Backup
    DiffParser --> Cache
    
    Local --> Server
    Server --> API
    Server --> Static
    
    Browser --> Server
    Static --> Decrypt
    API --> UI
    Decrypt --> UI
    
    style Translator fill:#90EE90
    style Validator fill:#FFD700
    style Encryptor fill:#87CEEB
    style UI fill:#FFA07A
```

### Çeviri İş Akışı

```mermaid
flowchart TD
    Start([Güncelleme Algılandı]) --> CheckExist{Mevcut Çeviri<br/>Var mı?}
    
    CheckExist -->|Evet| CheckDiff{Diff URL<br/>Var mı?}
    CheckExist -->|Hayır| FullTranslation[Tam Sayfa Çevirisi]
    
    CheckDiff -->|Evet| ParseDiff[Diff Sayfasını<br/>Parse Et]
    CheckDiff -->|Hayır| FullTranslation
    
    ParseDiff --> AnalyzeChanges{Değişiklik Analizi}
    
    AnalyzeChanges -->|< %50 değişiklik| DiffMode[🚀 Diff Modu]
    AnalyzeChanges -->|> %50 değişiklik| FullTranslation
    AnalyzeChanges -->|> 10 değişiklik| FullTranslation
    
    DiffMode --> ExtractChanges[Sadece Değişiklikleri<br/>Çıkar]
    ExtractChanges --> TranslateDiff[Değişiklikleri Çevir<br/>%50-90 Tasarruf]
    TranslateDiff --> ApplyChanges[Mevcut Türkçe'ye<br/>Uygula]
    
    FullTranslation --> TranslateFull[Tüm Sayfayı Çevir]
    
    ApplyChanges --> Validate{Kalite Kontrolü}
    TranslateFull --> Validate
    
    Validate -->|✅ Başarılı| Encrypt[Şifrele & Kaydet]
    Validate -->|❌ Hatalı| Retry{Tekrar Dene?}
    
    Retry -->|Evet| FullTranslation
    Retry -->|Hayır| LogError[Hata Logla]
    
    Encrypt --> Backup[Yedekle]
    Backup --> UpdateProgress[İlerleme Güncelle]
    UpdateProgress --> End([✅ Tamamlandı])
    
    LogError --> End
    
    style DiffMode fill:#90EE90
    style TranslateDiff fill:#90EE90
    style Validate fill:#FFD700
    style Encrypt fill:#87CEEB
    style End fill:#98FB98
```

## 📊 Performans Metrikleri

| Metrik | Değer |
|--------|--------|
| **Toplam Sayfa** | 8000+ |
| **Ortalama Çeviri Süresi** | 15-30 saniye/sayfa |
| **Diff Modu Tasarrufu** | %50-90 token |
| **Günlük Güncelleme** | 10-50 sayfa |
| **API Başarı Oranı** | %99+ |
| **Cache Hit Oranı** | ~%75 |
| **Sayfa Yükleme** | < 500ms |

## 🎯 Teknik Zorluklar ve Çözümler

### 1. Büyük Ölçekli İçerik Yönetimi
**Zorluk**: 8000+ sayfa ve sürekli güncellenen içerik  
**Çözüm**: 
- Diff-tabanlı güncelleme sistemi
- Öncelikli işleme kuyruğu
- Progress tracking ile kesintisiz çalışma

### 2. Çeviri Kalitesi
**Zorluk**: Teknik terimlerin doğru çevrilmesi  
**Çözüm**:
- Özel prompt engineering
- Kod bloğu tanıma ve koruma
- Otomatik validasyon sistemi

### 3. Maliyet Optimizasyonu
**Zorluk**: API maliyetlerini düşük tutma  
**Çözüm**:
- Diff modu ile %90'a varan tasarruf
- Multi-API stratejisi
- Akıllı cache mekanizması

### 4. Güvenlik
**Zorluk**: İçerik güvenliği ve DDoS koruması  
**Çözüm**:
- Client-side decryption
- Rate limiting
- Statik dosya hosting

## 🤝 Topluluk

Bu proje, Türk oyuncu topluluğu için ve topluluk katkılarıyla geliştirilmektedir.

### İletişim
- 🌐 **Web**: [wiki.gmodtr.com](https://wiki.gmodtr.com/)
- 💬 **Geri Bildirim**: Site üzerinden iletişim formu

### Katkıda Bulunma Yolları
- 🐛 Hata bildirimi
- 💡 Özellik önerisi
- 📝 Çeviri düzeltmeleri
- ⭐ Projeyi yıldızlama

## 📈 İstatistikler

```
📚 Toplam Çevrilen İçerik: 8000+ sayfa
🔄 Günlük Güncelleme: 10-50 sayfa
👥 Aktif Kullanıcı: [Gizli]
🌍 Toplam Ziyaret: [Gizli]
⚡ Uptime: %99.9+
```

## 🏆 Başarılar

- ✅ Türkiye'nin ilk Facepunch wiki çeviri projesi
- ✅ Otomatik güncelleme ile sürekli güncellik
- ✅ Yüksek çeviri kalitesi (AI + Validation)
- ✅ Hızlı ve güvenli erişim

## 📄 Hukuki Uyarı

Bu proje, [wiki.facepunch.com](https://wiki.facepunch.com) sitesinin Türkçe çevirisidir. Tüm orijinal içerik hakları Facepunch Studios'a aittir. Bu proje ticari olmayan, eğitim amaçlı bir topluluk projesidir.

---

<div align="center">

**[🌐 Wiki'yi Ziyaret Et](https://wiki.gmodtr.com)**

Türk Oyuncu Topluluğu için ❤️ ile yapıldı

</div>