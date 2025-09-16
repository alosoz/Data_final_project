🎬 Senaryo: “Booking.com Veri Macerası – Fabric Güncel”

Bir turizm şirketi düşünün: HappyBooking.
Şirket, otellerden ve müşterilerden gelen rezervasyon verilerini topluyor. Ama veriler çok dağınık: Excel, JSON API ve canlı (stream) veri geliyor.

Şirketin amacı:
👉 Bu verileri düzenli bir şekilde toplamak, temizlemek ve müşteri davranışlarını anlamak için dashboardlar hazırlamak.

🎯 Öğrenci Görevleri

HappyBooking sizi Data Engineer ekibine aldı. Görevleriniz:

1. Ham veriyi toplamak

  Dosyalar ve API verilerini Eventstream veya Lakehouse Bronze katmanına almak.

2. Veriyi temizlemek ve düzenlemek

  Null/duplicate/yanlış veri temizliği.

  Silver katmanı oluşturmak.

3. Business/Reporting veri hazırlamak

  Gold tabakası oluşturmak (KPI’lar, özet tablolar).

4. Streaming veri için direkt raporlama

  Eventstream ile gelen verileri Kusto Table (KTM)’a yaz.

  Bu tablolar Power BI ile anlık raporlama için kullanılır.

5. Dashboard hazırlamak

  Gold tabakası + Kusto Table verileri kullanarak Power BI raporları hazırlamak.

...

🔄 Adımlar (Fabric Öğrenci Rehberi)
1️⃣ Bronze Katmanı – Ham Veri

    Dosyaları veya stream veriyi Lakehouse Bronze’a yükle.

    Eventstream simülatörü çalıştır: rezervasyon event’leri JSON olarak gelir.

2️⃣ Silver Katmanı – Temizlenmiş Veri

    Null değerleri doldur, duplicate kayıtları kaldır, tarihleri normalize et.

    Silver tablosu artık analiz için hazırdır.

3️⃣ Gold Katmanı – Business Veri

    Fakt tablolar (fact_booking) ve boyut tablolar (dim_hotel, dim_city, dim_date) oluştur.
    
    KPI’lar ve özet tabloları hesapla (rezervasyon sayısı, yoğun aylar, iptal oranı).

4️⃣ Streaming → Kusto Table (KTM)

    Eventstream’den gelen veriyi anlık Kusto Table’a yaz.
    
    Bu sayede Power BI canlı dashboard ile gerçek zamanlı raporlama yapılabilir.

5️⃣ Dashboard

    Gold tablosu + Kusto Table verisi kullanarak Power BI raporu oluştur.
    
    Örnek sayfalar:
    
      Popüler şehirler
      
      Ay bazında rezervasyon dağılımı
      
      Anlık rezervasyon trendleri (streaming veri)

...

🧩 Beklenen Çıktılar

    Her grup/öğrenci kendi pipeline’ını kuracak:
    
        Bronze → Silver → Gold
    
    Eventstream → Kusto Table → Power BI (realtime)
    
        Sonuç: HappyBooking Yönetim Dashboard’u 🎉

