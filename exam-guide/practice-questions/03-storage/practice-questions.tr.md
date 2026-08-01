# Modül 3 — Depolama ve Veritabanları: Alıştırma Soruları

[deep-dive/03-storage](../../../deep-dive/03-storage/storage.md) içeriğini kapsayan senaryo tabanlı alıştırma soruları. Ağırlık, insanların en çok yanıldığı sınav tuzaklarında ve karar tablosu ayrımlarında: Firestore vs Bigtable, Bigtable vs BigQuery ve Cloud SQL vs AlloyDB vs Spanner.

Önce derin dalış metnini oku. Soruları önce kendi başına çözmeyi dene, sonra aşağıdaki cevap anahtarıyla kontrol et.

---

## Sorular

1. Fotoğraf ve video paylaşım girişimi PixShare, kullanıcıların her biri birkaç gigabayta kadar olabilen fotoğraf ve kısa videolar yüklemesine izin veriyor. Uygulama bu dosyaları dünyanın her yerinden HTTP üzerinden sunmak zorunda; ayrıca bazen mobil istemci, büyük bir videonun tamamını indirmeden yalnızca küçük bir byte aralığını çekmek istiyor (örneğin bir küçük resim/kare önizlemesi için). PixShare bu yüklemeleri nerede saklamalı?
   A. Firestore, çünkü esnek bir NoSQL doküman veritabanıdır
   B. Bigtable, çünkü yüksek verimli yazmaları yönetir
   C. Cloud Storage, çünkü HTTP üzerinden erişilen büyük, yapılandırılmamış nesneler için tasarlanmıştır
   D. Cloud SQL, her dosyayı bir BLOB sütununda saklayarak

2. Bir sohbet uygulaması, mesajları mobil ve web istemcilerine gerçek zamanlı olarak senkronize etmek, kullanıcının cihazı çevrimdışıyken çalışmaya devam edip bağlantı gelince otomatik senkronize olmak ve uygulama büyüdükçe evrilebilecek esnek, iç içe geçmiş bir mesaj/konu yapısı saklamak zorunda. Bu uygulama için en uygun veritabanı hangisidir?
   A. Firestore, çünkü gerçek zamanlı senkron, çevrimdışı destek ve esnek doküman modeli sunar
   B. Bigtable, çünkü devasa yazma verimini yönetir
   C. BigQuery, çünkü her boyuttaki veriye ölçeklenebilir
   D. Cloud SQL, çünkü sohbet verisi doğası gereği ilişkiseldir

3. Endüstriyel bir IoT platformu, milyonlarca cihazdan sensör okuması alıyor, saniyede on binlerce yazma işlemi yapıyor ve belirli bir cihaz ID'si için en son okumayı 10 milisaniyenin altında bulabilmesi gerekiyor. Ekip ayrıca mevcut Apache HBase tabanlı araçları minimal değişiklikle yeniden kullanmak istiyor. Hangi servis uygundur?
   A. BigQuery, çünkü petabaytlarca sensör verisini sorgulayabilir
   B. Firestore, çünkü tam yönetilen bir NoSQL veritabanıdır
   C. Cloud SQL, ağır indekslenmiş bir tablo kullanarak
   D. Bigtable, çünkü devasa ölçekte sub-10ms key-value sorguları sunar ve HBase API uyumludur

4. Bir perakende şirketinin İş Zekası ekibi, üç yıllık tıklama akışı (clickstream) verisi — onlarca petabayt — üzerinde herhangi bir sunucu veya küme yönetmeden ad-hoc SQL sorguları çalıştırmak ve dashboard'lar oluşturmak istiyor. Hangi servisi kullanmalılar?
   A. Bigtable, çünkü devasa veri kümeleri için tasarlanmıştır
   B. BigQuery, saniyeler içinde terabaytları, dakikalar içinde petabaytları tarayan serverless bir veri ambarı
   C. AlloyDB, Columnar Engine'ini kullanarak
   D. Raporlama için read replica'lı Cloud SQL

5. PostgreSQL üzerine kurulu küçük bir dahili araç, tek bir bölgede tek bir ekip için günde birkaç yüz işlem işliyor. Gelişmiş analitiğe ihtiyaç yok, çoklu bölgeye geçme planı yok; ekip sadece mevcut veritabanını minimal değişiklikle taşımak istiyor. Hangi servis en uygun seçimdir?
   A. Sektör lideri SLA'sı için Spanner
   B. Maksimum PostgreSQL performansı için AlloyDB
   C. PostgreSQL uyumlu, genel amaçlı yönetilen ilişkisel veritabanı olan Cloud SQL
   D. Otomatik ölçeklendiği için Bigtable

6. Bir bankanın PostgreSQL tabanlı defter (ledger) sistemi, saniyede binlerce transactional yazmayı (para yatırma, çekme) işlemeye devam ederken, aynı veri üzerinde gerçek zamanlı analitik raporlar da çalıştırmak zorunda — bunu ayrı bir veri ambarına aktarmadan ve PostgreSQL ekosisteminden çıkmadan yapmak istiyor. Hangi servis uygundur?
   A. Columnar Engine'i, transactional performanstan ödün vermeden analitik sorguları hızlandıran AlloyDB
   B. PostgreSQL için Cloud SQL
   C. Defter verisini oraya taşıyarak BigQuery
   D. Esnek şeması için Firestore

7. Küresel bir ödeme platformu Kuzey Amerika, Avrupa ve Asya'da faaliyet gösteriyor. Tam ACID transaction'larıyla ilişkisel veri, dünya çapında güçlü tutarlılık (bir bölgedeki yazma her yerde anında görünür olmalı), işlem hacmi arttıkça yatay ölçeklenebilirlik ve en az %99.999 SLA gerektiriyor. Hangi veritabanını seçmeliler?
   A. Bölgeler arası read replica'lı Cloud SQL
   B. Çoklu bölgeli kümelere sahip AlloyDB
   C. Devasa ölçeği için Bigtable
   D. Küresel güçlü tutarlılık ve yatay ölçeklenebilirlik için tasarlanmış Spanner

8. Bir e-ticaret arka ucu, saniyede binlerce küçük ve hızlı transaction kaydetmek zorunda — sipariş oluşturma, envanter sayısı güncelleme, hesap bakiyesi ayarlama — her biri anında tutarlılık gerektiriyor. Bu hangi iş yükü kategorisidir ve hangi servis uygundur?
   A. OLAP iş yükü, dolayısıyla BigQuery
   B. OLTP iş yükü, dolayısıyla Cloud SQL
   C. OLAP iş yükü, dolayısıyla Bigtable
   D. OLTP iş yükü, dolayısıyla Cloud Storage

9. Bir ekip, bir Cloud SQL örneğine bağlanması gereken bir Cloud Run servisi dağıtıyor. Her ortam için elle IP izin listesi (allowlist) veya SSL sertifikası yönetmeden güvenli erişim istiyorlar. Ne kullanmalılar?
   A. Geniş bir güvenlik duvarı izin listesine sahip genel (public) IP
   B. Başka bir projeye VPC Peering
   C. Trafiği elle IP/SSL yapılandırmasına gerek kalmadan güvenli biçimde tünelleyen Cloud SQL Auth Proxy
   D. Uygulama koduna gömülü bir Service Account anahtarı

10. Bir mobil oyun, sorted set'ler üzerine kurulu gerçek zamanlı bir liderlik tablosu için üretimde zaten Redis kullanıyor ve ekip, uygulama kodunda hiçbir değişiklik yapmadan tam yönetilen bir doğrudan değiştirme (drop-in replacement) istiyor. Hangi Memorystore seçeneğini seçmeliler?
    A. Tam protokol uyumlu olan ve sorted set gibi daha zengin veri yapılarını destekleyen Memorystore for Redis
    B. Daha basit olduğu için Memorystore for Memcached
    C. Gerçek zamanlı güncellemeler için Firestore
    D. Düşük gecikmeli sorgular için Bigtable

11. Bir tasarım toplantısında mühendislerden biri, kullanıcı alışveriş sepeti bakiyelerini başka hiçbir arka planda destekleyici veritabanı olmadan yalnızca Memorystore'da saklamayı öneriyor; "yeterince hızlı ve ekstra bileşenden kaçınıyoruz" diyor. Bu tasarımın temel riski nedir?
    A. Risk yok; Memorystore birincil veri için yeterince dayanıklıdır
    B. Memorystore IAM veya VPC ile güvenli hale getirilemez
    C. Memorystore gereken verimi kaldıramaz
    D. Memorystore bellek içi bir önbellektir, dayanıklı bir birincil veri kaynağı (source of truth) değildir — sepet verisi kaybolabilir ve kalıcı bir veritabanında tutulmalıdır

12. Bir uygulama, kullanıcı tarafından yüklenen profil fotoğraflarını, sıkı referans bütünlüğüne sahip ilişkisel bir ürün kataloğunu, hızlı bir oturum önbelleğini ve iş zekası dashboard'ları için tarihsel veriyi saklamak zorunda. Önerilen mimari nedir?
    A. Basitlik için dört veri türünün tamamını tek bir Cloud SQL örneğine sıkıştırmak
    B. Her kullanım senaryosu için amaca uygun bir servis kullanmak — Cloud Storage, Cloud SQL, Memorystore ve BigQuery — ve bunları çok-dilli kalıcılık (polyglot persistence) mimarisinde birleştirmek
    C. Her türlü veri şekli için yeterince esnek olduğundan her şeyi Firestore'da saklamak
    D. Her boyuta ölçeklendiği için her şeyi Bigtable'da saklamak

13. Bir ekibin Cloud SQL örneği tek bir örnek için maksimum depolama kapasitesine yaklaşıyor ve veri hacmi büyümeye devam ediyor. Tek bir örneğin limitini aşarak ölçeklemenin önerilen yolu nedir?
    A. İmkânsızdır — tamamen farklı bir depolama paradigmasına göç etmeleri gerekir
    B. Mevcut tüm veriyi kalıcı olarak tek bir örneğe sığacak şekilde sıkıştırmak
    C. Veriyi birden çok veritabanı örneğine bölmek (shard/partition), çünkü boyut limitleri mimari başına değil örnek başınadır
    D. Cloud Storage'a geçip satırları ayrı nesneler olarak saklamak

14. Bir video akış (streaming) özelliği, kullanıcıların birkaç gigabaytlık bir video dosyasının herhangi bir noktasına atlayıp dosyanın tamamını önce indirmeden hemen oynatmaya başlamasına izin vermek zorunda. Videolar Cloud Storage'da nesne olarak saklanıyor. Bunu hangi yetenek mümkün kılar?
    A. Nesnenin yalnızca istenen byte aralığını getiren ranged GET isteği
    B. Cloud Storage kısmi indirmeyi desteklemez; nesnenin tamamının çekilmesi gerekir
    C. Her videoyu binlerce küçük Firestore dokümanına bölmek
    D. Rastgele erişim için videoyu Cloud SQL'de bir BLOB sütununda saklamak

15. Bir analitik ekibi şu anda kendi yönettiği altyapıda Apache HBase çalıştırıyor ve minimal kod değişikliğiyle yönetilen bir Google Cloud servisine göç etmek istiyor. Ayrıca trafik ani yükselişleri sırasında (sezonluk bir kampanya gibi) hiçbir servis kesintisi olmadan kümelerini yeniden boyutlandırmaları gerekiyor. Her iki gereksinimi de hangi servis karşılar?
    A. Otomatik ölçeklendiği için Firestore
    B. Serverless olduğu için BigQuery
    C. Dikey ölçekleme kullanan Cloud SQL
    D. HBase API uyumlu olan ve kesintisiz, anında küme yeniden boyutlandırmayı destekleyen Bigtable

---

## Cevap Anahtarı ve Açıklamalar

1. **C — Cloud Storage.** Cloud Storage, fotoğraf ve video gibi büyük, yapılandırılmamış blob'lar için tasarlanmış nesne depolamadır; nesne adıyla adreslenir ve ranged GET ile kısmi byte aralıkları çekilebilir, nesne başına 5 TB'a kadar. Firestore (A) baştan çekici bir çeldiricidir — o da esnek bir Google Cloud NoSQL servisi ama yapılandırılmış, sorgulanabilir dokümanlar için tasarlanmıştır, gigabaytlık ikili blob'lar için değil.

2. **A — Firestore.** Firestore'un doküman/koleksiyon modeli, güçlü tutarlılığı ve yerleşik gerçek zamanlı senkron + çevrimdışı desteği, tam olarak sohbet gibi mobil/web uygulamaları için tasarlanmıştır. Bigtable (B) klasik tuzaktır — ikisi de NoSQL'dir ama Bigtable, milyarlarca satırda sub-10ms tek-anahtarlı sorguları hedefler, gerçek zamanlı çevrimdışı senkronlu hiyerarşik dokümanları değil.

3. **D — Bigtable.** Sub-10ms sorgulara sahip, seyrek ve devasa bir tablo, HBase API uyumluluğuyla birlikte tam olarak Bigtable'ın profilidir. BigQuery (A) yine isim benzerliği tuzağıdır — o bir analitik veri ambarıdır, milisaniyelik operasyonel sorgular için tasarlanmamıştır.

4. **B — BigQuery.** Petabaytlar üzerinde SQL ile serverless OLAP, BigQuery'nin temel amacıdır. Bigtable (A) yine isim tuzağıdır — o operasyonel bir NoSQL key-value deposudur, serverless bir SQL veri ambarı değildir ve ad-hoc analitik SQL için tasarlanmamıştır.

5. **C — Cloud SQL.** Basit, düşük trafikli, tek bölgeli, minimal-refactor göç senaryosu tam olarak Cloud SQL'in alanıdır. AlloyDB (B) tuzaktır — o da PostgreSQL uyumludur ama fazladan performans ve HTAP yetenekleri bu kadar hafif bir iş yükü için gereksiz karmaşıklıktır.

6. **A — AlloyDB.** AlloyDB'nin compute/storage ayrımı ve Columnar Engine'i, transactional verime zarar vermeden analitikte 100 kata kadar hızlanma sağlar — bu HTAP'ın tanımıdır. Cloud SQL (B) tuzaktır çünkü o da yönetilen PostgreSQL sunar ama karma transactional-artı-analitik bir iş yükü için AlloyDB'nin analitik hızlanmasını sunmaz.

7. **D — Spanner.** Güçlü tutarlılık, yatay ölçek, ilişkisel veri, küresel erişim ve %99.999 SLA'nın hepsi bir arada olduğunda cevap tartışmasız Spanner'dır — başka hiçbir servis bunların hepsini birleştirmez. Cloud SQL'in (A) bölgeler arası read replica'ları yalnızca neredeyse-gerçek-zamanlı kopyalar sunar, gerçek anlamda küresel güçlü tutarlılık değil.

8. **B — OLTP iş yükü, Cloud SQL.** Çok sayıda küçük, hızlı ve anında tutarlı transaction, OLTP'nin ders kitabı tanımıdır; Cloud SQL (AlloyDB ve Spanner ile birlikte) bunun için tasarlanmıştır. BigQuery (A) tuzaktır: o OLAP için tasarlanmıştır — büyük ölçekli analitik tarama ve raporlama — saniyenin altında transactional yazma için değil.

9. **C — Cloud SQL Auth Proxy.** Proxy, uygulamayla standart veritabanı protokolüyle konuşan yerel bir istemci çalıştırır ve sunucu tarafına güvenli bir tünel kurar; böylece izin listelerini veya sertifikaları elle yönetme ihtiyacını ortadan kaldırır. A seçeneği tuzaktır — proxy'nin yerini almak için var olduğu, elle yapılan hataya açık yaklaşımdır.

10. **A — Memorystore for Redis.** Memorystore for Redis, sorted set'ler dahil olmak üzere açık kaynak Redis ile tam protokol uyumludur, böylece mevcut liderlik tablosu kodu değiştirilmeden çalışır. Memcached (B) tuzaktır — Memorystore onu da destekler ama Memcached, Redis'in sorted set gibi zengin veri yapılarına sahip olmayan daha basit bir key-value önbelleğidir.

11. **D — Memorystore bir önbelleklir, dayanıklı bir birincil veri kaynağı değildir.** Memorystore, kalıcı bir veritabanının (Cloud SQL, Firestore, Spanner vb.) önünde hızlı, geçici bir önbellek katmanı olarak tasarlanmıştır; asla kaybolmaması gereken verinin birincil deposu olarak değil. A seçeneği, "Memorystore hızlıdır" demeyi hatırlayıp dayanıklı olmadığını unutanlar için tuzaktır.

12. **B — Çok-dilli kalıcılık (polyglot persistence).** Modülün temel dersi, tek bir veritabanıyla sınırlı olmadığındır — her iş yükü için en uygun servisi (nesne depolama, ilişkisel, önbellek, ambar) seç ve birleştir. A seçeneği, modülün başında vurgulanan "tek beden herkese uymaz" ilkesini ihlal eden tuzaktır.

13. **C — Birden çok örneğe bölmek (shard/partition).** Depolama limitleri veritabanı örneği başınadır; veriyi birden çok örneğe bölmek, servisi terk etmeden tek bir örneğin tavanını aşmanın standart yoludur. A seçeneği, limiti modülün açıkça öyle olmadığını söylediği bir mimari çıkmaz sokak gibi abartır.

14. **A — Ranged GET isteği.** Cloud Storage, bir istemcinin bir nesnenin belirli bir byte aralığını istemesine izin veren ranged GET isteklerini destekler — büyük bir videoyu tamamen indirmeden içine atlamak için tam olarak gereken budur. B seçeneği, nesne depolamanın yalnızca nesnenin tamamının indirilmesini desteklediğini varsayanlar için tuzaktır.

15. **D — Bigtable.** Bigtable'ın HBase API uyumluluğu göçü kolaylaştırır ve kesintisiz ölçekleme modeli, yeniden boyutlandırma sırasında küme yapılandırma değişikliklerini anında, kesintisiz uygular. BigQuery (B) tekrar eden isim tuzağıdır — serverless'tır ama bir analitik veri ambarıdır, HBase uyumlu operasyonel bir depo değildir.
