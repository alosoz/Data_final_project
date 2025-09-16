# Data_final_project

# Proje: Booking.com Esinli Azure Data Engineering Pipeline

## 1) Proje Amacı

Gerçek dünyaya yakın bir senaryo ile, otel rezervasyon ve yorum verilerini (batch) destinasyon/hava durumu gibi zenginleştirme verileri ve gerçek zamanlı olay akışı (stream) ile birleştirerek **Bronze–Silver–Gold (Delta Lake)** mimarisinde işlemeyi; **Azure Databricks** üzerinde dönüşümler yapmayı; **Azure Synapse**’te analitik/sorgu katmanını kurmayı; **Azure ML** ile basit bir tahmin modeli (örn. iptal olasılığı veya sonraki destinasyon) eğitmeyi; **Power BI** ile son kullanıcı panosu hazırlamayı hedefler.

Başarı ölçütü: Uçtan uca pipeline’ın çalışır olması, zamanlanabilir/izlenebilir olması, veri doğrulama testlerini geçmesi ve BI/ML çıktılarının üretilebilmesi.

---

## 2) Kapsam ve Veri Setleri

* **Batch (ana veri):** Hotel Booking Demand (Kaggle) + TripAdvisor Hotel Reviews (örnek yorumlar)
* **Zenginleştirme:** World Cities (şehir–ülke–koordinat), statik hava durumu özetleri veya OpenWeatherMap’ten periyodik snapshot, kur bilgileri (opsiyonel)
* **Gerçek Zamanlı (stream):** Rezervasyon olaylarının simülasyonu (check-in/out event, fiyat güncellemesi vb.)

> Not: Tüm batch dosyaları önce **ADLS Gen2 / landing** klasörüne yüklenir.

---

## 3) Hedef Mimari (Özet)

```
Data Sources (Kaggle CSV, APIs)        Real-time Simulator (Python)
        |                                         |
        v                                         v
   Azure Data Factory (Copy)                Azure Event Hubs
        |                                         |
        v                                         v
     ADLS Gen2 (Bronze - Raw)  <—— Azure Stream Analytics / Databricks Structured Streaming ——>
        |
        v
   Databricks (PySpark, Delta)
        |  (ETL/ELT)
        v
     ADLS Gen2 (Silver - Cleaned)
        |
        v
    Business Logic / Aggregations (Databricks)
        |
        v
     ADLS Gen2 (Gold - Curated)
        |                           
        +——> Synapse Serverless / Dedicated SQL Pools ——> Power BI
        |
        +——> Batch/Real-time Scores to Gold/Synapse
```

---

## 4) Kullanılacak Azure Servisleri ve Gerekçeleri

* **ADLS Gen2 (Data Lake):** Katmanlı veri depolama (Bronze/Silver/Gold), Delta Lake desteği
* **Azure Databricks:** PySpark ile dönüşüm, Delta Lake, MLflow entegrasyonu
* **Azure Data Factory (veya Synapse Pipelines):** Batch ingest, orkestrasyon, zamanlama
* **Azure Event Hubs:** Gerçek zamanlı olay akışı girişi
* **Azure Stream Analytics** (opsiyonel) **veya Databricks Structured Streaming:** Stream’i Bronze’a yazma ve basit zenginleştirme
* **Azure Synapse Analytics:** Gold veriler için SQL Query ve Semantic Layer
* **Power BI:** Raporlama ve dashboard
* **Azure Monitor / Log Analytics:** İzleme, alarmlar
* **Key Vault:** Gizli anahtarlar ve bağlantı stringleri

---

## 5) Ortam ve Önkoşullar

* Azure Subscription, Resource Group, VNet (opsiyonel)
* Workspace’ler: Databricks, Synapse, AML
* RBAC: Storage Blob Data Contributor/Reader rolleri, least-privilege prensibi
* Naming & path standardı (örnek):

  * `adls://datalake/booking_raw/` (bronze), `.../booking_clean/` (silver), `.../booking_gold/` (gold)
  * `schema.dataset/version=1.0/ingestion_date=YYYY-MM-DD/`

---

## 6) Repo Yapısı (Öneri)

```
repo-root/
  ├─ infrastructure/ (Bicep/Terraform IaC)
  ├─ data/ (örnek CSV, küçük sample’lar)
  ├─ notebooks/
  │   ├─ 01_bronze_ingest_batch.py
  │   ├─ 02_stream_to_bronze.py
  │   ├─ 03_silver_transformations.py
  │   ├─ 04_gold_business_marts.py
  ├─ pipelines/ (ADF JSON tanımları)
  ├─ powerbi/ (pbix veya dataset tanımları)
  ├─ tests/ (great_expectations, pytest)
  └─ docs/ (readme, runbooks)
```

---

## 7) Uçtan Uca Adımlar

### 7.1 Batch Ingestion (ADF)

1. ADF’de **Linked Service**: ADLS, GitHub (opsiyonel), Key Vault
2. **Datasets**: CSV/Parquet tanımı (Hotel Booking, Reviews, Cities, Weather snapshots)
3. **Copy Activities** ile `landing → bronze` taşıma, schema drift için **Binary** ingest + Auto-Detect veya Mapping
4. **Validation**: Dosya boyutu, satır sayısı, schema check (Great Expectations veya Databricks expectation’ları)

### 7.2 Stream Ingestion (Event Hubs → Bronze)

* Python simülatör, rezervasyon olaylarını JSON olarak Event Hubs’a gönderir (ör. `booking_id, user_id, hotel_id, event_type, ts`)
* **Seçenek A:** Stream Analytics job: EH → ADLS (Avro/JSON) + tumbling window ile küçük agregasyonlar
* **Seçenek B:** Databricks Structured Streaming: EH connector → Delta Bronze

### 7.3 Bronze → Silver (Databricks)

* Temizlik: Null’lar, tip dönüştürme, tarih/ülke normalizasyonu
* Join: reviews + hotels + cities + weather snapshot
* Dedupe, slowly changing attributes (SCD Type 2 gerekli ise)

### 7.4 Silver → Gold (Databricks)

* İş kuralları: KPI’lar (doluluk, avg\_daily\_rate, cancellation\_rate, lead\_time buckets)
* Boyut-küp yapıları: `dim_hotel`, `dim_city`, `dim_date`, `fact_booking`
* İnkremental yazım (MERGE INTO, Z-order gibi Delta optimizasyonları)

### 7.5 Synapse Katmanı

* Gold klasöründeki Delta/Parquet tablolarına **Serverless SQL** ile external table veya Dedicated SQL Pool’a yükleme
* View/Stored Procedure ile semantic layer

### 7.6 Power BI

* Kaynak: Synapse SQL endpoint veya Gold Parquet/Delta
* Sayfalar: Yönetim Özeti, Operasyon (iptal & doluluk), Fiyat & Gelir, Coğrafi dağılım, ML skor dağılımı

### 7.9 Orkestrasyon ve Zamanlama

* ADF Pipeline:

  * Activity sırası: **Copy (batch)** → **Databricks Notebook (bronze→silver)** → **Databricks (silver→gold)** → **Synapse SQL scripts** → **Power BI dataset refresh**
* Trigger: Günlük (batch), saatlik (stream append kontrol), manuel tetikleme

### 7.9 İzleme & Kalite

* **Great Expectations** veya **Databricks Expectations**: satır sayısı, benzersiz anahtarlar, tarih aralıkları
* ADF ve Databricks job’ları için uyarılar (Azure Monitor, Action Groups)
* Maliyet izleme: Cost Analysis, tag’ler

---

## 8) Örnek Şema ve Alanlar

**`fact_booking` (Gold)**

* booking\_id (PK), hotel\_id (FK), city\_id (FK), checkin\_date, checkout\_date
* adr (average daily rate), adults, children, babies
* is\_canceled, lead\_time, market\_segment, distribution\_channel
* review\_sentiment\_score (joined), weather\_avg\_temp, currency\_rate

**`dim_hotel`**: hotel\_id, hotel\_name, stars, chain\_flag, city\_id

**`dim_city`**: city\_id, city\_name, country, lat, lon, region

**`dim_date`**: date\_key, day, week, month, quarter, year, season

---

## 9) Kod Örnekleri (Özet)

### 9.1 Event Hubs – Python Simülatör (Üretici)

```python
import json, time, uuid, random, datetime
from azure.eventhub import EventHubProducerClient, EventData

conn_str = "<EVENT_HUB_CONN>"
hub_name = "booking-events"
producer = EventHubProducerClient.from_connection_string(conn_str, eventhub_name=hub_name)

hotels = [101,102,103,104]
users = list(range(1000,1100))
events = ["search", "view", "book", "cancel"]

while True:
    event = {
        "event_id": str(uuid.uuid4()),
        "booking_id": random.randint(1, 10_000),
        "user_id": random.choice(users),
        "hotel_id": random.choice(hotels),
        "event_type": random.choices(events, weights=[0.4,0.3,0.2,0.1])[0],
        "ts": datetime.datetime.utcnow().isoformat()
    }
    with producer:
        batch = producer.create_batch()
        batch.add(EventData(json.dumps(event)))
        producer.send_batch(batch)
    time.sleep(0.3)
```

### 9.2 Databricks – Structured Streaming (EH → Bronze Delta)

```python
from pyspark.sql.types import *
from pyspark.sql.functions import *

connectionString = "<EH_CONN>"
conf = {
  "eventhubs.connectionString": sc._jvm.org.apache.spark.eventhubs.EventHubsUtils.encrypt(connectionString)
}
raw = (
  spark.readStream.format("eventhubs")
  .options(**conf)
  .load()
)
json_df = raw.select(col("body").cast("string").alias("json")).select(from_json("json", "event_id string, booking_id long, user_id long, hotel_id long, event_type string, ts timestamp").alias("data")).select("data.*")

bronze_path = "abfss://datalake@<account>.dfs.core.windows.net/booking_raw/events/"
(
  json_df
  .withColumn("ingestion_date", to_date(current_timestamp()))
  .writeStream.format("delta")
  .option("checkpointLocation", bronze_path+"_chk")
  .option("path", bronze_path)
  .outputMode("append")
  .start()
)
```

### 9.3 Databricks – Bronze → Silver Dönüşümü (Batch)

```python
from pyspark.sql.functions import *

bronze_bookings = "/mnt/datalake/booking_raw/bookings/"
silver_bookings = "/mnt/datalake/booking_clean/bookings/"

(df := spark.read.format("delta").load(bronze_bookings)) \
  .withColumn("checkin_date", to_date("arrival_date")) \
  .withColumn("lead_time", col("lead_time").cast("int")) \
  .dropDuplicates(["booking_id"]) \
  .write.format("delta").mode("overwrite").save(silver_bookings)
```

### 9.4 Databricks – Silver → Gold (MERGE INTO)

```sql
CREATE TABLE IF NOT EXISTS gold.fact_booking USING DELTA LOCATION '/mnt/datalake/booking_gold/fact_booking';

MERGE INTO gold.fact_booking AS tgt
USING silver.vw_fact_booking AS src
ON tgt.booking_id = src.booking_id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;
```

### 9.5 Synapse – External Table (Serverless)

```sql
CREATE EXTERNAL DATA SOURCE ds_dlake WITH (
  LOCATION = 'https://<account>.dfs.core.windows.net/datalake'
);
CREATE EXTERNAL FILE FORMAT ff_parquet WITH (FORMAT_TYPE = PARQUET);
CREATE EXTERNAL TABLE dbo.fact_booking
WITH (
  LOCATION = '/booking_gold/fact_booking/',
  DATA_SOURCE = ds_dlake,
  FILE_FORMAT = ff_parquet
)
AS SELECT * FROM OPENROWSET(
  BULK 'https://<account>.dfs.core.windows.net/datalake/booking_gold/fact_booking/',
  DATA_SOURCE = ds_dlake,
  FORMAT='PARQUET'
) AS rows;
```

## 10) Test, Doğrulama ve Kabul Kriterleri

* **Veri Kalitesi:** Zorunlu alanların doluluğu, tarih aralığı kontrolleri, benzersiz `booking_id`
* **İzlenebilirlik:** ADF/Databricks job’larının başarılı koşuları, checkpoint dosyaları, SLA raporu
* **Performans:** Batch ingest < X dakika, günlük 1M satırda silver dönüşümleri < Y dakika
* **Güvenlik:** Erişimler RBAC ile kısıtlı, secret’lar Key Vault’ta

---

## 11) Değerlendirme Rubriği (Bootcamp)

* Mimari tasarım (20%) – Katmanlı yapı, servis entegrasyonu
* Veri ingest ve dönüşümler (25%) – ADF + Databricks kalitesi
* Veri modeli ve Gold tabakası (15%) – Boyut-fakt yapısı, performans
* Dashboard (15%) – KPI’lar, kullanılabilirlik
* İzleme ve kalite (10%) – Testler, alarmlar

---

## 12) Zaman Planı (Öneri – 5 Gün)

* **Gün 1:** Ortam kurulumu, veri kaynakları, batch ingest
* **Gün 2:** Bronze→Silver dönüşümleri, veri kalitesi
* **Gün 3:** Silver→Gold, Synapse external tables, SQL görünümleri
* **Gün 4:** Streaming hat, Event Hubs + Structured Streaming/ASA

(Alternatif: 2–3 günlük yoğunlaştırılmış sürüm; ML veya streaming kısıtlı tutulur.)

---

## 13) Gelişmiş/İsteğe Bağlı

* **dbt** ile transformlar, **Unity Catalog** ile yönetişim
* **CI/CD:** GitHub Actions + ADF/Databricks deployment
* **Feature Store** entegrasyonu, **Model Registry** (MLflow)
* **Kullanım maliyeti optimizasyonu:** Auto-stop clusters, spot node’lar, Delta optimize/z-order

---

## 14) Beklenen Çıktılar

* Çalışır **ADF pipeline** (JSON), Databricks notebook’ları, AML deneyi
* ADLS’te **Bronze/Silver/Gold** Delta tabloları
* **Synapse** sorgulanabilir katman (external/dedicated)
* **Power BI** raporu (.pbix veya workspace dataset)
* **Runbook/README**: Kurulum ve çalıştırma adımları

---

## 15) Son Notlar

* Veri kaynakları lisanslarına uyun; scraping yapacaksanız robots.txt ve kullanım koşullarını kontrol edin.
* Demo amaçlı veri hacmini küçük tutup, performans testleri için örnek büyütülmüş veri oluşturabilirsiniz.
* Bootcamp sonunda ekipler bulgularını ve mimari kararlarını 10 dakikalık bir sunumla paylaşsın.

---
