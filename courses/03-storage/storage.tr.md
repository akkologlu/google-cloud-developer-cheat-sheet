# Modül 3 – Google Cloud Depolama Seçenekleri

> Uygulamanızın veri ve iş yükü gereksinimlerine göre doğru Google Cloud depolama hizmetini seçmeyi öğrenin.

---

# Öğrenme Hedefleri

Bu modülü tamamladıktan sonra, şunları yapabileceksiniz:

- Google Cloud'da mevcut olan farklı depolama hizmetlerini anlayın.
- Farklı uygulama senaryoları için doğru depolama çözümünü seçin.
- İlişkisel ve NoSQL veritabanları arasındaki farkları anlayın.
- OLTP ve OLAP iş yüklerini farklılaştırın.
- Cloud Storage, Firestore, Bigtable, Cloud SQL, AlloyDB, Spanner, BigQuery ve Memorystore'un ne zaman kullanılacağını bilin.

---

# Genel Resim

Yeni geliştiricilerin yaptığı en büyük hatalardan biri, her veri türünü tek bir veritabanında depolama çabası yapmasıdır.

Gerçek dünyadaki uygulamalar böyle çalışmaz.

Instagram'a benzer bir uygulama oluşturduğunuzu hayal edin.

Uygulamanız şunları depolaması gerekir:

- Kullanıcı profili bilgileri
- Fotoğraflar
- Videolar
- Yorumlar
- Beğeniler
- Sohbet mesajları
- Analitik veriler
- Önbelleğe alınmış veriler

Bu veri türlerinin tamamen farklı gereksinimleri vardır.

Google Cloud farklı depolama hizmetleri sağlar çünkü her biri farklı bir kullanım durumu için optimize edilmiştir.

**Her şey için en iyi olan tek bir veritabanı yoktur.**

---

# Cloud Storage

## Cloud Storage Nedir?

Cloud Storage, Google Cloud'un yönetilen **Nesne Depolama** hizmetidir.

Bir veritabanının aksine, Cloud Storage dosyanın içeriğini anlamaz.

Cloud Storage için her dosya sadece bir bayt koleksiyonudur.

Yükleme yaparsanız:

- image.jpg
- video.mp4
- report.pdf
- backup.zip

Hepsi tamamen aynı şekilde ele alınır.

---

## En İyi Kullanım Senaryoları

Cloud Storage şunların depolanması için idealdir:

- Resimler
- Videolar
- Belgeler
- Yedeklemeler
- Statik web sitesi varlıkları
- Kullanıcı yüklemeleri
- Günlük dosyaları

Nesneler **5 TB** kadar büyük olabilir.

Cloud Storage şu amaçlar için tasarlanmıştır:

- Yüksek dayanıklılık
- Yüksek kullanılabilirlik
- Büyük ölçeklenebilirlik
- Global erişim

---

## Cloud Storage Ne Zaman Kullanılmaz

Cloud Storage **değildir** bir veritabanı.

Şunlara ihtiyacınız olduğunda kullanmayın:

- SQL sorguları
- İşlemler
- JOIN işlemleri
- Kayıtlar arasındaki ilişkiler

---

# Firestore

## Firestore Nedir?

Firestore, **sunucusuz NoSQL belge veritabanıdır**.

Tablolar ve satırlar yerine veriler şu şekilde organize edilir:

```text
Koleksiyon
    └── Belge
            └── Alanlar
```

Örnek:

```text
Kullanıcılar
    ├── Abdullah
    │      ├── adı
    │      ├── yaşı
    │      └── şehri
    │
    └── John
           ├── adı
           └── ülkesi
```

Belgeler ayrıca iç içe geçmiş nesneler ve alt koleksiyonlar içerebilir.

---

## Avantajları

Firestore şunları sağlar:

- Otomatik ölçeklendirme
- Güçlü tutarlılık
- Gerçek zamanlı güncellemeler
- Çevrimdışı destek
- Esnek şema

SQL veritabanlarının aksine, belgeler aynı yapıya sahip olmak zorunda değildir.

---

## En İyi Kullanım Senaryoları

Firestore mükemmel bir seçimdir:

- Mobil uygulamalar
- Web uygulamaları
- Sohbet uygulamaları
- Sosyal medya uygulamaları
- Kullanıcı profilleri
- Hızla değişen veri yapılarına sahip uygulamalar

---

# Bigtable

## Bigtable Nedir?

Bigtable, **yüksek performanslı NoSQL veritabanıdır** muazzam miktardaki veriler için tasarlanmıştır.

Şunları depolayabilir:

- Milyarlar satır
- Binlerce sütun
- Terabayt ile Petabayt veri

Bigtable aşırı hızlı anahtar-değer aramaları için optimize edilmiştir.

Tipik gecikme süresi **10 milisaniyenin** altındadır.

---

## En İyi Kullanım Senaryoları

Bigtable ideal olduğu yerler:

- Zaman serisi veriler
- IoT veriler
- İzleme sistemleri
- Kullanıcı davranışı takibi
- Olay günlüğü
- Büyük ölçekli operasyonel iş yükler

Örnek:

Her YouTube kullanıcısı bir video izlediğinde, bir olay oluşturulur.

Milyonlarca bu tür olay her saniye geliyor.

Bigtable bu tür iş yükler için tasarlanmıştır.

---

## Bigtable Ne Zaman Kullanılmaz

Bigtable şunlar için uygun değildir:

- SQL sorguları
- Karmaşık birleştirmeler
- İlişkisel veriler

---

# Cloud SQL

## Cloud SQL Nedir?

Cloud SQL, Google'ın yönetilen ilişkisel veritabanı hizmetidir.

Şunları destekler:

- MySQL
- PostgreSQL
- SQL Server

Google otomatik olarak yönetir:

- Yedeklemeler
- Çoğaltma
- Başarısızlık yönetimi
- Bakım

Normal SQL yazmaya devam edersiniz.

---

## En İyi Kullanım Senaryoları

Cloud SQL ideal olduğu yerler:

- Web uygulamaları
- E-ticaret sistemleri
- ERP sistemleri
- CRM uygulamaları
- Geleneksel iş uygulamaları

MySQL veya PostgreSQL kullanan herhangi bir uygulama, en az değişiklikle taşıyabilir.

---

# Birincil Veritabanı ve Okuma Çoğaltması

Cloud SQL **Okuma Çoğaltmalarını** destekler.

Bu konsepti anlamak çok önemlidir.

## Birincil Veritabanı

Birincil veritabanı gerçeğin kaynağıdır.

Şunları halleder:

- INSERT
- UPDATE
- DELETE

SELECT sorgularını da işleyebilir.

---

## Okuma Çoğaltması

Okuma Çoğaltması, Birincil veritabanının bir kopyasıdır.

Otomatik olarak Birincil'den güncellemeleri alır.

Ancak, uygulamalar bir çoğaltmaya **yazamaz**.

Sadece okuma işlemleri için kullanılır.

---

## Neden Okuma Çoğaltmalarını Kullanırsınız?

Çoğu uygulamada:

- Okuma işlemleri yazma işlemlerini büyük ölçüde aşar.

Örnek:

Çevrimiçi bir mağaza alabilir:

- 5.000 yeni sipariş (yazma)
- 5 milyon ürün görünümü (okuma)

Çoğaltmalar olmadan:

```text
Tüm istekler
        │
        ▼
Birincil Veritabanı
```

Çoğaltmalar ile:

```text
              Birincil
           (Okuma + Yazma)
                  │
      ┌───────────┴───────────┐
      ▼                       ▼
 Okuma Çoğaltması      Okuma Çoğaltması
   (Okuma)               (Okuma)
```

Bu, okuma iş yükünü dağıtır ve performansı artırır.

> **Unutmayın:** Okuma Çoğaltması **değildir** bir yedekleme.
> Ölçeklenebilirliği artırmak için vardır.

---

# AlloyDB

## AlloyDB Nedir?

AlloyDB, Google'ın yeni nesil PostgreSQL veritabanıdır.

Geleneksel PostgreSQL'in aksine, AlloyDB şunları ayırır:

- İşlem Gücü
- Depolama

Bu mimari çok daha iyi ölçeklenebilirliğe izin verir.

Google şunları iddia ediyor:

- 4× kadar hızlı işlem performansı
- Analitik sorgularda 100× kadar hızlı

PostgreSQL uyumluluğunu korurken.

---

## En İyi Kullanım Senaryoları

AlloyDB'yi seçin, şunlara ihtiyacınız olduğunda:

- PostgreSQL uyumluluğu
- Yüksek işlem performansı
- Operasyonel veriler üzerinde analitik sorgular
- Otomatik ölçeklendirme

---

# Spanner

## Spanner Nedir?

Spanner, Google'ın küresel olarak dağıtılmış ilişkisel veritabanıdır.

Şunları birleştirir:

- SQL
- Yatay ölçeklenebilirlik
- Güçlü tutarlılık

Cloud SQL'in aksine, Spanner birden fazla bölgede çalışan uygulamalar için tasarlanmıştır.

Ayrıca endüstri lideri **99.999% SLA** sağlar.

---

## En İyi Kullanım Senaryoları

Spanner ideal olduğu yerler:

- Bankacılık sistemleri
- Mali uygulamalar
- Ödeme sistemleri
- Havayolu rezervasyon sistemleri
- Küresel e-ticaret platformları

Tutarlı veriler ile küresel kullanılabilirlik gerektiren herhangi bir uygulama, Spanner için iyi bir adaydır.

---

# BigQuery

## BigQuery Nedir?

BigQuery **değildir** işlemsel bir veritabanı.

Bu, **analitik için oluşturulmuş sunucusuz bir veri ambarıdır**.

BigQuery, büyük veri setlerini analiz etmek için tasarlanmıştır.

Google, bunun tarayabileceğini belirtir:

- Terabayt saniye içinde
- Petabayt dakika içinde

---

## En İyi Kullanım Senaryoları

BigQuery ideal olduğu yerler:

- İşletme Zekası
- Göstergeler Paneli
- Raporlama
- Veri analitiği
- Veri keşfi
- Makine öğrenmesi veri setleri

Örnek:

Şunu sormak yerine:

> "Müşteri #15'i göster."

BigQuery şöyle sorulara cevap verir:

> "Son beş yılda hangi şehir en yüksek satışları oluşturdu?"

---

# Bigtable ve BigQuery Karşılaştırması

Isimleri benzer olsa da, bu ürünler tamamen farklı sorunları çözer.

| Bigtable              | BigQuery                        |
| --------------------- | ------------------------------- |
| NoSQL veritabanı      | Veri ambarı                     |
| Operasyonel veriler   | Depolanmış verileri analiz eder |
| Hızlı okuma ve yazma  | Hızlı analitik sorgular         |
| Anahtar-değer erişimi | SQL analitik                    |
| Milisaniye gecikme    | Büyük veri setlerini tara       |

Farkı hatırlama yöntemi:

> **Bigtable veri depolar.**

> **BigQuery veriler hakkında sorular sorar.**

---

# Memorystore

## Memorystore Nedir?

Memorystore, Google'ın yönetilen bellek içi önbellek hizmetidir.

Şunları destekler:

- Redis
- Memcached

Veriler disk yerine bellekte depolandığı için, erişim aşırı hızlıdır.

---

## En İyi Kullanım Senaryoları

Memorystore yaygın olarak şunlar için kullanılır:

- Oturum depolama
- Uygulama önbelleği
- Oyunlar
- Puanlamalar
- Sık erişilen veriler

Veritabanını tekrar tekrar sorgulamak yerine, uygulamalar doğrudan önbellekten veri alabilirler.

---

# OLTP ve OLAP

Bu iki iş yükü türünü anlamak çok önemlidir.

---

## OLTP (Çevrimiçi İşlem İşleme)

OLTP sistemleri günlük iş operasyonlarını halleder.

Örnekler:

- Sipariş oluşturma
- Giriş yapma
- Ödeme yapma
- Mesaj gönderme
- Müşteri profili güncelleme

Özellikleri:

- Küçük işlemler
- Çok hızlı yanıt süreleri
- Birçok eşzamanlı kullanıcı
- Sık ekler ve güncellemeler

Tipik Google Cloud hizmetleri:

- Cloud SQL
- AlloyDB
- Spanner
- Firestore
- Bigtable

---

## OLAP (Çevrimiçi Analitik İşleme)

OLAP sistemleri mevcut verileri analiz eder.

İşlemleri işlemek yerine, iş sorularına cevap verirler.

Örnekler:

- Geçen yıl hangi ürün en çok satıldı?
- Hangi şehir en yüksek gelir oluşturur?
- Aylık satış trendleri
- Müşteri davranışı analizi

Özellikleri:

- Geniş taramalar
- Karmaşık sorgular
- Toplaştırmalar
- Raporlama
- Göstergeler Paneli

Tipik Google Cloud hizmeti:

- BigQuery

---

## Gerçek Dünya Örneği

Çevrimiçi bir yemek teslimatı uygulaması hayal edin.

### Gün boyunca

Müşteriler:

- Sipariş verir
- Ödeme yapır
- Teslimatı takip eder

Bunlar OLTP işlemleridir.

---

### Gece

Yönetim bilmek istiyor:

- Hangi şehir en çok sipariş verdi?
- Hangi restoran en yüksek gelir elde etti?
- Bölgeye göre ortalama teslimat süresi

Bunlar OLAP işlemleridir.

---

# Doğru Depolama Hizmetini Seçme

| Veri Türü                      | Önerilen Hizmet |
| ------------------------------ | --------------- |
| Resimler                       | Cloud Storage   |
| Videolar                       | Cloud Storage   |
| Belgeler                       | Cloud Storage   |
| Mobil Uygulama Verileri        | Firestore       |
| Sohbet Mesajları               | Firestore       |
| Zaman Serisi Veriler           | Bigtable        |
| Kullanıcı Davranışı            | Bigtable        |
| MySQL/PostgreSQL Uygulamaları  | Cloud SQL       |
| Yüksek Performanslı PostgreSQL | AlloyDB         |
| Global İlişkisel Veritabanı    | Spanner         |
| Analitik ve Raporlama          | BigQuery        |
| Önbellek                       | Memorystore     |

---

# Modül Özeti

Doğru depolama hizmetini seçme, verilerinizi anlamakla ilgilidir.

Kendinize sorun:

- Dosya mı?
- Yapılı mı yoksa yapısız mı?
- İşlemsel mi?
- Analitik mi?
- Global tutarlılık gerekli mi?
- Son derece hızlı okumalara mi ihtiyacınız?
- Geçici önbellek mi?

Google Cloud farklı depolama hizmetleri sunmaktadır çünkü her hizmet farklı bir iş yükü için optimize edilmiştir.

En iyi uygulamalar genellikle birden fazla depolama hizmetini birlikte kullanır.

Örnek:

- Cloud Storage → Ürün resimleri
- Cloud SQL → Siparişler ve müşteriler
- Memorystore → Ürün önbelleği
- BigQuery → Satış raporları

---

# Sertifika İpuçları

Sınav sorularını çözerken, önce veri türünü belirleyin.

- 📁 Dosya → Cloud Storage
- 📄 Esnek belge → Firestore
- 📊 Devasa anahtar-değer verisi → Bigtable
- 🗄️ İlişkisel veritabanı → Cloud SQL
- ⚡ Yüksek performanslı PostgreSQL → AlloyDB
- 🌍 Global ilişkisel veritabanı → Spanner
- 📈 Analitik → BigQuery
- 🚀 Önbellek → Memorystore

Basit bir karar süreci:

> **Ne tür veri depoluyorum ve onunla ne yapmak istiyorum?**

Bu iki soruya cevap vermek, genellikle sizi doğru Google Cloud depolama hizmetine yönlendirir.
