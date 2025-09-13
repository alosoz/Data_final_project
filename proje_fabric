📊 Rapor: Booking Pipeline – Microsoft Fabric
1. Veri Kaynağı → Ingestion

Araç: Eventstream (Fabric)

İş: Kaynak dosya, API veya streaming veri alımı

Çıktı: Bronze Layer (OneLake içinde ham veri)

2. Bronze → Silver (Temizleme & Transformasyon)

Araç: Fabric Lakehouse Notebook (PySpark veya Pandas)

İş: Schema düzenleme, null/duplicate temizliği, veri kalitesi kontrolü

Çıktı: Silver Layer (temizlenmiş tablo Lakehouse içinde)

3. Silver → Gold (Modelleme & Hazırlık)

Araç:

Fabric Lakehouse (Delta tablolar ile modelleme)

Fabric Data Warehouse (SQL-based modelleme)

İş: Join, aggregation, business rules

Çıktı: Gold Layer (raporlamaya hazır business veri modeli)

4. Analiz & Görselleştirme

Araç: Power BI (Fabric entegre)

İş: Dashboard ve raporların hazırlanması (Fabric içinden direkt erişim)

Çıktı: Self-service BI & raporlama

5. Orkestrasyon & İzleme

Araç:

Fabric Data Pipeline (ADF benzeri orchestrator)

Fabric Monitoring (pipeline + eventstream izleme)

✅ Özet Karşılaştırma

Azure’da ingestion için ADF kullanılırken Fabric’te Eventstream öne çıkıyor.

Azure’da transform için Databricks kullanılırken Fabric’te Lakehouse Notebook.

Azure’da SQL katmanı için Synapse SQL Pool gerekirken Fabric’te Data Warehouse gömülü.

Fabric’in artısı: tek platformda entegre, OneLake üzerinden tüm katmanlara erişim.






# Booking.com — Microsoft Fabric Data Engineering Pipeline

**Doküman:** Adım adım proje dosyası ve rehberi
**Hedef:** İki haftada tamamlanabilir, ML içermeyen, Bronze→Silver→Gold pipeline (Eventstream, Lakehouse, Notebooks, Dataflow Gen2, Warehouse, Power BI)

---

## İçindekiler

1. Özet ve başarı kriterleri
2. Önkoşullar (Fabric workspace, izinler)
3. Repo yapısı (dosya listesi ve açıklamaları)
4. Adım-adım uygulama (gün gün, her adımda oluşturulacak dosyalar ve içerikleri)
5. Notebook / script şablonları (kod blokları)
6. Data pipeline ve örnek aktiviteler
7. Warehouse & Power BI adımları
8. Testler, kalite kontrolleri ve runbook
9. Teslim ve demo kontrol listesi

---

## 1) Özet ve Kabul Kriterleri

* Bronze/Silver/Gold Delta tabloları Fabric Lakehouse üzerinde mevcut.
* Eventstream → Lakehouse Bronze → Notebook (Transforms) → Silver/Gold çalışır durumda.
* Warehouse ile en az 5 analytic sorgu çalıştırılabiliyor.
* Power BI raporu (PBIX) çalışıyor, 4 görsel içeriyor.
* Basit veri kalite testleri (unique booking\_id, mandatory null checks) geçiyor.

---

## 2) Önkoşullar

* Microsoft Fabric workspace
* OneLake (otomatik olarak sağlanır)
* Lakehouse (örn: `booking_lakehouse`)
* Eventstream (stream veya batch ingestion için)
* Dataflow Gen2 (temel transformlar için opsiyonel)
* Power BI Desktop

Erişim roller: Fabric Contributor.

---

## 3) Önerilen Repo Yapısı (oluşturacağın dosyalar)

```
repo-root/
  ├─ data/                            # küçük örnek CSV dosyaları (öğrenciler için)
  │    ├─ booking_sample.csv
  │    ├─ reviews_sample.csv
  │    └─ cities.csv
  ├─ notebooks/
  │    ├─ 01_bronze_ingest_notebook.ipynb
  │    ├─ 02_stream_simulator.py
  │    ├─ 03_bronze_to_silver.ipynb
  │    ├─ 04_silver_to_gold.ipynb
  │    └─ 05_quality_checks.ipynb
  ├─ pipelines/
  │    └─ fabric_data_pipeline.json
  ├─ scripts/
  │    └─ deploy_instructions.md
  ├─ powerbi/
  │    └─ Booking_Dashboard_Template.pbix
  ├─ tests/
  │    └─ data_quality_checks.py
  └─ docs/
       └─ runbook.md
```

---

## 4) Gün-Gün Uygulama (Adım Adım)

### Hazırlık (0.5 gün)

* Fabric workspace aç.
* Lakehouse oluştur.
* Repo'yu klonla; `data/` içine örnek CSV koy.

### Gün 1 — Veri & Keşif

* Task: `data/` içindeki CSV'leri incele, `data/dictionary.md` oluştur.
* Çıktı: `data/booking_sample.csv` vs. sütunlar.

### Gün 2 — Eventstream → Bronze

* Task: Eventstream oluştur, input (CSV veya simülasyon dosyası) → output (Lakehouse/bronze).
* Dosya: `pipelines/fabric_data_pipeline.json` (örnek export).

### Gün 3 — Bronze ingest notebook

* Task: `notebooks/01_bronze_ingest_notebook.ipynb` oluştur ve çalıştır.
* Bu notebook: bronze tabloları organize eder, ingestion\_ts ekler.

### Gün 4 — Bronze → Silver dönüşümleri

* Task: `notebooks/03_bronze_to_silver.ipynb` çalıştır.
* İşler: tip dönüşümü, tarih alanları, dedupe, zorunlu alan kontrolleri.

### Gün 5 — Dim / Fact table tasarımı ve Gold

* Task: `notebooks/04_silver_to_gold.ipynb`
* Oluştur: `dim_hotel`, `dim_city`, `fact_booking` (MERGE örneği)

### Gün 6 — Warehouse External Tables & SQL

* Task: Warehouse oluştur ve Lakehouse gold tablolarına bağla.
* Çalıştır: 5 temel sorgu (top city by revenue, cancellation rate, ADR trend)

### Gün 7 — Power BI Dashboard

* Task: `powerbi/Booking_Dashboard_Template.pbix` ile bağlantı ve rapor.

### Gün 8 — Orkestrasyon: Fabric Pipeline

* Task: Fabric pipeline'ı finalize et: Eventstream → Bronze → Notebook Silver → Notebook Gold

### Gün 9 — Testler, Log ve Runbook

* Task: `notebooks/05_quality_checks.ipynb` çalıştır; runbook.md yaz.

### Gün 10 — Final demo & teslim

* Task: Demo, Power BI canlı gösterim, repo sunumu.

---

## 5) Notebook / Script Şablonları

### `01_bronze_ingest_notebook.ipynb` (özet)

```python
# Learning objectives: Lakehouse bağlantısı, CSV read, write to Delta (bronze)
from pyspark.sql.functions import current_timestamp

landing_path = 'Tables/landing/bookings/'
bronze_path = 'Tables/bronze/bookings/'

df = spark.read.option('header', True).csv(landing_path)
df2 = df.withColumn('ingestion_ts', current_timestamp())

df2.write.format('delta').mode('append').save(bronze_path)
```

### `02_stream_simulator.py` (opsiyonel)

*(Aynı mantık, sadece Eventstream input klasörüne JSON dosyaları yazacak şekilde ayarlanır.)*

### `03_bronze_to_silver.ipynb` (özet)

```python
from pyspark.sql.functions import to_date, col

bronze = 'Tables/bronze/bookings/'
silver = 'Tables/silver/bookings/'

df = spark.read.format('delta').load(bronze)

clean = (df
  .withColumn('checkin_date', to_date(col('arrival_date'), 'yyyy-MM-dd'))
  .withColumn('checkout_date', to_date(col('departure_date'), 'yyyy-MM-dd'))
  .dropDuplicates(['booking_id'])
)

clean.write.format('delta').mode('overwrite').save(silver)
```

### `04_silver_to_gold.ipynb` (özet)

```python
silver_bookings = 'Tables/silver/bookings/'
gold_fact = 'Tables/gold/fact_booking/'

bookings = spark.read.format('delta').load(silver_bookings)

# dim city
dim_city = bookings.select('city','country').distinct()
dim_city.write.format('delta').mode('overwrite').save('Tables/gold/dim_city/')

# fact booking
bookings.select('booking_id','hotel_id','city','checkin_date','checkout_date','adr','is_canceled').write.format('delta').mode('overwrite').save(gold_fact)
```

### `05_quality_checks.ipynb` (özet)

```python
from pyspark.sql.functions import col

path = 'Tables/silver/bookings/'
df = spark.read.format('delta').load(path)

total = df.count()
distinct = df.select('booking_id').distinct().count()
assert total == distinct, f'Duplicates found: {total-distinct}'

null_count = df.filter(col('hotel_id').isNull() | col('checkin_date').isNull()).count()
assert null_count == 0, f'Nulls in mandatory fields: {null_count}'
```

---

## 6) Fabric Data Pipeline (örnek akış)

* Activity1: **Eventstream** — Input → Lakehouse Bronze
* Activity2: **Notebook** — `01_bronze_ingest_notebook.ipynb`
* Activity3: **Notebook** — `03_bronze_to_silver.ipynb`
* Activity4: **Notebook** — `04_silver_to_gold.ipynb`

---

## 7) Warehouse & Power BI

* Warehouse: Lakehouse Gold tablolarına bağlı external tables.
* Power BI: Warehouse endpoint’e bağlan, rapor sayfaları hazırlayın:

  * KPI (Total Bookings, Cancellation Rate, ADR)
  * Trend (bookings over time)
  * Map (city location)
  * Table (recent bookings)

---

## 8) Testler, Monitoring ve Runbook

* Test scriptleri `tests/data_quality_checks.py` içinde.
* Monitor: Fabric pipeline runs, Lakehouse tabloları kontrolü.
* Runbook.md içeriği:

  * Hatalı pipeline nasıl yeniden çalıştırılır
  * Common errors & fixes
  * Where to find logs

---

## 9) Teslim ve Demo Checklist

* [ ] Repo pushlandı (README, data örnekleri)
* [ ] Lakehouse içinde bronze/silver/gold yollar dolu
* [ ] Fabric pipeline başarılı run
* [ ] Warehouse external table sorgulanabiliyor
* [ ] Power BI raporu çalışıyor
* [ ] Runbook.md hazır

---

**Son:** Bu yeni versiyonda istersen ilk olarak Eventstream ile `bronze` ingestion kısmını mı, yoksa Notebook Silver transformunu mu detaylı açayım?
