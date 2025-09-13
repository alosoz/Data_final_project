🎬 Senaryo: “Booking.com Veri Macerası – Öğrenci Rehberi”

Bir turizm şirketi düşünün: HappyBooking.
Bu şirket, Avrupa’daki otellerden ve müşterilerden gelen rezervasyon verilerini topluyor. Ama sorun şu: veriler çok dağınık! Kimisi Excel dosyası, kimisi JSON API’si, bazıları da canlı (stream) veri olarak geliyor.

Şirketin amacı:
👉 Bu verileri düzenli bir şekilde toplayıp saklamak, temizlemek, ve en sonunda müşteri davranışlarını anlamak için güzel raporlar hazırlamak.

🎯 Öğrenci Görevleri

HappyBooking, sizi Data Engineer ekibine aldı. Görevleriniz:

Ham veriyi toplamak

Dosyaları, API verilerini veya simülasyonla üretilmiş stream verilerini sisteme almak.

Henüz hiç temizleme yapmayacağız.

Veriyi temizlemek ve düzenlemek

Boş satırları veya hatalı tarihleri düzeltmek.

Tekrarlanan kayıtları kaldırmak.

Eksik verileri mantıklı bir şekilde tamamlamak (örneğin, eksik şehir bilgisi → “Unknown”).

Analiz için veri hazırlamak

Hangi şehirde kaç rezervasyon var?

Hangi aylar yoğun?

Ortalama konaklama süresi nedir?

Rapor ve Dashboard hazırlamak

Power BI ile en popüler şehirler, aylara göre rezervasyon dağılımı, müşteri sayısındaki değişim gibi KPI’lar göstermek.

🔄 Adımlar (Öğrenci Rehberi)
1️⃣ Bronze Katmanı – Ham Veri

Görev: Veriyi olduğu gibi saklamak.

Örnek dosya: reservation_raw.csv

Yapılacaklar:

Dosyaları ADLS Gen2 (Azure) veya Lakehouse (Fabric) içine yükleyin.

Stream verisi simülatörü çalıştırabilirsiniz (Event Hubs veya Eventstream).

Amaç: Her şeyin kaynağını saklamak, ileride veri kalitesi kontrolleri için referans oluşturmak.

2️⃣ Silver Katmanı – Temizlenmiş Veri

Görev: Ham veriyi temiz ve kullanıma hazır hale getirmek.

Yapılacaklar:

Null değerleri temizle veya mantıklı default değer atayın.

Tarih formatlarını standart hâle getirin (YYYY-MM-DD).

Duplicate kayıtları kaldırın (booking_id veya user_id+hotel_id).

Otel ve şehir adlarını normalize edin.

Amaç: Analiz ve raporlamaya hazır veri oluşturmak.

3️⃣ Gold Katmanı – Business/Reporting Veri

Görev: İş kuralları ve özet tablolar oluşturmak.

Yapılacaklar:

Boyut tabloları oluşturun: dim_hotel, dim_city, dim_date.

Fakt tablosu oluşturun: fact_booking (rezervasyon sayısı, iptal durumu, gelir vb.).

KPI’lar:

Şehirlere göre rezervasyon sayısı

Aylara göre yoğunluk

İptal oranı

Amaç: Yönetim için hızlı rapor üretilecek tablo oluşturmak.

4️⃣ Power BI Dashboard

Görev: Gold katmanı kullanarak görselleştirme yapmak.

Önerilen sayfalar:

Popüler Şehirler: Şehir bazlı rezervasyon sayısı

Rezervasyon Trendleri: Ay bazında yoğunluk ve iptal oranları

Müşteri Analizi: Yeni vs. tekrar eden müşteriler

Amaç: Yönetim dashboard’u ve karar destek aracı hazırlamak.

5️⃣ Orkestrasyon ve İzleme

Azure Kullanıyorsanız:

ADF Pipelines: Batch ve stream veri akışını kontrol etmek.

Azure Monitor / Log Analytics: Pipeline ve notebook job’larını izlemek.

Fabric Kullanıyorsanız:

Eventstream & Lakehouse Notebooks: Ham veriden Gold’a kadar pipeline’ı kurmak.

Warehouse Monitoring: Veri akışını ve tabloları takip etmek.

🧩 Öğrencilerden Beklenen Çıktılar

Her grup/öğrenci, kendi veri hattını (pipeline) kuracak.

Bronze → Silver → Gold katmanlarını oluşturacak.

Power BI dashboard’u hazırlayacak ve yönetim sunumu yapacak.

Sonuç: HappyBooking Yönetim Dashboard’u 🎉
