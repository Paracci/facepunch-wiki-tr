# 🎮 Facepunch Wiki Turkish Translation Project

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Node.js](https://img.shields.io/badge/Node.js-14+-green.svg)](https://nodejs.org/)
[![Status](https://img.shields.io/badge/Status-Active-success.svg)]()

> A Turkish wiki resource for Facepunch games.

> This repository contains documentation only.  
> Source code of the website is not publicly available.

**🌐 Live site:** [wiki.gmodtr.com](https://wiki.gmodtr.com/)

**🇹🇷 Türkçe sürüm:** [README.tr.md](README.tr.md)

## 📋 Table of Contents

* [About the Project](#-about-the-project)
* [Features](#-features)
* [Technologies](#technologies)
* [Screenshots](#-screenshots)
* [Architecture](#architecture)
* [Performance](#-performance-metrics)
* [How to Contribute](#ways-to-contribute)

## 🎯 About the Project

This project is a system that automatically translates and keeps up-to-date the English wiki pages on [wiki.facepunch.com](https://wiki.facepunch.com) into Turkish. The goal is to provide native-language technical documentation to the Turkish gaming community.

### Supported Wikis

* ✅ **Garry's Mod** (GLua API — 6000+ pages)
* ✅ **Rust** (game mechanics and API)
* ✅ **Steamworks** (Steam integration APIs)
* ✅ **Facepunch General Wiki**

## ✨ Features

### 🤖 Intelligent Translation System

* **Diff-Based Updates**: Translates only the changed parts instead of the whole page

  * Saves 50–90% on token usage
  * Faster update cycles
  * Cost optimization

* **Hybrid AI Model**:

  * Primary: Google Gemini AI
  * Fallback: DeepL API
  * Automatic failover system

* **Technical Content Protection**:

  * Code blocks are preserved unchanged
  * Function names are not translated
  * API parameters remain in their original form
  * HTML structure is kept intact

### 🔄 Automatic Updating

* Hourly check system
* Detects changes on the source wiki
* Processes only updated pages
* Resumes from where it left off in case of errors

### 🔍 Advanced Search

* Full-text search
* Real-time results
* Highlighted result display
* Category-based filtering

### 💾 Data Security

* All content is stored encrypted
* Automatic backup system
* Version control
* Cloud backup support

## Technologies

### Backend

* **Python 3.8+** — Main bot logic and translation engine
* **Node.js/Express** — Web server and API

### AI & Translation

* **Google Gemini AI** — Primary translation engine
* **DeepL API** — Fallback translation service
* **Custom Validation** — Translation quality control system

### Frontend

* **Vanilla JavaScript** — Single Page Application (SPA)
* **Modern CSS** — Responsive design
* **Client-Side Encryption** — Secure content delivery

## 📸 Screenshots

<p align="center">
  <img src="screenshots/home.png" width="220" />
  <img src="screenshots/detail.png" width="220" />
  <img src="screenshots/search.png" width="220" />
  <img src="screenshots/404.png" width="220" />
</p>

## Architecture

### System Flowchart

```mermaid
flowchart TB
    subgraph Source["📡 Source"]
        FP[Facepunch Wiki]
        RC[Recent Changes API]
    end
    
    subgraph Bot["🤖 Translation Bot"]
        Monitor[Change Monitor]
        DiffParser[Diff Parser]
        Translator[AI Translation Engine]
        Validator[Quality Control]
        Encryptor[Encryption]
    end
    
    subgraph Storage["💾 Storage"]
        Local[Local Files]
        Backup[Backup System]
        Cache[Diff Cache]
    end
    
    subgraph Web["🌐 Web Layer"]
        Server[Express Server]
        API[REST API]
        Static[Static Files]
    end
    
    subgraph Client["👥 User"]
        Browser[Browser]
        Decrypt[Client Decryption]
        UI[SPA Interface]
    end
    
    FP -->|Hourly Check| Monitor
    Monitor -->|New Update| RC
    RC -->|Diff URL| DiffParser
    DiffParser -->|Changes| Translator
    
    Translator -->|Gemini AI| Validator
    Translator -.->|DeepL Fallback| Validator
    
    Validator -->|Approved| Encryptor
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

### Translation Workflow

```mermaid
flowchart TD
    Start([Update Detected]) --> CheckExist{Existing Translation<br/>Present?}
    
    CheckExist -->|Yes| CheckDiff{Diff URL<br/>Present?}
    CheckExist -->|No| FullTranslation[Full Page Translation]
    
    CheckDiff -->|Yes| ParseDiff[Parse Diff Page]
    CheckDiff -->|No| FullTranslation
    
    ParseDiff --> AnalyzeChanges{Change Analysis}
    
    AnalyzeChanges -->|< 50% changed| DiffMode[🚀 Diff Mode]
    AnalyzeChanges -->|> 50% changed| FullTranslation
    AnalyzeChanges -->|> 10 changes| FullTranslation
    
    DiffMode --> ExtractChanges[Extract Only<br/>the Changes]
    ExtractChanges --> TranslateDiff[Translate Changes<br/>50–90% Savings]
    TranslateDiff --> ApplyChanges[Apply to Existing Turkish]
    
    FullTranslation --> TranslateFull[Translate Entire Page]
    
    ApplyChanges --> Validate{Quality Control}
    TranslateFull --> Validate
    
    Validate -->|✅ Success| Encrypt[Encrypt & Save]
    Validate -->|❌ Failure| Retry{Retry?}
    
    Retry -->|Yes| FullTranslation
    Retry -->|No| LogError[Log Error]
    
    Encrypt --> Backup[Backup]
    Backup --> UpdateProgress[Update Progress]
    UpdateProgress --> End([✅ Completed])
    
    LogError --> End
    
    style DiffMode fill:#90EE90
    style TranslateDiff fill:#90EE90
    style Validate fill:#FFD700
    style Encrypt fill:#87CEEB
    style End fill:#98FB98
```

## 📊 Performance Metrics

| Metric                       | Value              |
| ---------------------------- | ------------------ |
| **Total Pages**              | 8000+              |
| **Average Translation Time** | 15–30 seconds/page |
| **Diff Mode Savings**        | 50–90% tokens      |
| **Daily Updates**            | 10–50 pages        |
| **API Success Rate**         | 99%+               |
| **Cache Hit Rate**           | ~75%               |
| **Page Load**                | < 500ms            |

## 🎯 Technical Challenges and Solutions

### 1. Large-Scale Content Management

**Challenge**: 8000+ pages and continuously updating content
**Solution**:

* Diff-based update system
* Priority processing queue
* Uninterrupted operation via progress tracking

### 2. Translation Quality

**Challenge**: Correct translation of technical terms
**Solution**:

* Custom prompt engineering
* Code block detection and protection
* Automated validation system

### 3. Cost Optimization

**Challenge**: Keeping API costs low
**Solution**:

* Diff mode saves up to 90%
* Multi-API strategy
* Smart caching mechanism

### 4. Security

**Challenge**: Content security and DDoS protection
**Solution**:

* Client-side decryption
* Rate limiting
* Static file hosting

## 🤝 Community

This project is developed for the Turkish gaming community and with community contributions.

### Contact

* 🌐 **Web**: [wiki.gmodtr.com](https://wiki.gmodtr.com/)
* 💬 **Feedback**: Via the site contact form

### Ways to Contribute

* 🐛 Report bugs
* 💡 Suggest features
* 📝 Improve translations
* ⭐ Star the project

## 📈 Statistics

```
📚 Total Translated Content: 8000+ pages
🔄 Daily Updates: 10–50 pages
👥 Active Users: [Hidden]
🌍 Total Visits: [Hidden]
⚡ Uptime: 99.9%+
```

## 🏆 Achievements

* ✅ Turkey's first Facepunch wiki translation project
* ✅ Continuous freshness through automatic updates
* ✅ High translation quality (AI + Validation)
* ✅ Fast and secure access

## 📄 Legal Notice

This project is a Turkish translation of [wiki.facepunch.com](https://wiki.facepunch.com). All original content rights belong to Facepunch Studios. This project is a non-commercial, community project for educational purposes.

---

<div align="center">

**[🌐 Visit the Wiki](https://wiki.gmodtr.com)**

Made with ❤️ for the Turkish gamer community

</div>
