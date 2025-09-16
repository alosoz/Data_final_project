# Data_final_project – HappyBooking  

## 📌 Proje Amacı  

Gerçek dünyaya yakın bir senaryoda:  

- Otel rezervasyon ve yorum verileri (batch)  
- Destinasyon, hava durumu gibi zenginleştirme verileri  
- Gerçek zamanlı olay akışı (streaming)  

birleştirilerek **Bronze–Silver–Gold mimarisi** üzerinden işlenecek.  

Proje; **Microsoft Fabric** üzerinde dosya ve streaming ingest işlemleri, **Lakehouse** tabanlı dönüşümler, **Kusto Table (KTM)** ile anlık raporlama, **Power BI** ile son kullanıcı dashboard’u oluşturmayı hedefler.  

✅ **Başarı Ölçütleri:**  
- Uçtan uca pipeline’ın çalışması (batch + stream)  
- Planlanabilir ve izlenebilir olması  
- Veri doğrulama testlerinden geçmesi  
- BI çıktılarının üretilebilmesi  

---

## 🎯 Projeden Ne Bekleniyor?  

### Teknik Hedefler  
- Gerçekçi bir data pipeline (batch + real-time)  
- Katmanlı veri mimarisi (Bronze/Silver/Gold)  
- Fabric servis entegrasyonu: Eventstream, Lakehouse, Notebooks, Kusto Table, Power BI  
- Veri temizleme (null, duplicate, tarih normalizasyonu)  
- Business veri modeli (fact & dimension tabloları)  
- Streaming verilerin Kusto Table üzerinden direkt raporlanması  
- Kullanıcıya değer: BI dashboard + realtime raporlama  

### Başarı Kriterleri  
- Uçtan uca çalışırlık (otomatik, hatasız)  
- Veri doğruluğu ve kalitesi (testler geçmeli)  
- Power BI dashboard’unun oluşturulması  
- İzlenebilirlik ve güvenlik (loglama, workspace RBAC)  
- Dokümantasyon ve otomasyon (README, runbook, notebook)  

---

## 📊 Çıktılar Nasıl Yorumlanacak?  

- **KPI’lar ve Trendler:** Rezervasyon sayısı, iptal oranı, doluluk oranı  
- **Coğrafi Analiz:** Şehir/bölge bazında farklar, popüler destinasyonlar  
- **Rezervasyon & İptal Davranışları:** Kanal, müşteri segmenti, dönem bazında  
- **Dashboard Yorumları:** Yönetim özeti, operasyonel metrikler, canlı trendler  
- **Pipeline Sağlığı:** Veri kalitesi sorunları, darboğazlar, loglar  
- **Maliyet & Optimizasyon:** Veri boyutu küçültme, auto-stop, verimli transformasyon  

---

## 🗂️ Kapsam ve Veri Setleri  

- **Batch (ana veri):** Hotel Booking Demand (Kaggle) + TripAdvisor Reviews  
- **Zenginleştirme:** World Cities, hava durumu snapshot, kur bilgileri  
- **Gerçek Zamanlı (stream):** Rezervasyon olay simülasyonu (Eventstream JSON)  

---

## 🏗️ Hedef Mimari  

Data Sources (Excel, CSV, API) Eventstream (Streaming)
| |
v v
Lakehouse Bronze (Raw) <-----------------------+
|
v
Lakehouse Silver (Cleaned)
|
v
Lakehouse Gold (Business KPIs) -----> Power BI (historical reports)
|
+--> Kusto Table (Realtime) --> Power BI (live dashboard)


---

## ☁️ Kullanılacak Fabric Servisleri  

- **Lakehouse:** Katmanlı veri depolama (Bronze / Silver / Gold)  
- **Eventstream:** Gerçek zamanlı olay akışı ingestion  
- **Notebooks / Dataflows:** Veri temizleme, dönüşüm  
- **Kusto Table (KTM):** Streaming veriler için anlık raporlama  
- **Power BI (Fabric):** Dashboard & raporlama  
- **Monitor:** İzleme ve loglama  

---

## 📁 Repo Yapısı  

repo-root/
├─ data/ (örnek CSV/Excel dosyaları)
├─ notebooks/
│ ├─ 01_bronze_ingest.ipynb
│ ├─ 02_silver_cleaning.ipynb
│ ├─ 03_gold_business.ipynb
├─ eventstream/ (simülatör, json örnekleri)
├─ kusto/ (KQL scriptleri)
├─ powerbi/ (pbix veya dataset tanımları)
└─ docs/ (readme, runbooks)



---

## 🔄 Uçtan Uca Adımlar  

1. **Batch Ingestion (Bronze):** Dosyaları Lakehouse Bronze’a yükle.  
2. **Stream Ingestion (Eventstream):** Simülatör → Eventstream → Bronze.  
3. **Bronze → Silver (Notebook/Dataflow):** Temizlik, dedupe, normalizasyon.  
4. **Silver → Gold (Notebook):** Fact & dimension tabloları, KPI hesaplamaları.  
5. **Streaming → Kusto Table:** Eventstream → KTM → Power BI Live.  
6. **Dashboard (Power BI):** Gold + Kusto Table ile yönetim & operasyon raporu.  
7. **İzleme & Kalite:** Veri doğrulama testleri, Fabric Monitor.  

---

## ✅ Test ve Kabul Kriterleri  

- Zorunlu alanlar dolu mu?  
- Duplicate kayıtlar temizlendi mi?  
- Tarihler normalize edildi mi?  
- Streaming veri Power BI’da canlı görünüyor mu?  
- Dashboard KPI’ları doğru hesaplanıyor mu?  

---

## 📝 Değerlendirme Rubriği (Bootcamp)  

- Mimari tasarım (20%)  
- Veri ingest ve dönüşümler (25%)  
- Veri modeli & Gold tabakası (15%)  
- Dashboard (15%)  
- İzleme & kalite (10%)  

---

## ⏳ Zaman Planı (5 Gün)  

- **Gün 1:** Ortam kurulumu, Bronze ingest  
- **Gün 2:** Silver dönüşümleri  
- **Gün 3:** Gold tablosu ve KPI’lar  
- **Gün 4:** Streaming pipeline (Eventstream → KTM)  
- **Gün 5:** Power BI dashboard, testler, final sunum  

---

## 📦 Beklenen Çıktılar  

- Lakehouse Bronze/Silver/Gold tabloları  
- Eventstream & Kusto Table yapılandırması  
- Notebooks / Dataflows dönüşümleri  
- Power BI dashboard (historical + live)  
- Runbook & README  

---

## 🚀 Gelişmiş / Opsiyonel  

- **dbt entegrasyonu**  
- **Fabric Data Activator** ile event-driven tetiklemeler  
- **CI/CD (GitHub Actions)**  
- **Maliyet optimizasyonu:** Auto-stop, boyut küçültme, performans tuning  

---

## 🎯 Sonuç  

Bu proje, **Microsoft Fabric üzerinde** batch + streaming verilerin işlendiği, Bronze–Silver–Gold yaklaşımıyla yönetilen, testlerle güvence altına alınmış ve Power BI ile gerçek zamanlı raporlama yapan gerçekçi bir **Booking.com esinli Data Engineering Pipeline**’dır.  




