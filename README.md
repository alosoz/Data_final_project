

# Data_final_project  
## Booking.com Esinli Azure Data Engineering Pipeline  

---

## 📌 Proje Amacı  

Gerçek dünyaya yakın bir senaryo ile:  

- Otel rezervasyon ve yorum verileri (batch)  
- Destinasyon, hava durumu gibi zenginleştirme verileri  
- Gerçek zamanlı olay akışı (stream)  

birleştirilerek **Bronze–Silver–Gold (Delta Lake)** mimarisinde işlenecek.  

Proje; **Azure Databricks** üzerinde dönüşümler, **Azure Synapse**’te analitik katman, **Azure ML** ile basit bir tahmin modeli (ör. iptal olasılığı), **Power BI** ile son kullanıcı dashboard’u oluşturmayı hedefler.  

✅ **Başarı Ölçütleri:**  
- Uçtan uca pipeline’ın çalışması (batch + stream)  
- Planlanabilir ve izlenebilir olması  
- Veri doğrulama testlerinden geçmesi  
- BI ve ML çıktılarının üretilebilmesi  

---

## 🎯 Projeden Ne Bekleniyor?  

### Teknik Hedefler  
- Gerçekçi bir data pipeline (batch + real-time)  
- Katmanlı veri mimarisi (Bronze/Silver/Gold)  
- Azure servis entegrasyonu: ADF, Event Hubs, Databricks, Synapse, Power BI, ML  
- Veri zenginleştirme (şehir, hava durumu, kur bilgileri)  
- Gerçek zamanlı akış (Event Hubs)  
- Kullanıcıya değer: BI dashboard, sorgulanabilir veri tabakası  

### Başarı Kriterleri  
- Uçtan uca çalışırlık (otomatik, hatasız)  
- Veri doğruluğu ve kalitesi (testler geçmeli)  
- Power BI dashboard’unun oluşturulması  
- İzlenebilirlik ve güvenlik (loglama, RBAC, Key Vault)  
- Dokümantasyon ve otomasyon (README, runbook, notebook, JSON pipeline)  

---

## 📊 Çıktılar Nasıl Yorumlanacak?  

- **KPI’lar ve Trendler:** Doluluk oranı, ADR, iptal oranı, lead time  
- **Coğrafi Analiz:** Şehir/bölge farkları, mevsimsellik  
- **Rezervasyon & İptal Davranışları:** Segment, kanal, dönem bazında davranışlar  
- **Dashboard Yorumları:** Yönetim özeti, operasyonel metrikler, gelir dağılımı, ML skorları  
- **Pipeline Sağlığı:** Veri kalitesi sorunları, darboğazlar, loglar  
- **Maliyet & Optimizasyon:** Spot node, auto-stop, veri boyutu küçültme önerileri  
- **Mimari Gerekçeler:** Neden bu servisler ve veri modeli seçildi?  
- **Kısıtlar & Geliştirmeler:** Ölçeklenebilirlik, CI/CD, dbt  

---

## 🗂️ Kapsam ve Veri Setleri  

- **Batch (ana veri):** Hotel Booking Demand (Kaggle) + TripAdvisor Reviews  
- **Zenginleştirme:** World Cities, OpenWeatherMap snapshot, kur bilgileri  
- **Gerçek Zamanlı (stream):** Rezervasyon olay simülasyonu  

> Batch dosyaları önce **ADLS Gen2 / landing** klasörüne yüklenir.  

---

## 🏗️ Hedef Mimari  

---


Data Sources (CSV, APIs) Real-time Simulator (Python)
| |
v v
Azure Data Factory (Copy) Azure Event Hubs
| |
v v
ADLS Gen2 (Bronze - Raw) <—— Stream Analytics / Databricks ——>
|
v
Databricks (PySpark, Delta)
|
v
ADLS Gen2 (Silver - Cleaned)
|
v
Business Logic / Aggregations (Databricks)
|
v
ADLS Gen2 (Gold - Curated)
|
+——> Synapse SQL ——> Power BI
|
+——> ML Scores → Gold/Synapse

---

## ☁️ Kullanılacak Azure Servisleri  

- **ADLS Gen2:** Katmanlı veri depolama  
- **Databricks:** PySpark dönüşümleri, Delta Lake, MLflow  
- **ADF / Synapse Pipelines:** Batch ingest, orkestrasyon  
- **Event Hubs:** Gerçek zamanlı olay akışı  
- **Stream Analytics / Structured Streaming:** Stream ingestion  
- **Synapse Analytics:** SQL Query & Semantic Layer  
- **Power BI:** Dashboard & raporlama  
- **Monitor / Log Analytics:** İzleme, alarmlar  
- **Key Vault:** Gizli bilgilerin yönetimi  

---

## 📁 Repo Yapısı  


repo-root/
├─ infrastructure/ (IaC)
├─ data/ (örnek CSV)
├─ notebooks/
│ ├─ 01_bronze_ingest_batch.py
│ ├─ 02_stream_to_bronze.py
│ ├─ 03_silver_transformations.py
│ ├─ 04_gold_business_marts.py
├─ pipelines/ (ADF JSON tanımları)
├─ powerbi/ (pbix veya dataset tanımları)
├─ tests/ (great_expectations, pytest)
└─ docs/ (readme, runbooks)


---

## 🔄 Uçtan Uca Adımlar  

1. **Batch Ingestion (ADF):** Copy activity, validation, landing → bronze  
2. **Stream Ingestion (Event Hubs):** Python simülatör → EH → Bronze Delta  
3. **Bronze → Silver (Databricks):** Temizlik, join, dedupe, normalizasyon  
4. **Silver → Gold (Databricks):** İş kuralları, KPI, fact & dimension tabloları  
5. **Synapse Katmanı:** External tables, SQL views  
6. **Power BI:** Yönetim & operasyon dashboard  
7. **Orkestrasyon (ADF):** Günlük batch, saatlik stream append  
8. **İzleme & Kalite:** Expectations testleri, Azure Monitor  

---

## ✅ Test ve Kabul Kriterleri  

- Zorunlu alanlar, tarih aralığı, benzersiz booking_id  
- ADF/Databricks job izlenebilirliği  
- Performans: ingest < X dk, dönüşüm < Y dk  
- Güvenlik: RBAC + Key Vault  

---

## 📝 Değerlendirme Rubriği (Bootcamp)  

- Mimari tasarım (20%)  
- Veri ingest ve dönüşümler (25%)  
- Veri modeli & Gold tabakası (15%)  
- Dashboard (15%)  
- İzleme & kalite (10%)  

---

## ⏳ Zaman Planı (5 Gün)  

- **Gün 1:** Ortam, batch ingest  
- **Gün 2:** Bronze → Silver  
- **Gün 3:** Silver → Gold, Synapse  
- **Gün 4:** Streaming pipeline  
- **Gün 5:** Power BI, testler, final sunum  

---

## 📦 Beklenen Çıktılar  

- ADF pipeline (JSON)  
- Databricks notebook’ları  
- ADLS’te Bronze/Silver/Gold tabloları  
- Synapse tabloları (external/dedicated)  
- Power BI dashboard  
- Runbook & README  

---

## 🚀 Gelişmiş / Opsiyonel  

- **dbt entegrasyonu**  
- **Unity Catalog** ile yönetişim  
- **CI/CD (GitHub Actions)**  
- **Feature Store & MLflow registry**  
- **Maliyet optimizasyonu:** Auto-stop, spot, Delta optimize/z-order  

---

## 🎯 Sonuç  

Bu proje, batch + stream verilerin işlendiği, Azure ekosisteminde uçtan uca çalışan, testlerle güvence altına alınmış, BI ve ML çıktıları üreten gerçekçi bir **Booking.com esinli Data Engineering Pipeline**’dır.  
