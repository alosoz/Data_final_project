🛠️ Kullanılacak Fabric Servisleri ve Araçlar
📂 Veri Depolama & Yönetim

OneLake (Data Lake) → Tüm batch ve stream verilerinin saklandığı merkezi göl

Lakehouse (Bronze/Silver/Gold) → Katmanlı Delta tabanlı veri modeli

KQL Database → Streaming ve operational sorgular için

🔄 Veri Alma & İşleme

Eventstream → Gerçek zamanlı rezervasyon olaylarını ingest etmek

Dataflow Gen2 → Basit batch ingestion & transform işlemleri

Data Pipeline (Fabric Pipeline) → Batch ingest, orkestrasyon ve schedule işlemleri

Notebook (PySpark / SparkSQL) → Gelişmiş batch transform, Silver → Gold dönüşümleri

📊 Analitik & Sunum

Warehouse (Fabric Data Warehouse) → SQL tabanlı analitik katman

Power BI → Dashboard & raporlama (doluluk, iptal oranı, gelir vb.)

🛡️ İzleme & Yönetim

Fabric Monitor → Pipeline, notebook, eventstream izleme

OneLake Security + RBAC → Yetkilendirme

Data Quality Rules (Opsiyonel - Notebook + Power Query) → Veri doğrulama

🚀 Özet Akış (ML’siz Fabric versiyon)

Batch Data → Dataflow Gen2 / Pipeline → OneLake (Bronze) → Notebook → Silver → Notebook → Gold → Warehouse → Power BI

Streaming Data → Eventstream → KQL DB / Bronze → Notebook / Event Processing → Silver/Gold → Warehouse → Power BI
