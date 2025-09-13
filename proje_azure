📊 Rapor: Booking Pipeline – Azure Data Platform
1. Veri Kaynağı → Ingestion

Araç: Azure Data Factory (ADF) Pipeline

İş: Kaynak dosyalar (CSV/JSON) veya API’den veri alımı

Çıktı: Bronze Layer (Data Lake Storage Gen2 – ham veri)

2. Bronze → Silver (Temizleme & Transformasyon)

Araç: Azure Databricks (PySpark)

İş: Veri kalitesi kontrolleri, schema enforcement, null/duplicate temizliği

Çıktı: Silver Layer (temizlenmiş & analize hazır veri)

3. Silver → Gold (Modelleme & Hazırlık)

Araç: Databricks veya Azure Synapse SQL Pools

İş: Join, aggregation, business rules uygulanması

Çıktı: Gold Layer (business reporting tablosu)

4. Analiz & Görselleştirme

Araç: Power BI (Azure Synapse bağlantısı üzerinden)

İş: Dashboard ve raporların hazırlanması

Çıktı: Self-service BI & raporlama

5. Orkestrasyon & İzleme

Araç:

ADF (pipeline scheduling)

Azure Monitor / Log Analytics (alert & izleme)




Özet (hedef)

Amaç: Azure araçlarıyla uçtan uca data engineering + analytics pipeline kurmak (Bronze→Silver→Gold), Databricks ile dönüşümler, Synapse Serverless ile sorgu, Power BI ile dashboard.

Kısıtlama: İleri ML yok. Proje 2 haftada (10 iş günü) tamamlanabilir olmalı; karmaşık streaming yerine opsiyonel basit stream-simülasyonu (dosya tabanlı veya Event Hubs ile basit producer) bırakıyoruz.

Hedef kitle: Yeni öğrenen Data Engineer / Data Analyst öğrencilere uygun, hands-on.

Önerilen sadeleştirmeler / düzeltmeler (neden & nasıl)

ML kısmını çıkar

Neden: Ek süre, veri mühendisliği öğrenme hedefini dağıtır.

Ne yapılır yerine: Gold katmanda “feature” ve özet KPI’lar üret, Power BI’da görselleştir.

Streaming’i opsiyonel ve basitleştir

Orijinal karmaşıklık: Event Hubs + ASA veya Structured Streaming tam prod.

Öneri:

Minimum: Simüle edilmiş stream için bir Python script ile küçük JSON dosyaları landing/stream/ içine drop et ve ADF/Databricks ile düzenli (her 5 dk) pick-up yap.

İsteğe bağlı gelişmiş: Gerçek Event Hubs + Databricks Structured Streaming olarak göster, ama materyal/ödevde opsiyonel bırak.

ADF complexity azalt

Tüm copy aktivitelerini ADF’ye koy; fakat Databricks notebook tetiklemelerini Databricks Jobs veya ADF’den bir “RunNotebook” aktivitesi ile yap. Böylece ADF yalnızca ingest ve orkestrasyondan sorumlu olur.

Infrastructure as Code (IaC) opsiyonel tut

Eğer süre kısıtı varsa, IaC yerine adım adım portal veya minimal ARM/Bicep örneği ver. İleri düzey olanlar için repo’da infrastructure/ ile bir örnek bırak.

Great Expectations yerine hafif testler

Full GE kurulum zaman alır. Bunun yerine: Databricks üzerinde basit pytests veya PySpark assertions (örn. null check, unique booking_id) kullan. İstersen GE örneği “ek” olarak ver.

Datasets: küçük örneklerle başla

Tam 500k+ veri yerine küçük örnek CSV’ler (10k–50k) kullan. Demo için örnekleri repo/data içine koy veya Kaggle’dan küçük export al.

Dev ortamı

Databricks Community veya Azure Databricks (dev) kullan. Synapse Serverless kullanımı için maliyet düşük ve kurulumu basit.

Minimal (öğrenci dostu) Teknoloji Listesi

Zorunlu (öğrenme hedefine uygun):

ADLS Gen2 (landing / bronze / silver / gold)

Azure Databricks (PySpark, Delta)

Azure Data Factory (orchestration, copy)

Azure Key Vault (connection secrets)

Azure Synapse Serverless (external table / SQL)

Power BI (dashboard)

Opsiyonel (ileri/isteğe bağlı):

Azure Event Hubs (gerçek stream)

Databricks Structured Streaming (eğer Event Hubs kullanılır)

Great Expectations (veri kalite kapsamını artırmak için)

IaC (Bicep/Terraform) — tercih edilen ama opsiyonel

Yeni, sadeleştirilmiş Repo Yapısı (öğrenciler için)
repo-root/
  ├─ data/                # küçük sample CSV'ler (booking_sample.csv, reviews_sample.csv, cities.csv)
  ├─ notebooks/
  │   ├─ 01_ingest_adf_instructions.md
  │   ├─ 02_bronze_ingest_notebook.py
  │   ├─ 03_bronze_to_silver.py
  │   ├─ 04_silver_to_gold.py
  │   └─ 05_synapse_external_tables.sql
  ├─ pipelines/           # ADF JSON örnekleri (kopya + run notebook)
  ├─ scripts/
  │   ├─ stream_simulator.py   # opsiyonel - küçük JSON producer (dosya drop)
  ├─ tests/
  │   └─ data_quality_checks.py
  └─ docs/
      └─ runbook.md

Gün Gün (2 hafta / 10 iş günü) Plan — Öğrenci Dostu (adım adım)

Hazırlık (öncesi): Azure aboneliği, resource group, Databricks workspace, ADLS gen2, KeyVault. (bu hazırlık 0.5 gün olarak görülür)

1. Gün — Proje başlangıç & veri keşfi (4–6 saat)

Hedef: Veri kaynaklarını indir, örnek CSV’leri repo/data/ koy.

Çıktı: README + veri sözlüğü (hangi sütun, tip).

2. Gün — ADLS & erişim + ADF copy (4–6 saat)

ADLS container oluştur: landing/ ve bronze/.

ADF ile local CSV → ADLS landing/ kopya pipeline oluştur.

Çıktı: successful copy run.

3. Gün — Databricks ortam & basit bronze ingest notebook (4–6 saat)

Mount veya direkt credentials ile ADLS’ye eriş.

Notebook 02_bronze_ingest_notebook.py: landing → bronze (Delta) dönüşümü (binary ingest / autoinfer).

Çıktı: bronze delta path içinde veriler.

4. Gün — Bronze temizlik & silver dönüşümü (4–6 saat)

03_bronze_to_silver.py: tip dönüşümleri, zorunlu alan kontrolü, dedupe.

Basit veri kalite kontrolleri (null%<threshold, unique booking_id).

Çıktı: silver delta tablolar.

5. Gün — Dim/Facts & gold hazırlığı (4–6 saat)

04_silver_to_gold.py : dim_hotel, dim_city, fact_booking oluştur.

MERGE INTO kullanarak incremental yazım göster (örnek küçük).

Çıktı: gold klasörü ve örnek tablolar.

6. Gün — Synapse Serverless external tables + SQL views (4 saat)

05_synapse_external_tables.sql dosyası ile external table oluştur.

Basit analytic queries hazırla (top city by revenue, cancellation rate).

Çıktı: Synapse üzerinden çalıştırılabilir SQL.

7. Gün — Power BI dashboard & dataset (4–6 saat)

Power BI Desktop ile Synapse veri kaynağına bağlan, 4-5 görsel (KPI, harita, iptal oranı, ADR trend).

Çıktı: .pbix dosyası + kısa sunum.

8. Gün — Orkestrasyon (ADF) & scheduling (4 saat)

ADF pipeline: Copy(landing) → Run Databricks(02) → Run Databricks(03/04) → Trigger synapse refresh (opsiyonel).

Test: pipeline çalıştır, loglar, alert baseline.

9. Gün — Testler, kalite kontrolleri, runbook (4 saat)

Basit test scriptleri (data_quality_checks.py) çalıştır, failure senaryoları test et.

Runbook.md: nasıl debug yapılır, checkpoint/where to look.

10. Gün — Sunum & teslimat + buffer (4–6 saat)

Final demo: 10–15 dk sunum, Power BI canlı, kısa repo tour.

Buffer: hata düzeltmeleri, eksik dokümanlar.

Not: Eğer Event Hubs/Stream istersen, bunu 1 ekstra gün (opsiyonel) ekle; yoksa stream_simulator ile dosya-drop simüle ederiz.

Kabul Kriterleri (Basitleştirilmiş ve ölçülebilir)

Bronze, Silver, Gold klasörlerinde Delta tablolar var ve booking_id unique (KPI: unique% >= 99.9).

ADF pipeline başarılı şekilde çalışıyor ve Databricks notebook’larını tetikliyor.

Synapse Serverless ile en az 5 analytic sorgu çalıştırılabiliyor (örnek sorgular hazır).

Power BI raporu çalışıyor ve en az 4 görsel içeriyor (KPI, trend, map, table).

Basit veri kalite testleri (null check, date range, unique id) geçiyor.

Tüm adımlar için runbook/README bulunuyor.

Örnek veri kalite kontrolleri (Databricks/PySpark)
# Basit kontroller örneği
df = spark.read.format("delta").load("/mnt/datalake/booking_clean/bookings/")
# 1. booking_id unique
total = df.count()
distinct = df.select("booking_id").distinct().count()
assert distinct == total, f"Duplicate booking_id found: {total-distinct} duplicates"

# 2. zorunlu alanlar dolu
null_critical = df.filter(df["hotel_id"].isNull() | df["checkin_date"].isNull()).count()
assert null_critical == 0, f"Critical nulls: {null_critical}"

# 3. tarih aralığı kontrolü
from pyspark.sql.functions import year
years = df.select(year("checkin_date")).distinct().collect()
print("Years present:", years)

Basit örnek PySpark snippet — Bronze → Silver (doğru tip dönüşümü + dedupe)
from pyspark.sql.functions import to_date, col

bronze = "/mnt/datalake/booking_raw/bookings_delta/"
silver = "/mnt/datalake/booking_clean/bookings_delta/"

df = spark.read.format("delta").load(bronze)
df2 = (df
      .withColumn("checkin_date", to_date(col("arrival_date"), "yyyy-MM-dd"))
      .withColumn("checkout_date", to_date(col("departure_date"), "yyyy-MM-dd"))
      .withColumn("lead_time", col("lead_time").cast("int"))
      .dropDuplicates(["booking_id"])
)

df2.write.format("delta").mode("overwrite").save(silver)

Değerlendirme Rubriği (Bootcamp uyarlanmış)

Pipeline & Orkestrasyon: 30%

Veri Temizliği & Model (dim/fact): 25%

Analitik sorgular & Power BI dashboard: 25%

Test & Dokümantasyon (runbook): 20%

Ek Notlar / İpuçları (pratik)

Cluster: Databricks’de küçük sınıf cluster (2–4 worker) yeterli; auto-terminate ayarla.

Secrets: Key Vault kullan; notebook’ta doğrudan connection string yazma.

Delta optimize: OPTIMIZE ve VACUUM göstermeye gerek yok, opsiyonel.

Veri boyutu: Demo 10k–50k satır yeterli; büyük veri performansını istersen synthetic step ekle.

Öğretici materyal: Her notebook başına “learning objectives” yaz — öğrencilere ne öğrenecekleri net olsun.

Eğer istersen bir sonraki adımda hemen şu dosyaları ben hazırlayayım (hemen bu konuşmada):

02_bronze_ingest_notebook.py — hazır PySpark notebook (ADLS path parametreli)

03_bronze_to_silver.py — temizleme notebook (özellikle tip dönüşümü, dedupe, basit normalizasyon)

data/ içine küçük örnek CSV (örnek 5k satır) — fakat veri yüklemek için benden veri indirmemi istersen bunu sağlayamam; sana dosya şablonu hazırlayıp nasıl dolduracağın gösteririm.

Hangisini istersin hemen hazırlayayım? (not: eğer “hemen hazırla” dersen, doğrudan notebook kodlarını buraya eklerim).

Şunu dedin:
bana gore bana proje dosyasini adim adim bastan sona yunlendirecek sekilde hazirla
ChatGPT:
Booking Azure Data Pipeline Step By Step
Booking.com — Azure Data Engineering Pipeline

Doküman: Adım adım proje dosyası ve rehberi Hedef: İki haftada tamamlanabilir, ML içermeyen, Bronze→Silver→Gold pipeline (Databricks, ADLS, ADF, Synapse, Power BI)

İçindekiler

Özet ve başarı kriterleri

Önkoşullar (Azure kaynakları, izinler)

Repo yapısı (dosya listesi ve açıklamaları)

Adım-adım uygulama (gün gün, her adımda oluşturulacak dosyalar ve içerikleri)

Notebook / script şablonları (kod blokları)

ADF pipeline ve örnek aktiviteler

Synapse & Power BI adımları

Testler, kalite kontrolleri ve runbook

Teslim ve demo kontrol listesi

1) Özet ve Kabul Kriterleri

Bronze/Silver/Gold Delta tabloları ADLS üzerinde mevcut.

ADF pipeline: Landing → Bronze → Databricks(Transforms) → Gold çalışır durumda.

Synapse Serverless ile en az 5 analytic sorgu çalıştırılabiliyor.

Power BI raporu (PBIX) çalışıyor, 4 görsel içeriyor.

Basit veri kalite testleri (unique booking_id, mandatory null checks) geçiyor.

2) Önkoşullar

Azure subscription, Resource Group

ADLS Gen2 hesabı (container: datalake)

Databricks workspace (dev cluster oluşturulacak)

Azure Data Factory workspace

Azure Key Vault (secretler için)

Power BI Desktop

Erişim roller: Storage Blob Data Contributor (veya uygun mount/credential erişimi).

3) Önerilen Repo Yapısı (oluşturacağın dosyalar)
repo-root/
  ├─ data/                            # küçük örnek CSV dosyaları (öğrenciler için)
  │    ├─ booking_sample.csv
  │    ├─ reviews_sample.csv
  │    └─ cities.csv
  ├─ notebooks/
  │    ├─ 01_bronze_ingest_notebook.py
  │    ├─ 02_stream_simulator.py
  │    ├─ 03_bronze_to_silver.py
  │    ├─ 04_silver_to_gold.py
  │    └─ 05_quality_checks.py
  ├─ pipelines/
  │    └─ adf_copy_and_run_notebook.json
  ├─ scripts/
  │    └─ deploy_instructions.md
  ├─ powerbi/
  │    └─ Booking_Dashboard_Template.pbix
  ├─ tests/
  │    └─ data_quality_checks.py
  └─ docs/
       └─ runbook.md

Her notebook başına kısa bir açıklama satırı (learning objectives) ekle.

4) Gün-Gün Uygulama (Adım Adım)
Hazırlık (0.5 gün)

Azure kaynakları oluştur: Resource Group, Storage (ADLS Gen2), Databricks workspace, ADF, Key Vault.

Kısa not: Key Vault içine storage account key veya service principal secret kaydet.

Repo'yu klonla; data/ içine örnek CSV koy.

Gün 1 — Veri & Keşif

Task: data/ içindeki CSV'leri incele, data/dictionary.md oluştur.

Çıktı: data/booking_sample.csv vs. sütunlar.

Gün 2 — ADLS Landing + ADF Copy

Task: ADF Linked Service (ADLS), Dataset (CSV), Copy activity ile local→landing/booking/ yükle.

Dosya: pipelines/adf_copy_and_run_notebook.json (örnek pipeline export).

Gün 3 — Databricks bağlantısı & Bronze ingest notebook

Task: notebooks/01_bronze_ingest_notebook.py oluştur ve çalıştır.

Bu notebook: landing/ içindeki CSV'leri okur, basit schema apply eder, Delta formatında bronze/bookings/ yaz.

Gün 4 — Bronze -> Silver dönüşümleri

Task: notebooks/03_bronze_to_silver.py çalıştır.

İşler: tip dönüşümü, checkin_date/checkout_date to_date, dedupe, zorunlu alan kontrolleri.

Gün 5 — Dim / Fact table tasarımı ve Gold

Task: notebooks/04_silver_to_gold.py

Oluştur: dim_hotel, dim_city, fact_booking (MERGE örneği)

Gün 6 — Synapse External Tables & SQL

Task: docs/synapse_instructions.md ekle: Serverless external data source, external table tanımları

Çalıştır: 5 temel sorgu (top city by revenue, cancellation rate, ADR trend)

Gün 7 — Power BI Dashboard

Task: powerbi/Booking_Dashboard_Template.pbix ile bağlantı ve rapor.

Gün 8 — Orkestrasyon: ADF Pipeline

Task: ADF pipeline'ı finalize et: Copy → Run Databricks(01) → Run Databricks(03) → Run Databricks(04)

Gün 9 — Testler, Log ve Runbook

Task: notebooks/05_quality_checks.py çalıştır; runbook.md yaz.

Gün 10 — Final demo & teslim

Task: Demo, Power BI canlı gösterim, repo sunumu.

5) Notebook / Script Şablonları

NOT: Aşağıdaki kod blokları notebook içine konulacak (parametre ile ADLS path alarak). Her notebook başına Learning objectives satırı ekle.

01_bronze_ingest_notebook.py (özet)
# Learning objectives: ADLS bağlantısı, CSV read, write to Delta (bronze)
from pyspark.sql.functions import current_timestamp


landing_path = '/mnt/datalake/landing/bookings/'
bronze_path = '/mnt/datalake/booking_raw/bookings_delta/'


df = spark.read.option('header', True).csv(landing_path)
# Basit transform: ingestion_ts
df2 = df.withColumn('ingestion_ts', current_timestamp())


(df2.write.format('delta').mode('append').save(bronze_path))
02_stream_simulator.py (opsiyonel)
# Basit dosya-drop simulator: booking_sample.csv'den rastgele satırlar alıp json olarak landing/stream/ dizinine koyar
import csv, json, random, uuid, datetime, time


src = 'data/booking_sample.csv'
dst_folder = '/path-to-mounted/landing/stream/'


with open(src) as f:
    reader = list(csv.DictReader(f))


while True:
    rec = random.choice(reader)
    rec['event_id'] = str(uuid.uuid4())
    rec['ts'] = datetime.datetime.utcnow().isoformat()
    fname = dst_folder + f'event_{uuid.uuid4().hex}.json'
    with open(fname,'w') as out:
        out.write(json.dumps(rec))
    time.sleep(2)
03_bronze_to_silver.py (özet)
# Learning objectives: data cleaning, type cast, dedupe
from pyspark.sql.functions import to_date, col


bronze = '/mnt/datalake/booking_raw/bookings_delta/'
silver = '/mnt/datalake/booking_clean/bookings_delta/'


df = spark.read.format('delta').load(bronze)


clean = (df
  .withColumn('checkin_date', to_date(col('arrival_date'), 'yyyy-MM-dd'))
  .withColumn('checkout_date', to_date(col('departure_date'), 'yyyy-MM-dd'))
  .withColumn('lead_time', col('lead_time').cast('int'))
  .dropDuplicates(['booking_id'])
)


clean.write.format('delta').mode('overwrite').save(silver)
04_silver_to_gold.py (özet)
# Learning objectives: dim/fact creation, MERGE incremental
silver_bookings = '/mnt/datalake/booking_clean/bookings_delta/'
gold_fact = '/mnt/datalake/booking_gold/fact_booking/'


# örnek: dim_city oluştur
bookings = spark.read.format('delta').load(silver_bookings)


# dim city (basit)
dim_city = bookings.select('city','country','city_lat','city_lon').distinct()
dim_city.write.format('delta').mode('overwrite').save('/mnt/datalake/booking_gold/dim_city/')


# fact booking (örnek)
bookings.select('booking_id','hotel_id','city','checkin_date','checkout_date','adr','is_canceled').write.format('delta').mode('overwrite').save(gold_fact)
05_quality_checks.py (özet)
# Simple assertions
from pyspark.sql.functions import col


path = '/mnt/datalake/booking_clean/bookings_delta/'
df = spark.read.format('delta').load(path)


# 1) booking_id unique
total = df.count()
distinct = df.select('booking_id').distinct().count()
assert total == distinct, f'Duplicates found: {total-distinct}'


# 2) mandatory fields not null
null_count = df.filter(col('hotel_id').isNull() | col('checkin_date').isNull()).count()
assert null_count == 0, f'Nulls in mandatory fields: {null_count}'
6) ADF Pipeline (örnek akış)

Activity1: Copy — Local/Blob → ADLS landing/

Activity2: Databricks Notebook — 01_bronze_ingest_notebook.py

Activity3: Databricks Notebook — 03_bronze_to_silver.py

Activity4: Databricks Notebook — 04_silver_to_gold.py

ADF'de her notebook aktivitesi için Databricks linked service ve notebook path parametrelerini kullan.

pipelines/adf_copy_and_run_notebook.json dosyasını ADF export formatında koy; örnek adımlar içersin.

7) Synapse & Power BI

Synapse: Serverless external data source tanımla (ADLS URL). External table ile gold parquet/delta erişimi.

Power BI: Synapse endpoint'e bağlan, rapor sayfaları hazırlayın:

KPI (Total Bookings, Cancellation Rate, ADR)

Trend (bookings over time)

Map (city location)

Table (recent bookings)

8) Testler, Monitoring ve Runbook

Test scriptleri tests/data_quality_checks.py içinde.

Monitor: ADF runs, Databricks job runs, storage checkpoint klasörleri kontrolü.

Runbook.md içeriği:

Hatalı pipeline nasıl yeniden çalıştırılır

Common errors & fixes

Where to find logs

9) Teslim ve Demo Checklist




Ek kaynaklar & öneriler

Databricks docs: Delta Lake, MERGE INTO examples

ADF docs: copy activity, databricks notebook activity

Son: Bu dokümanı kullanarak hangi dosyayı ilk oluşturayım? (ör. 01_bronze_ingest_notebook.py tam kodunu canvas içinde yazayım veya pipelines/adf_copy_and_run_notebook.json örneğini oluşturayım.)

Dokümanı oluşturdum: Booking Azure Data Pipeline Step By Step. Açıp adımları, dosya şablonlarını ve günlük planı oradan takip edebilirsin. Hangi dosyayı hemen içine tam olarak yazmamı istersin? (ör. 01_bronze_ingest_notebook.py veya ADF pipeline JSON örneği)
