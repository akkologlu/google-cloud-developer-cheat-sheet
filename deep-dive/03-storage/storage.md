# Google Cloud Veri Depolama Seçenekleri — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 3 — Overview of Data Storage Options** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Amaç servisleri ezberletmek değil; her birinin **neden var olduğunu, nasıl çalıştığını ve hangi iş yükünde ideal, hangisinde ideal-olmadığını** sezgi düzeyinde oturtmak. Bu modülün kalbinde tek bir cümle var: **"Tek beden herkese uymaz" (no one size fits all).** Doğru depolama seçimi, uygulamanın verisine ve iş yüküne bağlıdır. Acele etme; her servisi "hangi veri türü için doğdu" gözüyle sindirerek oku. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül neyi öğretiyor ve neden önemli?

İlk modülde (Core Infrastructure) Google Cloud'un yapı taşlarını — VM'ler (sanal makineler), ağlar, IAM, depolama seçeneklerine kuşbakışı — tanıdın. İkinci modülde bir **geliştirici** olarak iyi bir bulut uygulamasının nasıl tasarlandığını öğrendin: durumsuzluk, gevşek bağlılık, önbellekleme, asenkron işleme. O modülde defalarca "durumu ayrı bir veritabanında tut", "veriyi önbelleğe al", "yüklenen dosyayı Cloud Storage'a koy" dedik ama **hangi veritabanını, hangi önbelleği, hangi depolamayı** seçeceğimizi havada bıraktık. İşte bu modül tam o boşluğu dolduruyor.

Gerçek bir uygulama tek tip veri saklamaz. Aynı uygulama içinde çok farklı veri türleri bir arada yaşar:

- **Resim ve dosya** gibi büyük, yapılandırılmamış (unstructured) nesneler — kullanıcının yüklediği videolar, fotoğraflar, belgeler.
- **Yüksek hacimli kullanıcı mesajları** — saniyede binlerce yazma isteğiyle akan, hızlı erişilmesi gereken veri.
- **Transactional (işlemsel) veri** — sipariş, ödeme, hesap bakiyesi gibi tutarlılığın hayati olduğu ilişkisel veri.
- **Sık erişilen veri için önbellek (cache)** — her seferinde yeniden hesaplamak yerine bellekten anında dönmek istediğin veri.
- **İş zekası (business intelligence) için toplanan veri** — analiz edilmek, sorgulanmak, raporlanmak üzere biriktirilen devasa veri kümeleri.

Google Cloud, bu türlerin **her biri için ayrı bir yönetilen servis** sunar: Cloud Storage, Firestore, Bigtable, Cloud SQL, AlloyDB, Spanner, BigQuery ve önbellek için Memorystore. Bu modülün hedefi, her servisin **ideal** ve **ideal-olmayan** kullanım senaryolarını öğrenmen; böylece bir uygulama tasarlarken "bu veri parçası nereye gitmeli" sorusuna güvenle cevap verebilmen.

> **Neden bu bakış açısı önemli?** Çünkü sertifikada ve gerçek mimaride en sık yapılan hata, "tek bir veritabanına her şeyi sığdırmaya çalışmak"tır. Bir ürünün neye **ideal olduğunu** bilmek kadar, neye **ideal olmadığını** bilmek de kritiktir. Bir soruda "yüksek hacimli, sub-10ms key-value lookup" geçiyorsa cevap Bigtable'dır; "güçlü tutarlılıkla küresel ilişkisel işlem" geçiyorsa Spanner'dır. Anahtar kelimeleri servislere bağlamayı öğreneceksin.

---

## "Tek beden herkese uymaz": Karar bir iş yükü kararıdır

Modülün en temel mesajını en başta netleştirelim. Depolama ve veritabanı seçimi **uygulamaya ve iş yüküne bağlıdır.** Evrensel olarak "en iyi" bir veritabanı yoktur; yalnızca belirli bir kullanım senaryosu için **en uygun** olan vardır.

Ayrıca çok önemli bir özgürlüğün var: **Tek bir veritabanıyla sınırlı değilsin.** Modern bir uygulama pekâlâ aynı anda birkaç depolama servisini birden kullanabilir — kullanıcı fotoğrafları için Cloud Storage, kullanıcı profilleri için Firestore, önbellek için Memorystore, faturalandırma için Cloud SQL, analitik için BigQuery. Her kullanım senaryosu için o senaryoya **en uygun** veritabanını seçersin. Buna genellikle "polyglot persistence" (çok dilli kalıcılık) denir.

Bu özgürlüğün pratik bir sonucu da şu: **Boyut limitleri veritabanı başınadır.** Bir servisin depolama limitine dayanırsan, veriyi **birden çok veritabanı örneğine bölerek (sharding/partitioning)** limitleri aşabilirsin. Yani limit, mimarini kilitleyen bir duvar değil, tasarımını şekillendiren bir parametredir.

Servis seçiminde göz önünde tutman gereken teknik boyutları da baştan sayalım; bu modülün geri kalanı boyunca bu ölçütleri her servise uygulayacağız:

- **Okuma/yazma gecikmesi (read/write latency):** İsteğin ne kadar hızlı yanıtlanması gerekiyor? Sub-10ms mi, yoksa saniyeler kabul edilebilir mi?
- **Verinin tipik boyutu:** Kilobaytlık kayıtlar mı, gigabaytlık nesneler mi, petabaytlık tablolar mı?
- **Depolama tipi:** Yapılandırılmamış nesne mi (blob), doküman mı, ilişkisel tablo mu, key-value mu, analitik ambar mı?

Şimdi servisleri tek tek gezelim.

---

# Yapılandırılmamış Nesneler: Cloud Storage

## Neden var?

Bir uygulamanın en yaygın ihtiyaçlarından biri, **büyük ve yapılandırılmamış (unstructured) dosyaları** saklamaktır: kullanıcının yüklediği videolar, fotoğraflar, PDF'ler, ses dosyaları, yedekler, arşivler. Bu tür veriyi bir ilişkisel veritabanına satır olarak koymak akıl kârı değildir — veritabanları küçük, yapılandırılmış kayıtlar için tasarlanmıştır, gigabaytlık video dosyaları için değil. İşte **Cloud Storage (birleşik nesne depolama)** tam bu boşluk için var: veriyi dünyanın her yerinde sun, analiz et veya arşivle.

> **İlk modülle bağ:** İkinci modülde asenkron işlemeyi anlatırken "yüklenen resimleri bir Cloud Storage bucket'ına sakla, sonra yeni resim yüklenince tetiklenen bir Cloud Run fonksiyonu onu işlesin" demiştik. İşte o örneğin depolama katmanı buydu. Cloud Storage, olay güdümlü mimarilerin doğal başlangıç noktasıdır.

## Nasıl çalışır?

Cloud Storage'ı bir **nesne deposu (object store)** olarak düşün. İçine attığın her şey bir "nesne"dir ve şu özellikleri taşır:

- **Erişim HTTP istekleriyle olur.** Nesneye bir URL üzerinden ulaşırsın. Hatta bir nesnenin sadece bir **parçasını** almak istersen "ranged GET" isteğiyle byte aralığı belirterek dosyanın yalnızca bir bölümünü çekebilirsin — koca bir videoyu indirmeden ortasından bir dilim alabilirsin.
- **Tek anahtar, nesnenin adıdır.** Cloud Storage bir key-value deposu gibi davranır: anahtar nesnenin adı, değer ise nesnenin içeriğidir. Karmaşık sorgular, indeksler, JOIN'ler yoktur; sadece "şu adı ver, bu nesneyi al".
- **Nesnenin kendisi yapılandırılmamış byte'lardır.** Cloud Storage nesnenin içine bakmaz, onun ne olduğuyla ilgilenmez — senin için sadece bir byte yığınıdır. Nesnenin yanında **object metadata** (üstveri) tutulur ama nesnenin içeriği Cloud Storage açısından **anlamsız (opak) byte** olarak işlenir.
- **Her nesne 5 TB'a kadar** olabilir.

Cloud Storage; **yüksek kullanılabilirlik (availability), dayanıklılık (durability), ölçeklenebilirlik (scalability) ve tutarlılık (consistency)** için tasarlanmıştır. Yani nesnelerin kaybolmaz, her yerden erişilebilir ve tutarlı biçimde okunur.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **Statik web sitesi barındırma** — HTML, CSS, JS ve görsellerini doğrudan bir bucket'tan sunmak.
- **Büyük statik içerik sunma** — video, foto, indirilebilir dosyalar.
- **Kullanıcı tarafından yüklenen içerik** — uygulamana kullanıcıların yüklediği her tür medya.
- **Genel olarak her türlü yapılandırılmamış veri** — yedekler, arşivler, log dökümleri, blob'lar.

**İdeal değil:** İçinde arama/sorgu yapman gereken, birbiriyle ilişkili, yapılandırılmış veriyi Cloud Storage'a koymak. Orada anahtar yalnızca nesne adıdır; "fiyatı 100'den büyük tüm ürünleri getir" gibi bir sorgu yapamazsın. Bu tür veri bir veritabanına aittir.

> **Sınav tuzağı:** "Unstructured" (yapılandırılmamış) kelimesini gördüğün an aklına **Cloud Storage** gelmeli. Aynı şekilde "static website hosting", "store images/videos/blobs", "user-uploaded files" ifadeleri de neredeyse her zaman Cloud Storage'ı işaret eder. Bir soruda veri üzerinde SQL sorgusu, transaction ya da ilişki geçiyorsa Cloud Storage **yanlış** cevaptır — o bir veritabanı işidir.

---

# Esnek Doküman Verisi: Firestore

## Neden var?

Mobil ve web uygulamaları geliştirirken sık karşılaşılan bir ihtiyaç vardır: **esnek, hızlı değişen, hiyerarşik veriyi** saklamak ve bunu istemcilere (telefon, tarayıcı) gerçek zamanlı olarak yansıtmak. Kullanıcı profilleri, sohbet mesajları, uygulama durumu... Bunlar için katı bir tablo şeması hantaldır; ayrıca cihaz çevrimdışıyken bile çalışabilen, bağlantı gelince senkronize olan bir yapı istersin. **Firestore**, tam bu senaryo için doğmuş, hızlı, tam yönetilen, **serverless bir NoSQL doküman veritabanıdır.** Otomatik ölçekleme, yüksek performans ve kolay uygulama geliştirme için tasarlanmıştır.

> **İlk modülle bağ:** İkinci modülde durumsuzluk (statelessness) ilkesini anlatırken şöyle demiştik: "Cloud Run servisleri durum saklamaz; peki veri nereye gider? **Ayrı bir veritabanında** — örneğin Firestore." İşte o cümlenin karşılığı burası. Durumsuz bir hesaplama katmanının arkasındaki durum katmanı, çoğu web/mobil senaryoda Firestore olur.

## Nasıl çalışır?

Firestore'un veri modeli **hiyerarşiktir** ve iki temel kavrama dayanır:

- **Document (doküman):** Veri, dokümanlarda tutulur. Bir doküman, alan-değer çiftlerinden oluşan bir kayıttır; ama düz bir satır değildir — içinde **karmaşık, iç içe geçmiş (nested) nesneler** barındırabilir.
- **Collection (koleksiyon):** Dokümanlar koleksiyonlar içinde organize edilir. Dahası, bir doküman kendi içinde **subcollection (alt koleksiyon)** tutabilir; böylece ağaç gibi bir hiyerarşi kurulur.

Bu model esnektir: her doküman aynı şemaya uymak zorunda değildir, veri yapın uygulamanla birlikte evrilebilir. Firestore'un öne çıkan diğer özellikleri:

- **Güçlü tutarlı (strongly consistent) depolama katmanı** — bir yazma tamamlandığında, sonraki okumalar o güncel değeri görür.
- **Gerçek zamanlı güncellemeler ve çevrimdışı (offline) destek** — istemci kütüphaneleri veriyi cihaza senkronize eder; bağlantı kesilse bile uygulama çalışır, bağlantı gelince değişiklikler eşitlenir.
- **Mobil ve web için istemci kütüphaneleri (client libraries)** — uygulamadan doğrudan Firestore'a bağlanırsın.
- Google altyapısıyla **otomatik ölçeklenir** — sen kapasite yönetmezsin.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **Mobil ve web uygulamaları**, özellikle **esnek veri saklama** gereksinimi olanlar.
- **Harici kullanıcı hesaplarına** sahip uygulamalar — Firebase ekosistemiyle kimlik doğrulamaya doğal biçimde bağlanır.
- Gerçek zamanlı senkronizasyon ve çevrimdışı çalışma isteyen istemci-yoğun uygulamalar.

**İdeal değil:** Petabayt ölçeğinde, saniyede milyonlarca yazma alan analitik/operasyonel iş yükleri (orası Bigtable'ın işi) ya da karmaşık çok tablolu ilişkisel işlemler ve JOIN'ler (orası Cloud SQL/Spanner'ın işi).

> **Sınav tuzağı — Firestore vs Bigtable:** İkisi de "NoSQL" ve Google'ın veritabanları olduğu için karıştırılır ama tamamen farklı senaryolar içindir. **Firestore** = doküman modeli, hiyerarşik, mobil/web, gerçek zamanlı senkron ve çevrimdışı, esnek veri. **Bigtable** = devasa, tek-anahtarlı (single-keyed), sparse tablolar, sub-10ms key-value lookup, saniyede muazzam okuma/yazma. Soruda "mobile/web app, real-time, offline, flexible documents" görürsen Firestore; "billions of rows, petabytes, high-throughput, sub-10ms lookups" görürsen Bigtable.

---

# Yüksek Hacimli Key-Value: Bigtable

## Neden var?

Bazı uygulamalar **devasa hacimde**, sürekli akan veriyi, **çok düşük gecikmeyle** okuyup yazmak zorundadır: kullanıcı davranış izleri (click stream), IoT sensör ölçümleri, finansal zaman serileri, reklam gösterim kayıtları. Burada milyarlarca satır, saniyede on binlerce işlem ve milisaniyelik yanıt süresi gerekir. İlişkisel bir veritabanı bu ölçekte boğulur. **Bigtable**, tam bu ihtiyaç için tasarlanmış **yüksek performanslı bir NoSQL veritabanı servisidir.**

## Nasıl çalışır?

Bigtable'ın veri modeli, **seyrek doldurulmuş (sparsely populated) devasa bir tablodur:**

- **Milyarlarca satıra ve binlerce sütuna** ölçeklenir; **terabaytlardan petabaytlara** veri saklar.
- "Sparse" olması şu demek: satırların çoğu hücresi boş olabilir ve boş hücreler yer kaplamaz — bu yüzden çok geniş, seyrek tablolar verimli tutulur.
- **Hızlı key-value lookup** için optimize edilmiştir: **tutarlı, sub-10ms (10 milisaniyenin altında) gecikme** verir. Bu hız sayesinde kullanıcı davranışı gibi hızlı yazılan/okunan verileri saklamak için mükemmeldir.
- **Sorunsuz ölçekleme (seamless scaling):** Dağıtım yapılandırmasında (deployment config) yaptığın değişiklikler **anında** etki eder ve yeniden yapılandırma (reconfiguration) sırasında **downtime yaşanmaz.** Kümeni büyütüp küçültürken servis kesintiye uğramaz.
- **HBase API uyumluluğu:** Açık kaynak endüstri standardı **Apache HBase API'sini** destekler. Bu, hem HBase ekosistemindeki araçları kullanabilmeni hem de satıcıya kilitlenme (vendor lock-in) endişeni azaltmanı sağlar.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **Operasyonel ve analitik uygulamalar** — hem canlı hizmet hem büyük ölçekli analiz.
- **Büyük miktarda tek-anahtarlı (single-keyed) veri** saklama — anahtara göre hızlı erişim.
- **MapReduce operasyonları** — devasa veri kümeleri üzerinde toplu işleme.
- Kullanıcı davranışı, zaman serisi, IoT, finans piyasası verisi gibi yüksek verimli (high-throughput) senaryolar.

**İdeal değil:** Karmaşık, çok anahtarlı sorgular, ilişkisel işlemler, transaction'lar veya ad-hoc SQL analitiği. Bigtable'da tek bir satır anahtarına göre tasarım yaparsın; "birden çok koşula göre filtrele, JOIN yap" gibi ihtiyaçların varsa yanlış araçtasın.

> **Sınav tuzağı — Bigtable vs BigQuery (isim benzerliği!):** Bu ikisi isim benzerliği yüzünden en çok karıştırılan çifttir, ama tamamen farklı dünyalardır. **Bigtable** = düşük gecikmeli, operasyonel **NoSQL key-value** deposu; canlı uygulamaların milisaniyelik okuma/yazması için. **BigQuery** = **analitik veri ambarı (data warehouse)**; saniyeler içinde terabayt tarayıp raporlayan OLAP motoru. Biri "uygulaman canlı çalışırken hızlıca veri yaz/oku" (Bigtable), diğeri "biriken devasa veriyi sorgula ve analiz et" (BigQuery). Soruda "sub-10ms, key-value, operational" → Bigtable; "data warehouse, analytics, SQL over petabytes" → BigQuery.

---

# İlişkisel ve Yönetilen: Cloud SQL

## Neden var?

Dünyadaki uygulamaların çok büyük kısmı hâlâ klasik **ilişkisel veritabanları** üzerine kuruludur: MySQL, PostgreSQL, SQL Server. Bunlar yapılandırılmış veriyi tablolar, ilişkiler ve SQL sorgularıyla yönetir ve **transaction (işlem)** garantileri sunar. Ancak bir ilişkisel veritabanını üretimde ayakta tutmak — replikasyon kurmak, yük devretmeyi (failover) ayarlamak, yedek almak, güncelleme yapmak — ciddi operasyonel yüktür. **Cloud SQL**, bu yükü Google'a devreden **yönetilen ilişkisel veritabanı servisidir.** Sen uygulamana odaklanırsın, Google altyapıyı yönetir.

## Nasıl çalışır?

Cloud SQL, **MySQL, PostgreSQL ve SQL Server** uyumlu veritabanları sunar. "Uyumlu" olması kritik: mevcut MySQL/PostgreSQL/SQL Server uygulamanı **minimal refactor ile** taşıyabilirsin, çünkü aynı motoru konuşur.

Google senin yerine şunları yönetir:

- **Replikasyon (replication):** Primary (birincil) örnek, bir veya daha fazla **read replica**'ya (okuma kopyası) çoğaltılır. Read replica, primary'deki değişiklikleri **neredeyse gerçek zamanlı** yansıtan bir kopyadır; okuma yükünü bu kopyalara dağıtarak primary'yi rahatlatırsın.
- **Otomatik yük devretme (automatic failover):** Yüksek kullanılabilirlik için, primary örnek çökerse sistem otomatik olarak devralır.
- **Yedekleme (backup):** Replikasyon ve yedekler kolayca yapılandırılır.

### Cloud SQL Auth Proxy: Güvenli erişimin zarif yolu

Bir veritabanına internet üzerinden güvenli erişim vermek klasik olarak zahmetlidir: izin verilen IP adreslerini (allowlist) tek tek girmen, SSL sertifikaları yönetmen gerekir. **Cloud SQL Auth Proxy** bu derdi ortadan kaldırır. Nasıl?

- Yerelde (uygulamanın yanında) çalışan bir **istemci (proxy)** vardır.
- Uygulaman, standart veritabanı protokolüyle **bu yerel proxy ile** konuşur — sanki veritabanı hemen yanındaymış gibi.
- Proxy ise sunucu tarafındaki eş süreçle **güvenli bir tünel** kurar ve trafiği oradan taşır.
- Sonuç: İzin verilen IP'leri veya SSL sertifikalarını elle yapılandırmadan **güvenli erişim** elde edersin.

> **İlk modülle bağ:** Bu proxy deseni, ikinci modüldeki "kimlik ve kimlik bilgileriyle güvenli erişim" fikrinin somut bir örneğidir. Birazdan göreceğin **AlloyDB Auth Proxy** da aynı mantıkla çalışır — Cloud SQL Auth Proxy'nin kardeşidir.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **Web framework'leri** ve **yapılandırılmış veri** gerektiren klasik uygulamalar.
- **OLTP (Online Transaction Processing)** iş yükleri — çok sayıda küçük, hızlı transaction (sipariş oluştur, bakiye güncelle, kayıt ekle).
- MySQL, PostgreSQL veya SQL Server kullanan ve Google Cloud'a **minimal refactor ile** göç edecek uygulamalar.

**İdeal değil:** Tek bölgenin ölçeğini aşan, küresel, yatay ölçeklenmesi gereken devasa ilişkisel iş yükleri (orası Spanner) ya da ağır analitik/raporlama sorguları (orası BigQuery).

> **Sınav tuzağı — OLTP vs OLAP:** Bu ayrımı ezberle. **OLTP** (Online Transaction Processing) = çok sayıda küçük, hızlı **işlem**; kayıt yaz/güncelle/oku; Cloud SQL, AlloyDB, Spanner buna hizmet eder. **OLAP** (Online Analytical Processing) = büyük veri üzerinde **analitik sorgular**, toplama, raporlama; BigQuery buna hizmet eder. Soruda "transactions, web app, structured data" → OLTP → Cloud SQL. "analytics, reporting, data warehouse, scan terabytes" → OLAP → BigQuery.

---

# PostgreSQL'in Yüksek Performanslı Hali: AlloyDB

## Neden var?

PostgreSQL, dünyanın en sevilen açık kaynak ilişkisel veritabanlarından biridir; ama tarihsel bir sınırı vardır. Klasik olarak PostgreSQL **tek bir VM üzerinde, ona bağlı bir diskle (attached disk)** çalışır. Bu mimariyi ölçeklemek zordur — tek makinenin sınırlarına dayanırsın. **AlloyDB**, "PostgreSQL uyumluluğundan vazgeçmeden Google ölçeğinde performans ve ölçeklenebilirlik" sunmak için doğdu: **tam yönetilen, yüksek performanslı, PostgreSQL uyumlu bir veritabanı servisi.** Google'ın altyapı gücünü PostgreSQL'in tanıdıklığıyla birleştirir.

## Nasıl çalışır?

AlloyDB'nin kilit mimari fikri, **compute (işlem) ile storage (depolama) katmanlarını ayırmasıdır.** Klasik PostgreSQL'in "tek VM + bağlı disk" modelinden farklı olarak, AlloyDB hesaplama ile depolamayı birbirinden bağımsız katmanlara böler. Bu neden önemli? Çünkü Google'ın en iyi ölçeklenen veritabanları — **Bigtable ve Spanner** — de aynı ayrımı kullanır; işlem ve depolamayı ayırmak, her katmanı bağımsız ölçeklemeni sağladığı için çok iyi ölçeklenmenin sırrıdır. AlloyDB bu faydayı PostgreSQL dünyasına taşır: hem okumada hem yazmada yüksek performansı korurken ölçeklenir.

Performans rakamları çarpıcı ve akılda tutulması gereken türden:

- **Transactional iş yüklerinde standart PostgreSQL'den 4 kat daha hızlı.**
- **Columnar Engine (sütun-yönelimli motor)** sayesinde **analitik sorgularda standart PostgreSQL'den 100 kata kadar daha hızlı.**
- Bu analitik hızlanma, transactional performansa **sıfır olumsuz etkiyle** gelir — yani hem işlem hem analiz aynı sistemde iyi çalışır. Bu, **BI/raporlama** ve özellikle **hibrit transactional-analitik işleme (HTAP — Hybrid Transactional/Analytical Processing)** iş yükleri için idealdir.

Ayrıca AlloyDB:

- **Tam PostgreSQL uyumluluğu** sunar — mevcut PostgreSQL uygulamaların çalışır.
- **Otomatik ölçekleme ile yüksek kullanılabilirlik** sağlar.
- Replikasyon, yük devretme ve yedeklemeyi **Google yönetir** (Cloud SQL'de olduğu gibi).
- **AlloyDB Auth Proxy** ile güvenli erişim verir — bu, Cloud SQL Auth Proxy'ye benzer şekilde çalışır (yerel proxy → güvenli tünel → sunucu).

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- Aynı anda **yüksek performans hem de PostgreSQL uyumluluğu** isteyen uygulamalar.
- **Transactional ve analitik işlemenin karışımını (mix / HTAP)** yapmak isteyen uygulamalar — hem OLTP hem OLAP'ı tek sistemde birleştirmek.
- Standart PostgreSQL'in performansına sıkışmış, ama motoru değiştirmek istemeyen ekipler.

**İdeal değil:** Basit, küçük ölçekli bir uygulama için Cloud SQL zaten yeterliyken AlloyDB'ye geçmek gereksiz karmaşıklıktır. Ayrıca MySQL/SQL Server dünyasındaysan AlloyDB seni ilgilendirmez — o **yalnızca PostgreSQL** içindir.

> **Sınav tuzağı — Cloud SQL vs AlloyDB vs Spanner:** Üçü de "yönetilen ilişkisel veritabanı" olduğu için birbirine karışır. Ayrımı şöyle kur:
> - **Cloud SQL** = genel amaçlı yönetilen ilişkisel DB; MySQL, PostgreSQL **veya** SQL Server; klasik web app/OLTP; minimal refactor ile göç.
> - **AlloyDB** = **yalnızca PostgreSQL** uyumlu, ama yüksek performanslı; 4x transactional, 100x analitik (Columnar Engine); HTAP (transactional + analitik mix) için.
> - **Spanner** = küresel ölçekte **yatay ölçeklenen**, güçlü tutarlı, %99.999 SLA'lı ilişkisel DB; mission-critical devasa OLTP için.
> Soruda "PostgreSQL + yüksek performans + analitik karışımı" → AlloyDB. "SQL Server / basit MySQL göçü" → Cloud SQL. "küresel, horizontal scale, en yüksek SLA" → Spanner.

---

# Küresel Ölçek + Güçlü Tutarlılık: Spanner

## Neden var?

İlişkisel veritabanlarının klasik açmazı şudur: **Ya güçlü tutarlılık (strong consistency) alırsın ama tek bölgeye sıkışırsın, ya da yatay ölçeklersin ama tutarlılıktan ödün verirsin.** Uzun yıllar boyunca "hem küresel ölçekte yatay ölçeklenen, hem de güçlü tutarlı, hem de ilişkisel" bir veritabanı imkânsız sayıldı. Google **Spanner** ile bu ikilemi kırdı: **hem güçlü tutarlılık hem de yatay ölçeklenebilirlik (horizontal scalability)** sunan, tam yönetilen bir ilişkisel veritabanı. Mission-critical (kritik görev) OLTP uygulamaları için tasarlandı.

## Nasıl çalışır?

Spanner'ı özel kılan birkaç nokta:

- **Yatay ölçeklenir** — veriyi ve yükü otomatik olarak birçok makineye dağıtır; tek makine sınırına takılmazsın.
- **Güçlü tutarlılık (strong consistency)** — küresel ölçekte bile, bir yazma tamamlandığında tüm okumalar güncel veriyi görür. "Eventual consistency" (sonunda tutarlılık) değil, gerçek güçlü tutarlılık.
- **Otomatik, senkron replikasyon (synchronous replication)** ile yüksek kullanılabilirlik sağlar.
- **Çok bölgeli (multi-region) replikasyon** destekler ve endüstrinin en yüksek SLA'larından birini sunar: **%99.999 kullanılabilirlik** (yılda yaklaşık beş dakikadan az kesinti).

> **İlk modülle bağ:** İlk modülde "multi-region" kavramını anlatırken tam olarak Spanner'ı örnek vermiştik: çoklu bölge yapılandırması veritabanını birden fazla bölgedeki birden fazla zona kopyalar, böylece yakın konumlardan düşük gecikmeyle okursun. Ayrıca %99.999 SLA rakamı da o modülde Spanner'ın imzasıydı. Bu modül o bilgiyi tamamlıyor.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **İlişkisel, yapılandırılmış ve yarı-yapılandırılmış (semi-structured) veri.**
- Aynı anda **yüksek kullanılabilirlik, güçlü tutarlılık ve transactional okuma/yazma** gerektiren uygulamalar.
- Küresel ölçekte çalışan, kesinti kabul etmeyen mission-critical sistemler — finans, envanter, küresel kullanıcı hesapları.

**İdeal değil:** Küçük, tek bölgeli, mütevazı bir uygulama için Spanner aşırıya kaçmaktır (over-engineering) — orada Cloud SQL daha uygun ve ekonomiktir. Spanner'ın gücü ancak küresel ölçek ve katı tutarlılık gereksinimi varsa anlam kazanır.

> **Sınav tuzağı — Strong vs Eventual Consistency:** "Strong consistency" (güçlü tutarlılık) = yazmadan hemen sonra herkes güncel değeri görür. "Eventual consistency" (nihai tutarlılık) = değişiklik yayılana kadar bazı okuyucular eski değeri görebilir. Soruda "strong consistency + horizontal scale + relational + global" hepsi bir aradaysa cevap neredeyse kesin **Spanner**'dır — bu üçlüyü aynı anda sunan başka servis yok. Ayrıca **Cloud SQL vs Spanner** ayrımında anahtar kelime "horizontal scalability" ve "%99.999 SLA"dır; bunları görürsen Cloud SQL değil, Spanner.

---

# Analitik Veri Ambarı: BigQuery

## Neden var?

İş zekası (business intelligence) dünyasının ihtiyacı, canlı uygulamanınkinden temelde farklıdır. Burada amaç, tek bir kaydı milisaniyede getirmek değil; **devasa veri kümeleri üzerinde karmaşık analitik sorgular çalıştırıp** eğilimleri, toplamları, raporları çıkarmaktır. Bu OLAP dünyasıdır. **BigQuery**, tam bu iş için tasarlanmış **tam yönetilen, serverless bir kurumsal veri ambarıdır (enterprise data warehouse).**

## Nasıl çalışır?

BigQuery'nin belirleyici özellikleri:

- **Serverless** — sunucu, küme veya kapasite yönetmezsin; sorguyu yazarsın, BigQuery çalıştırır.
- **Muazzam tarama hızı** — **saniyeler içinde terabaytlarca**, **dakikalar içinde petabaytlarca** veriyi tarar. Bu ölçek, geleneksel veritabanlarının erişemeyeceği bir analitik gücüdür.
- **Yerleşik gelişmiş yetenekler:** doğrudan SQL ile **makine öğrenmesi (BigQuery ML)**, **jeo-uzamsal (geospatial) analiz** ve **iş zekası** araçlarıyla entegrasyon.

Yani BigQuery'yi, "SQL bilen herkesin petabayt ölçeğinde analiz yapabildiği, altyapıyla hiç uğraşmadığı" bir ambar olarak düşünebilirsin.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **OLAP (Online Analytical Processing)** iş yükleri.
- **Büyük veri keşfi ve işleme** — devasa veri kümelerinde analiz.
- **BI araçlarıyla raporlama** — dashboard'lar, iş zekası raporları.

**İdeal değil:** Canlı bir uygulamanın milisaniyelik tekil okuma/yazma ihtiyacı (OLTP). BigQuery bir işlem veritabanı değildir; tek satırlık "sipariş oluştur, anında oku" senaryosu için yanlış araçtır — orası Cloud SQL/Spanner/Bigtable'ın alanıdır.

> **Sınav tuzağı — tekrar Bigtable vs BigQuery:** Yeniden vurgulamaya değer çünkü en sık tuzak budur. **BigQuery** analitik/OLAP/data warehouse; **Bigtable** operasyonel/NoSQL/sub-10ms key-value. İsimleri benziyor, işleri zıt. "Data warehouse", "analytics", "reporting", "scan petabytes with SQL" → **BigQuery**. "high-throughput writes", "sub-10ms lookups", "single-keyed operational data" → **Bigtable**.

---

# Bellek İçi Önbellek: Memorystore

## Neden var?

İkinci modülde önbelleklemenin (caching) neden önemli olduğunu görmüştük: **sık erişilen** ya da **her seferinde hesaplaması pahalı** veriyi bellekte tutup anında dönmek, uygulama performansını artırır ve gecikmeyi düşürür. Ama bir önbellek altyapısını (Redis/Memcached kümesini) kendin kurup yönetmek — provisioning, replikasyon, yük devretme, yama (patching) — ciddi operasyonel yüktür. **Memorystore**, tam bu yükü kaldıran servistir: **Redis veya Memcached kullanarak yüksek performanslı, tam yönetilen, bellek içi (in-memory) veri deposu.**

> **İlk modülle bağ:** İkinci modülde "Memorystore = uygulama verisi önbelleği (Redis/Memcached), Cloud CDN = web/statik içerik önbelleği" ayrımını yapmıştık. İşte Memorystore'un ayrıntısı burada. Önbellek akışını da hatırla: önce önbelleğe bak; varsa oradan dön (hızlı), yoksa arka uçtan getir ve önbelleği güncelle.

## Nasıl çalışır?

- **İki açık kaynak motorunu da** destekler: **Redis** ve **Memcached**; her biriyle **tam protokol uyumludur.** Yani mevcut Redis/Memcached uygulamanı değiştirmeden bağlayabilirsin.
- **Tam yönetilen:** provisioning, replikasyon, yük devretme (failover) ve yama (patching) **otomatik** yapılır — sen küme yönetmezsin.
- **İzleme ve alarm:** **Cloud Monitoring** ile entegre; performansı izler, alarm kurarsın.
- **Güvenlik:** **VPC ağları ve dahili IP (internal IP)** üzerinden çalışır; böylece internetten korunur. Ayrıca **IAM ile entegredir** — erişimi kimlik ve rollerle kontrol edersin.

> **İlk modülle bağ:** VPC, dahili IP ve IAM kavramları ilk modülden tanıdık. Memorystore'un internetten izole, VPC içinde ve IAM ile korumalı olması, o modüldeki ağ ve güvenlik ilkelerinin doğrudan uygulamasıdır.

## Ne zaman kullanılır (ve kullanılmaz)?

**İdeal:**

- **Ölçeklenebilir web uygulamaları** — oturum durumu, sık erişilen veri önbelleği.
- **Oyun (gaming)** — liderlik tabloları, gerçek zamanlı oyun durumu.
- **Akış işleme (stream processing)** — dağıtık bellek içi veri deposunun hızlı gerçek zamanlı erişim/işleme sağladığı senaryolar.

**İdeal değil:** Kalıcı olması, kaybolmaması hayati olan birincil (source of truth) veriyi yalnızca Memorystore'da tutmak. Önbellek doğası gereği geçicidir; asıl veri kalıcı bir veritabanında (Cloud SQL, Firestore, Spanner...) durmalı, Memorystore o verinin hızlı erişilen kopyasını taşımalıdır.

> **Sınav tuzağı — Redis vs Memcached:** Memorystore her ikisini de destekler; hangisi ne zaman? Kabaca: **Memcached** basit, çok basamaklı (multi-node) key-value önbelleği için; hafif ve saf caching. **Redis** ise daha zengin veri yapıları (liste, set, sorted set), kalıcılık seçenekleri ve replikasyon isteyen senaryolar için. Sınav açısından asıl bilmen gereken: Memorystore **ikisini de tam protokol uyumuyla** sunar, yani mevcut Redis veya Memcached uygulamanı kod değiştirmeden taşıyabilirsin.

---

# Hangi Servisi Ne Zaman Seçerim? (Karar Bölümü)

Şimdi tüm bu servisleri bir arada, **karar verme** gözüyle toparlayalım. Doğru seçim için bir ürünün tasarımı gereği **neye ideal olduğunu VE neye ideal olmadığını** birlikte düşünmen gerekir. Seçimde kullanacağın üç ana ölçüt (modülün başında saydıklarımız):

1. **Okuma/yazma gecikmesi** — sub-10ms mı gerekiyor, yoksa saniyeler kabul mü?
2. **Verinin tipik boyutu** — küçük kayıt mı, gigabaytlık nesne mi, petabaytlık tablo mu?
3. **Depolama tipi** — unstructured blob, doküman, key-value, ilişkisel tablo, analitik ambar?

Ve iki altın kural:

- **Tek bir veritabanıyla sınırlı değilsin.** Her kullanım senaryosu için en uygun servisi seç; aynı uygulamada birkaçını birlikte kullan.
- **Boyut limitleri veritabanı başınadır.** Bir limite dayanırsan veriyi birden çok veritabanına bölerek (partition) aşabilirsin.

## Karşılaştırma tablosu

| Servis | Kategori / Tip | İdeal senaryo | İdeal DEĞİL | Anahtar kelimeler |
| --- | --- | --- | --- | --- |
| **Cloud Storage** | Nesne depolama (unstructured) | Statik site, resim/video/dosya, kullanıcı yüklemeleri, arşiv/yedek | Sorgulanan/ilişkili yapılandırılmış veri | unstructured, blob, object, 5 TB, HTTP |
| **Firestore** | NoSQL doküman DB | Mobil/web app, esnek veri, gerçek zamanlı + offline, harici kullanıcı hesapları | Petabayt analitik; karmaşık JOIN'ler | document, collection, mobile/web, real-time, offline |
| **Bigtable** | NoSQL key-value | Yüksek verimli operasyonel/analitik; single-keyed veri; MapReduce; kullanıcı davranışı | İlişkisel sorgu, transaction, ad-hoc SQL | sub-10ms, billions of rows, petabytes, HBase, high-throughput |
| **Cloud SQL** | Yönetilen ilişkisel | Web app, yapılandırılmış veri, OLTP; MySQL/PostgreSQL/SQL Server göçü | Küresel horizontal scale; ağır analitik | OLTP, MySQL/PostgreSQL/SQL Server, read replica, minimal refactor |
| **AlloyDB** | Yüksek performanslı PostgreSQL | PostgreSQL + yüksek performans; transactional + analitik mix (HTAP) | Basit küçük app; MySQL/SQL Server | PostgreSQL, 4x transactional, 100x analytics, Columnar Engine, HTAP |
| **Spanner** | Küresel ilişkisel | Mission-critical OLTP; küresel, güçlü tutarlılık + horizontal scale; %99.999 SLA | Küçük tek-bölge app (over-engineering) | strong consistency, horizontal scale, multi-region, 99.999% |
| **BigQuery** | Serverless veri ambarı | OLAP, büyük veri keşfi/işleme, BI raporlama; SQL ile ML/geospatial | Canlı OLTP tekil okuma/yazma | data warehouse, OLAP, analytics, serverless, terabytes/petabytes |
| **Memorystore** | Bellek içi önbellek | Ölçeklenebilir web app, gaming, stream processing önbelleği | Kalıcı birincil veri (source of truth) | cache, in-memory, Redis, Memcached, VPC/internal IP |

## Karar için pratik düşünce zinciri

Bir veri parçasıyla karşılaştığında kendine şu soruları sırayla sor:

- **Yapılandırılmamış büyük bir dosya mı** (resim, video, blob)? → **Cloud Storage.**
- **Sadece hızlı bellek erişimi için geçici önbellek mi** istiyorum? → **Memorystore.**
- **Analiz/raporlama için biriktirilen devasa veri mi** (OLAP)? → **BigQuery.**
- **Yüksek verimli, sub-10ms, single-keyed operasyonel veri mi**? → **Bigtable.**
- **Mobil/web için esnek, hiyerarşik, gerçek zamanlı doküman verisi mi**? → **Firestore.**
- **Yapılandırılmış ilişkisel + transaction (OLTP) mı**?
  - Klasik, tek bölge, MySQL/PostgreSQL/SQL Server → **Cloud SQL.**
  - PostgreSQL + yüksek performans + analitik karışımı (HTAP) → **AlloyDB.**
  - Küresel, güçlü tutarlılık + horizontal scale + en yüksek SLA → **Spanner.**

---

# Toplu Özet (Hızlı Tekrar)

Modülün tamamını bir arada görelim.

**Temel felsefe:** "Tek beden herkese uymaz." Depolama/veritabanı seçimi iş yüküne bağlıdır. Tek DB'yle sınırlı değilsin; her senaryoya en uygun servisi seç. Boyut limitleri DB başınadır; veriyi bölerek aşarsın. Seçim ölçütleri: read/write latency, veri boyutu, storage tipi.

**Bir uygulamanın veri türleri:** resim/dosya (unstructured), yüksek hacimli kullanıcı mesajı, transactional veri, cache, iş zekası verisi. Her biri için ayrı yönetilen servis var.

**Cloud Storage:** Birleşik nesne depolama; unstructured byte'lar; anahtar = nesne adı; HTTP erişimi (ranged GET ile parça); nesne 5 TB'a kadar; availability/durability/scalability/consistency. İdeal: statik site, resim/video/blob, kullanıcı yüklemeleri, arşiv.

**Firestore:** Serverless NoSQL doküman DB; collection/document hiyerarşisi, nested object + subcollection; strong consistency; gerçek zamanlı + offline; mobil/web client library. İdeal: mobil/web app, esnek veri, harici kullanıcı hesapları.

**Bigtable:** Yüksek performanslı NoSQL; sparse tablo, milyarlarca satır/binlerce sütun, TB-PB; sub-10ms key-value lookup; seamless scaling (downtime yok); HBase API. İdeal: operasyonel+analitik, single-keyed veri, MapReduce, kullanıcı davranışı.

**Cloud SQL:** Yönetilen ilişkisel (MySQL/PostgreSQL/SQL Server); Google replikasyon/failover/backup yönetir; primary → read replica; Cloud SQL Auth Proxy ile güvenli erişim (IP/SSL uğraşı yok). İdeal: web framework, yapılandırılmış veri, OLTP, minimal refactor göçü.

**AlloyDB:** Yüksek performanslı, PostgreSQL uyumlu; compute-storage ayrımı (Bigtable/Spanner gibi) → iyi ölçeklenir; transactional 4x, analitik 100x (Columnar Engine); HTAP; AlloyDB Auth Proxy. İdeal: PostgreSQL + yüksek performans + transactional/analitik mix.

**Spanner:** Yönetilen ilişkisel; strong consistency + horizontal scalability birlikte; senkron replikasyon; multi-region; %99.999 SLA. İdeal: mission-critical OLTP, küresel, güçlü tutarlılık + transactional read/write.

**BigQuery:** Serverless veri ambarı; saniyede TB, dakikada PB tarar; yerleşik ML/geospatial/BI. İdeal: OLAP, büyük veri keşfi/işleme, BI raporlama.

**Memorystore:** Tam yönetilen bellek içi; Redis + Memcached (tam protokol uyumu); provisioning/replication/failover/patching otomatik; Cloud Monitoring; VPC/internal IP + IAM. İdeal: ölçeklenebilir web app, gaming, stream processing önbelleği.

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Unstructured / blob / static site / user uploads** → **Cloud Storage.** Anahtar = nesne adı; içerik opak byte; nesne 5 TB'a kadar; ranged GET ile parça alınır.
- **Firestore vs Bigtable:** Firestore = doküman modeli, mobil/web, gerçek zamanlı + offline, esnek/hiyerarşik veri. Bigtable = milyarlarca satır, sub-10ms key-value, high-throughput, single-keyed, HBase API. "mobile/web + real-time + flexible" → Firestore; "billions of rows + sub-10ms + petabytes" → Bigtable.
- **Bigtable vs BigQuery (isim tuzağı!):** Bigtable = operasyonel NoSQL, sub-10ms, canlı uygulama. BigQuery = analitik veri ambarı, OLAP, SQL ile petabayt tarama. İsimleri benzer, işleri zıt.
- **Cloud SQL vs AlloyDB vs Spanner:** Cloud SQL = genel yönetilen ilişkisel (MySQL/PostgreSQL/SQL Server), OLTP, minimal refactor. AlloyDB = yalnızca PostgreSQL, yüksek performans (4x/100x), HTAP. Spanner = küresel, horizontal scale + strong consistency, %99.999 SLA, mission-critical.
- **OLTP vs OLAP:** OLTP = çok sayıda küçük hızlı transaction (Cloud SQL, AlloyDB, Spanner). OLAP = büyük veri analitiği/raporlama (BigQuery).
- **Strong vs eventual consistency:** Strong = yazmadan sonra herkes güncel değeri görür (Spanner, Firestore storage layer). Eventual = değişiklik yayılana kadar eski değer görülebilir. "strong consistency + horizontal scale + relational + global" → Spanner.
- **Cloud SQL Auth Proxy / AlloyDB Auth Proxy:** Yerel proxy istemcisi standart DB protokolüyle konuşur, sunucudaki eşle güvenli tünel kurar; izin verilen IP/SSL yapılandırmadan güvenli erişim.
- **Memorystore Redis vs Memcached:** İkisini de tam protokol uyumuyla destekler; Memcached = saf/basit caching, Redis = zengin veri yapıları + kalıcılık + replikasyon. VPC/internal IP + IAM ile korunur; birincil kalıcı veri deposu değildir.
- **AlloyDB compute-storage ayrımı:** Bigtable ve Spanner de aynı ayrımı kullanır; bu ayrım iyi ölçeklenmenin sırrıdır.
- **Seçim ölçütleri:** read/write latency, veri boyutu, storage tipi. Tek DB'yle sınırlı değilsin; limitler DB başına, veriyi bölerek aşarsın.

---

> **Kapanış:** Bu modül, "buluta veri nasıl konur" sorusunu bir mühendislik kararına dönüştürdü. Artık elinde her veri türü için tasarlanmış bir servis var: unstructured için Cloud Storage, esnek doküman için Firestore, yüksek hacimli key-value için Bigtable, ilişkisel OLTP için Cloud SQL, yüksek performanslı PostgreSQL/HTAP için AlloyDB, küresel güçlü-tutarlı OLTP için Spanner, analitik OLAP için BigQuery ve önbellek için Memorystore. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; özellikle isim benzerliği taşıyan (Bigtable/BigQuery) ve aynı aileden gelen (Cloud SQL/AlloyDB/Spanner) servisleri karıştırmadığından emin ol. Bir servide takılırsan ilgili ana bölüme dön ve "neye ideal, neye ideal değil" sorusunu yeniden kur. Sıradaki modüle geçmeye hazırsın.
