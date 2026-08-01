# Google Cloud ile Uygulama Geliştirme: Temeller — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun ilk iki modülünde anlatılan **her şeyi** kavratmak için yazıldı. Kaynak transkript iki parçadan oluşuyor: (1) **Bulut uygulama geliştirme en iyi uygulamaları** ve (2) **Google Cloud geliştirmeye başlarken kullandığın araçlar**. İlk modül (Core Infrastructure) altyapıyı tanıttı; bu modül ise o altyapının üzerinde **uygulama yazan bir geliştirici** olarak neyi neden yapman gerektiğini öğretiyor. Acele etme; her kavramı "neden var, nasıl çalışır, ne zaman kullanılır" ekseninde sindirerek oku. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül neyi öğretiyor ve neden önemli?

İlk modülde Google Cloud'un yapı taşlarını — VM'ler, ağlar, depolama, IAM — tanıdın. Ama bir geliştirici için asıl soru şudur: "Bu araçları kullanarak **iyi** bir bulut uygulaması nasıl yazarım?" Çünkü buluta bir uygulama koymak, onu otomatik olarak ölçeklenebilir, dayanıklı ve güvenli yapmaz. Uygulamanı **bulutun doğasına uygun** tasarlaman gerekir; aksi halde bulutun sunduğu esneklik, elastiklik ve güvenilirlikten faydalanamazsın.

Bu öğretici iki büyük parçaya ayrılıyor:

1. **En iyi uygulamalar (best practices):** Kodunu nasıl yönetmelisin, uygulamanı nasıl parçalamalısın, hataya nasıl dayanıklı olmalısın, nasıl ölçeklenmelisin, nasıl dağıtım yapmalısın? Bunlar mimari düzeyinde kararlardır ve sertifikada "hangi yaklaşım doğru" biçiminde sık sorulur.
2. **Geliştirme araçları:** Google Cloud servislerine kodundan nasıl erişirsin? Cloud API'ler, SDK, gcloud CLI, Cloud Client Libraries, Cloud Shell, Cloud Code, yerel emülatörler ve Cloud Workstations.

Başlayalım.

---

# PARÇA 1 — Bulut Uygulama Geliştirme En İyi Uygulamaları

## Bulutta çalışan bir uygulamanın karşılaştığı üç gerçek

En iyi uygulamalara geçmeden önce, **neden** bu kadar özen gerektiğini anlamalısın. Bulutta çalışan bir uygulama, geleneksel bir sunucudaki uygulamadan farklı üç gerçekle yüzleşir:

**1. Küresel erişim (global reach).** Uygulaman dünyanın her yerinden kullanıcıya hizmet vermek zorunda kalabilir. Bu yüzden **duyarlı (responsive)** ve her yerden **erişilebilir** olmalıdır. Tokyo'daki bir kullanıcı ile Londra'daki bir kullanıcı benzer bir deneyim yaşamalı.

**2. Ölçeklenebilirlik ve yüksek kullanılabilirlik (scalability & high availability).** Uygulaman yüksek trafik hacmini **güvenilir biçimde** karşılayabilmeli. Mimarin, altta yatan bulut platformunun yeteneklerini kullanarak yüke göre **elastik biçimde** ölçeklenmeli — yani yük artınca büyümeli, azalınca küçülmeli.

**3. Güvenlik (security).** Hem uygulaman hem de altta yatan altyapı güvenlik en iyi uygulamalarını hayata geçirmeli. Kullanım senaryona göre, güvenlik ve uyumluluk (compliance) nedeniyle kullanıcı verisini **belirli bir bölgede izole** etmen gerekebilir. (Buna "veri ikametgâhı" / data residency denir; örneğin AB kullanıcı verisinin AB içinde kalması gibi.)

> **Neden bu üçlüyü akılda tut?** Çünkü aşağıdaki her en iyi uygulama, bu üç gerçekten en az birine hizmet eder. Bir tekniği okuduğunda kendine sor: "Bu, küresel erişime mi, ölçeklenebilirliğe mi, yoksa güvenliğe mi katkı sağlıyor?"

## Kod ve ortam yönetimi

Uygulamanı yazmaya başlamadan önce, kodunu ve yapılandırmasını nasıl organize edeceğine dair üç temel kural var. Bunlar basit görünür ama ihmal edilirse ilerde büyük acı verir.

### 1. Kodunu bir sürüm kontrol sisteminde sakla

Kodunu **Git gibi bir sürüm kontrol sistemindeki** bir kod deposunda (repository) tut. Neden? Çünkü bir kod deposu:

- Kaynak kodundaki değişiklikleri **izlemeni** sağlar (kim, ne zaman, neyi değiştirdi).
- **Sürekli entegrasyon ve teslimat (CI/CD)** sistemleri kurmanın önkoşuludur. Kodun bir depoda olmadan otomatik derleme/test/dağıtım kuramazsın.

### 2. Harici bağımlılıkları kod deposunda saklama

JAR dosyaları ya da harici paketler gibi **harici bağımlılıkları** kod deposuna koyma. Bunun yerine, uygulama platformuna göre bağımlılıklarını **sürümleriyle birlikte açıkça bildir** ve bir **bağımlılık yöneticisi (dependency manager)** ile kur.

Örnek: Bir Node.js uygulamasında bağımlılıklarını `package.json` dosyasında bildirir, sonra `npm install` komutuyla kurarsın. Java'da Maven/Gradle, Python'da pip/requirements aynı işi görür.

> **Neden?** Bağımlılıkları depoya gömersen depo şişer, sürüm çakışmaları yönetilemez hale gelir ve "hangi sürümü kullanıyoruz" sorusu belirsizleşir. Bildirim + yönetici yaklaşımı, her ortamda **tam olarak aynı** bağımlılıkların kurulmasını garanti eder.

### 3. Yapılandırmayı koddan ayır

Yapılandırma ayarlarını (config settings) kaynak kodunda **sabit (constant) olarak saklama.** Bunun yerine **ortam değişkenleri (environment variables)** olarak belirt.

Neden bu kadar önemli? Çünkü ortam değişkenleriyle yapılandırma geçirmek, ayarları **geliştirme, test ve üretim** ortamları arasında değiştirmene izin verir. Böylece **test edilen kodun aynısı üretimde** çalışır — sadece ayarlar değişir, kod değişmez. Veritabanı adresini ya da API anahtarını koda gömersen, her ortam için kodu değiştirmen gerekir; bu da "test ettiğim kod ile ürettiğim kod aynı mı?" belirsizliğini doğurur.

> **Sınav tuzağı:** "Config nerede tutulmalı?" → Kaynak kodda sabit olarak **değil**, ortam değişkeni olarak. Bu, "twelve-factor app" felsefesinin de temel taşıdır.

## Monolit mi, mikroservis mi?

Bir uygulamayı tek bir büyük parça (monolith) olarak mı yoksa birçok küçük bağımsız servis (microservices) olarak mı kurmalısın? Transkript bu kararı önemle işliyor, çünkü mimarinin geri kalanını belirler.

**Monolitin sorunları.** Çoğu uygulama zamanla önemli ölçüde değişir. Monolitte:

- Kod tabanı **şişer (bloated)**.
- Yeni bir özellik eklerken **hangi kodun değişmesi gerektiğini** belirlemek zorlaşır.
- Bileşenlerin bağımlılıkları **birbirine dolanır (tangled)**.
- Kodun küçük bir kısmı değişse bile **tüm uygulama** dağıtılıp test edilmek zorundadır.
- Sonuç: Özellik değişiklikleri ve hata düzeltmeleri **daha çok efor ve daha çok risk** demektir.

**Mikroservislerin getirdiği.** Mikroservisler, uygulama bileşenlerini **iş sınırlarına (business boundaries)** göre yeniden yapılandırmana izin verir. Her mikroservis:

- **Modülerdir** — nerede değişiklik yapılacağı bellidir.
- **Bağımsız güncellenir, test edilir ve dağıtılır** — müşterilerinin (onu kullanan diğer servislerin) aynı anda değişmesi gerekmez.
- **Bağımsız ölçeklenir** — yüke göre yalnızca ilgili servis büyür.

**Ama bedeli var.** Bir monoliti mikroservislere ayırmak (refactor) ciddi zaman ve emek gerektirebilir. Bu yüzden transkript net bir uyarı yapar: **Optimizasyonun maliyeti ile faydasını değerlendir.** Her uygulama mikroservise dönüştürülmeye değmez; kararı körlemesine "mikroservis daha modern" diye verme.

> **Sınav tuzağı:** Mikroservis her zaman "daha iyi" değildir. Doğru cevap genelde "maliyet/fayda analizini yap" yönündedir. Mikroservisin faydası bağımsız geliştirme/ölçekleme; bedeli operasyonel karmaşıklık.

## Asenkron ve olay güdümlü işleme

Uzak işlemler (remote operations) öngörülemeyen yanıt süreleri üretebilir ve uygulamanı yavaş gösterebilir. Bu yüzden temel kural:

**Kullanıcı iş parçacığında (user thread) olan işlemleri minimumda tut.** Yani kullanıcı bir istek yaptığında, onu bekletecek ağır işleri **arka planda asenkron** yap. Mümkün olduğunca **olay güdümlü (event-driven)** işleme kullan.

Somut örnek: Uygulaman yüklenen resimleri işliyorsa, yüklenen resimleri bir **Cloud Storage** bucket'ında sakla. Sonra **Cloud Run**'da, yeni bir resim yüklendiğinde **tetiklenen** bir fonksiyon/servis kur. Bu fonksiyon resmi işler ve sonucu farklı bir Cloud Storage konumuna yükler. Böylece kullanıcı resmi yükler yüklemez yanıt alır; ağır işlem arka planda, olay tetiklemesiyle gerçekleşir.

> **İlk modülle bağ:** Bu tam olarak Cloud Run functions'ın var oluş nedeniydi — olay güdümlü, tek amaçlı, yükleme olunca tetiklenen işleme.

## Gevşek bağlılık (loose coupling)

Uygulama bileşenlerini **çalışma zamanında gevşek bağlı (loosely coupled at runtime)** olacak şekilde tasarla. Neden? Çünkü **sıkı bağlı (tightly coupled)** bileşenler uygulamayı; hatalara, trafik ani yükselmelerine (spike) ve servis değişikliklerine karşı **daha kırılgan** yapar. Bir bileşen çökerse ya da yavaşlarsa, ona sıkı bağlı diğer bileşenler de onunla birlikte batar.

Gevşek bağlılığı nasıl sağlarsın? Bir **olay (event)** veya **mesaj kuyruğu (message queue)** kullanarak. Bu yapılar üç işi birden görür: gevşek bağlılık kurar, asenkron işleme sağlar ve trafik ani yükselirse istekleri **tamponlar (buffer)**. Google Cloud'da:

- **Eventarc tetikleyicisi** → olay kuyruğu olarak.
- **Pub/Sub konusu (topic)** → mesaj kuyruğu olarak.

Örnek: Sipariş servisi ile envanter servisi gevşek bağlıysa, biri diğerini beklemeden bağımsız çalışabilir. Sipariş servisi bir mesaj bırakır, envanter servisi onu kendi hızında işler.

### HTTP API yükünde (payload) gevşek bağlılık

Gevşek bağlılık sadece servisler arasında değil, **API tüketicileri ile yayıncıları** arasında da geçerlidir. HTTP API tüketicileri, API yayıncısına **gevşek bağlanmalıdır.**

Örnek: E-posta servisi, her müşteri hakkındaki bilgiyi müşteri servisinden alıyor. Müşteri servisi yükünde (payload) **isim, yaş ve e-posta** döndürüyor. E-posta göndermek için e-posta servisi yalnızca **isim ve e-posta** alanlarına başvurmalı; yükteki **tüm alanlara bağlanmaya çalışmamalı.**

Neden? Çünkü alanlara bu gevşek bağlanma yöntemi, yayıncının API'yi **geriye dönük uyumlu (backward-compatible)** biçimde geliştirmesine ve yüke yeni alanlar eklemesine izin verir. Tüketici tüm alanlara sıkı bağlanmışsa, yayıncı yüke bir alan eklediğinde tüketici bozulabilir.

> **Sınav tuzağı:** Gevşek bağlılık = dayanıklılık + evrilebilirlik. Payload'da yalnızca ihtiyacın olan alanları kullan; hepsine bağlanma.

## Durumsuzluk (statelessness)

Uygulama bileşenlerini, durumu (state) **içeride saklamayacak ya da paylaşmayacak** şekilde kur. Neden? Çünkü **paylaşılan duruma erişim, ölçeklenebilirlik için yaygın bir darboğazdır (bottleneck).** Bileşenler ortak bir belleğe/duruma bağımlıysa, onları çoğaltmak zorlaşır.

Doğru yaklaşım: Her bileşeni yalnızca **hesaplama görevlerine (compute tasks)** odaklanacak biçimde tasarla. Bu, bir **worker pattern (işçi deseni)** kullanmana olanak tanır — ölçeklenebilirlik için bileşenin ek örneklerini ekler ya da çıkarırsın. Bileşenler durumsuz olduğu için birbirinin yerine geçebilir; herhangi biri herhangi bir isteği işleyebilir.

Ek iki davranış:

- **Hızlı başlat (start up quickly)** — verimli ölçekleme için. Yük artınca yeni örnekler hızla devreye girmeli.
- **Zarifçe kapan (shut down gracefully)** — bir sonlandırma sinyali (termination signal) aldığında, işini temiz bitirip kapanmalı.

Örnek: Trafiğin çok değişkense, uygulaman için **Cloud Run** kullan ve kapasiteyi trafiğe göre ölçekle. Cloud Run servisleri gelen istekleri işler ama **durum saklamaz/paylaşmaz**, bu yüzden trafik azalınca kolayca kapatılabilirler. Peki veri nereye gider? **Ayrı bir veritabanında** kalıcılaştırılır — örneğin **Firestore.**

> **Kilit sezgi:** Durumsuz bileşen + ayrı veritabanı = kolay ölçeklenen mimari. Durum, hesaplamanın içinde değil, dışarıdaki bir veri katmanında yaşamalı.

## Dayanıklılık (resilience): Geçici ve kalıcı hatalar

Dağıtık bir sistemde servislere ve kaynaklara erişirken, uygulaman hem **geçici (transient)** hem de **uzun süreli (long-lasting)** hatalara dayanıklı olmalı. Bu ikisi farklı stratejiler gerektirir; ayrımı iyi öğren.

### Geçici hatalar → yeniden dene + üstel geri çekilme

Kaynaklar bazen geçici ağ hataları yüzünden erişilemez olur. Bu durumda uygulama, **üstel geri çekilme (exponential backoff)** ile **yeniden deneme (retry) mantığı** uygulamalı ve hatalar sürerse **zarifçe başarısız olmalı.**

Üstel geri çekilme nedir? Her başarısız denemeden sonra bekleme süresini katlayarak artırmaktır (örneğin 1s, 2s, 4s, 8s...). Neden? Çünkü hemen ve sürekli yeniden denersen, zaten zorlanan arka ucu ya da ağı **daha da yükleyerek sorunu büyütürsün.** Üstel geri çekilme bunu önler.

İyi haber: **Cloud Client Libraries, başarısız istekleri otomatik olarak yeniden dener.** Yani bu mantığı çoğu zaman elle yazman gerekmez.

### Kalıcı hatalar → devre kesici

Servisler uzun süreli hatalarla çöktüğünde, uygulama **trafik üretmemeli** ya da isteği yeniden denemek için **CPU döngülerini boşa harcamamalı.** Bu durumda bir **devre kesici (circuit breaker)** uygula ve hatayı zarifçe ele al.

Devre kesici mantığı: Bir servis sürekli başarısız oluyorsa, "devreyi aç" ve bir süre ona istek göndermeyi tamamen durdur. Böylece hem kendi kaynaklarını hem de çöken servisi boşuna zorlamaktan kurtulursun. Bir süre sonra "acaba düzeldi mi" diye kontrol edersin.

> **Sınav tuzağı:** *Geçici* hata → retry + exponential backoff. *Kalıcı* hata → circuit breaker. İkisini karıştırma: kalıcı bir hatada durmadan retry yapmak felakettir.

### Zarif bozulma (graceful degradation)

Kullanıcıya kadar ulaşan hatalarda, hata mesajını açıkça göstermek yerine uygulamayı **zarifçe bozmayı (degrade gracefully)** düşün.

Örnek: Öneri motoru (recommendation engine) çökmüşse, her sayfada hata mesajı göstermek yerine **öneri bölümünü gizle.** Kullanıcı, olmayan bir özelliği fark etmez bile; oysa kırmızı hata mesajı deneyimi bozar. Yani "çalışmıyorsa görünmesin" ilkesi, "çalışmıyorsa bağır" ilkesinden iyidir.

## Önbellekleme (caching)

İçeriği önbelleğe almak, uygulama performansını artırır ve ağ gecikmesini düşürür. Neyi önbelleğe almalısın? **Sık erişilen** ya da **her seferinde hesaplaması pahalı** olan uygulama verisini.

Önbellek akışı şöyle işler:

1. Kullanıcı veri istediğinde, bileşen **önce önbelleğe** bakar.
2. Veri önbellekte varsa → **önbellekteki veriyi döndür** (hızlı).
3. Veri önbellekte yoksa ya da süresi dolmuşsa (expired) → arka uç veri kaynağından **getir/yeniden hesapla**, sonucu döndür ve **önbelleği yeni değerle güncelle.**

**Memorystore.** Google Cloud'un, **Redis veya Memcached** kullanarak önbellekleme yapan **tam yönetilen, bellek içi (in-memory)** servisidir. Uygulama verisini önbelleğe almak için bunu kullanırsın.

**Cloud CDN.** Uygulama verisini önbelleğe almanın yanında, **web içeriğini** önbelleğe almak için bir CDN de kullanabilirsin. Cloud CDN, Google'ın **küresel Edge ağını** kullanarak içeriği kullanıcılara daha yakın sunar; böylece web sitelerini ve uygulamalarını hızlandırır. Statik içerik şuralardan sunulabilir:

- Cloud Storage bucket'ları,
- Cloud Run'da çalışan servisler ve fonksiyonlar,
- Compute Engine VM örnek grupları (instance groups).

> **Ayrım:** Memorystore = **uygulama verisi** önbelleği (bellek içi, Redis/Memcached). Cloud CDN = **web/statik içerik** önbelleği (Edge ağı). İkisi farklı katmanlarda çalışır.

## API ağ geçitleri (API gateways)

Arka uç işlevselliğini tüketici uygulamalara sunmak için **API ağ geçitleri** kur. Google Cloud'da bunun platformu **Apigee**'dir — API geliştirme ve yönetme platformu.

Apigee ne yapar? Servislerinin önüne bir **proxy katmanı** koyar; yani arka uç servis API'lerin için bir **cephe (facade)** görevi görür ve şunları sağlar: güvenlik, oran sınırlama (rate limiting), kota, analitik ve daha fazlası.

Özel bir kullanım: **Eski (legacy) uygulamalar.** Buluta taşınamayan ya da yeniden yazılamayan eski uygulamaların varsa, önlerine **API'ler** koymayı düşün. Böylece her tüketici, eski protokollerle ve dağınık arayüzlerle iletişim kurma işlevini kendisi yazmak yerine, bu **modern API'leri** çağırarak arka uçtan bilgi alır. Eski sistem yerinde kalır ama dış dünyaya modern bir yüz gösterir.

## Kimlik yönetimini devretmek (delegating identity)

Kullanıcı yönetimi eforunu, kimlik yönetimini **devrederek** en aza indirebilirsin. Yani kullanıcı kimlik doğrulamasını (authentication) kendin sıfırdan yazmak yerine Google'a ya da Facebook, GitHub gibi harici sağlayıcılara devret.

**Identity Platform.** Kullanıcı kaydı ve girişi için **hazır (drop-in), özelleştirilebilir** bir kimlik doğrulama servisi sunar. Harici sağlayıcıların yanında şu yöntemleri de destekler: e-posta/parola, SAML, OpenID Connect ve çok faktörlü kimlik doğrulama (MFA).

**Firebase Authentication (Identity Platform ile).** Arka uç servisleri, kullanımı kolay SDK'lar ve hazır UI kütüphaneleri sağlayarak kullanıcıları uygulamana doğrular.

Bu **federe kimlik yönetimi (federated identity management)** sayesinde, kullanıcıları doğrulamak için kendi tescilli çözümünü **uygulamak, güvenceye almak ve ölçeklemek zorunda kalmazsın.** Bu, hem efor hem de güvenlik riski açısından büyük kazançtır — kimlik doğrulamayı yanlış yazmak ciddi güvenlik açıkları doğurur.

> **Neden devret?** Kimlik doğrulama "basit görünüp aslında çok zor" alanlardan biridir. Parola saklama, oturum yönetimi, MFA, sosyal giriş... Hepsini doğru ve güvenli yapmak uzmanlık ister. Devretmek, bu yükü uzmana bırakmaktır.

## Logları olay akışı olarak ele almak

Loglarını **olay akışları (event streams)** gibi düşün. Loglar, uygulama çalıştığı sürece sürekli oluşan kesintisiz bir olay akışıdır.

Kritik kural: **Uygulamanda log dosyalarını yönetme.** Bunun yerine, bir olay akışına — örneğin **standart çıktıya (standard out / stdout)** — yaz ve altta yatan altyapının tüm olayları sonraki analiz ve saklama için **toplamasına (collate)** izin ver.

Bu yaklaşımın faydaları:

- **Log tabanlı metrikler (logs-based metrics)** kurabilirsin.
- İstekleri uygulamandaki **farklı servisler boyunca izleyebilirsin (trace).**
- Özellikle **serverless** işlem seçenekleri (Cloud Run gibi) için çok iyi çalışır — çünkü serverless'ta yönetebileceğin kalıcı bir disk/dosya sistemi zaten yoktur; stdout'a yazmak doğal yoldur.

**Google Cloud Observability** ile şunları kurarsın: hata raporlama (error reporting), loglama ve log tabanlı metrikler; ayrıca çok bulutlu (multi-cloud) bir ortamda çalışan uygulamaları izlersin.

> **Sınav tuzağı:** "Serverless'ta log nasıl?" → Dosya yönetme, **stdout'a yaz**, altyapı toplasın. Log dosyası açıp diske yazma alışkanlığı bulut-doğasına aykırıdır.

## CI/CD ve güçlü bir DevOps modeli

Otomasyonlu güçlü bir DevOps modeli için **CI/CD hatları (pipelines)** kur. Otomasyon, sürüm hızını (release velocity) ve güvenilirliği artırır. Sağlam bir CI/CD hattıyla, çok sayıda değişiklik içeren büyük sürümler yapmak yerine değişiklikleri **artımlı (incremental)** test edip yayarsın. Bu yaklaşım: gerileme (regression) riskini düşürür, sorunları hızlı ayıklamana yardım eder ve gerekirse **son kararlı sürüme (last stable build) geri dönmene (rollback)** izin verir.

Şimdi kısaltmaları netleştirelim, çünkü sık karıştırılırlar:

**CI — Continuous Integration (Sürekli Entegrasyon).** Geliştiriciler kendi özellik dallarında (feature branch) yaptıkları değişiklikleri, kod deposundaki **paylaşılan bir dala** entegre eder. Bir güncelleme yapıldığında:

- Uygulama derlemesi (build) **tetiklenir.**
- **Otomatik testler**, yeni güncellemenin mevcut birim (unit) ve entegrasyon testlerini bozmadığını doğrular.

**CD — Continuous Delivery (Sürekli Teslimat).** CI sırasında doğrulanan kod değişikliği, otomatik olarak bir kod deposunda **saklanır.** Bu doğrulanmış kod tabanı, operasyon ekibi tarafından **üretime dağıtılmaya hazırdır.** Yani "üretime hazır" duruma otomatik gelir, ama üretime **çıkış kararı hâlâ insandadır.**

**CD — Continuous Deployment (Sürekli Dağıtım).** Bir adım öteye gider: Doğrulanmış değişiklikleri üretime **otomatik olarak dağıtır.** Yalnızca **başarısız bir test**, değişikliğin üretime çıkmasını engeller. Avantajı: değişiklik ve düzeltmeler üretimde daha hızlı yer alır, otomatik dağıtımlar operasyon ekibinin işini azaltır. Ama dikkat: Üretime çıkışı doğrulayan **manuel bir süreç yoktur.** Bu yüzden uygulaman, testlerin ve CI/CD hattın **çok iyi tasarlanmış** olmalı ki üretimde sorun çıkmasın.

> **Sınav tuzağı — Delivery vs Deployment:** İkisi de "CD" kısaltmasını paylaşır ama farkı üretime **son adımdır.** *Delivery*: üretime hazır hale getirir, çıkışı **insan** onaylar. *Deployment*: testler geçerse **otomatik** üretime çıkar. Tek fark: son dağıtım adımı manuel mi, otomatik mi?

### Google Cloud'da CI/CD araçları

Google Cloud'da bir CI/CD hattı kurduğunda:

- **Cloud Build** depo commit'lerini algılar, bir derleme tetikler ve birim testleri çalıştırır. Derleme sistemi, **Artifact Registry**'de saklanabilen dağıtım yapıtları (deployment artifacts) üretir.
- **Cloud Deploy** derlemelerin test ortamlarına ya da doğrudan üretime dağıtımını otomatik tetikleyebilir. Entegrasyon, güvenlik ve performans testlerini otomatik çalıştırıp başarılı derlemeleri üretime dağıtabilirsin.

Akışı zihninde şöyle kur: **commit → Cloud Build (derle + test) → Artifact Registry (yapıtı sakla) → Cloud Deploy (test/üretime dağıt).**

### Güvenli dağıtım stratejileri

Üretime derleme yayarken, **mavi/yeşil (blue/green)** dağıtımlar ya da **kanarya (canary)** testleri yapmayı düşün. Bu stratejiler, yeni derlemedeki beklenmedik sorunların **çoğu kullanıcıyı etkilememesini** sağlar.

- **Blue/green:** İki özdeş ortam tutarsın — "mavi" (mevcut, canlı) ve "yeşil" (yeni sürüm). Trafiği bir anda maviden yeşile çevirirsin; sorun çıkarsa hızlıca maviye geri dönersin.
- **Canary:** Yeni sürümü önce **küçük bir kullanıcı dilimine** açarsın (kanarya kuşu gibi — tehlikeyi önce o fark eder). Sorun yoksa yaymayı kademeli artırırsın.

### Strangler (boğucu) deseni

Büyük uygulamaları yeniden mimarlerken ya da göç ettirirken (migrate) **strangler deseni** kullanmayı düşün. Mantığı şu:

- Göçün erken evrelerinde, eski (legacy) uygulamanın **küçük bileşenlerini** daha yeni bileşen/servislerle değiştirirsin.
- Zamanla orijinal uygulamanın **daha fazla özelliğini** yeni servislerle **artımlı olarak** değiştirirsin.
- Bir **strangler cephesi (facade)** istekleri alır ve onları ya eski uygulamaya ya da yeni servislere yönlendirir.
- İmplementasyon evrildikçe, eski uygulama yeni servisler tarafından "boğulur (strangled)" ve artık gerekmez.

Bu yaklaşım riski en aza indirir: İş açısından kritik operasyonları etkilemeden, her servis implementasyonundan **öğrenerek** ilerlersin. İsim, **boğucu asma bitkilerinden (strangler vines)** gelir — bu asmalar incir ağacının üst dallarında filizlenip yavaş yavaş ağacı sarar ve sonunda onun yerini alır.

> **Ne zaman strangler?** "Big bang" (her şeyi bir anda değiştir) göçü çok risklidir. Büyük bir legacy sistemi kademeli, parça parça değiştirmen gerektiğinde strangler deseni doğru cevaptır.

---

# PARÇA 2 — Google Cloud Geliştirmeye Başlarken (Araçlar)

En iyi uygulamaları öğrendin; şimdi **pratik soru:** Kodundan Google Cloud servislerine nasıl erişirsin? Google Cloud, uygulamalarını kurabileceğin birçok platform sunar ve uygulamaların bu güçlü servislerden faydalanabilir. Bu parçada bu servislere programatik erişimi öğreneceksin: Cloud API'ler, SDK, Cloud Client Libraries ve Cloud Code.

## Cloud API'ler ve Google Cloud SDK

Google servisleriyle etkileşmek için **Cloud API'ler** ve **Google Cloud SDK** kullanılır.

**Cloud API'ler**, Google Cloud servislerine **programatik arayüzler** sağlar. Bir Google Cloud kaynağını/servisini uygulamanda kullanmak için, karşılık gelen Cloud API'yi **çağırırsın.** Cloud API'ler sayesinde uygulamanda işlem (compute), ağ (networking), depolama (storage) ve makine öğrenmesi gibi güçlü yetenekleri kullanırsın.

Cloud API'ler iki şekilde çağrılabilir:

- **HTTP istekleriyle**, **JSON (JavaScript Object Notation)** yükleri kullanarak.
- **gRPC (Google Remote Procedure Call)** istekleriyle. gRPC, her yerde çalışabilen, **verimli bir ikili (binary) istek yapısı** kullanan açık kaynak bir uzak prosedür çağrısı çerçevesidir. İkili olduğu için JSON/HTTP'ye göre daha performanslıdır.

**Kimlik bilgileri (credentials).** Cloud API'leri çağırmak için çağıran taraf **uygulama kimlik bilgileri** sağlamalıdır. Bu kimlik bilgileri, uygulamanın senin Google Cloud projene ve kaynaklarına erişmesine izin verildiğini doğrulamak için denetlenir. (İlk modüldeki **Service Account'lar** burada devreye girer — uygulaman genelde bir Service Account kimliğiyle çalışır.)

**Google Cloud SDK** iki kategoriden oluşur:

1. **Komut satırı araçları (command-line tools).**
2. **Dile özel Cloud Client Libraries.**

Bu araçların ve kütüphanelerin ikisi de, Google Cloud ile iletişim kurmak için **altta Cloud API'leri kullanır.** Yani API'ler temeldir; CLI ve kütüphaneler onların üzerine kurulu kolaylık katmanlarıdır.

## Google Cloud CLI (gcloud CLI)

**gcloud CLI (Google Cloud Command Line Interface)**, Google Cloud servislerini komut satırından ya da otomatik betiklerden yönetmen için araçlar sunar. Bu araçlar, Cloud API'lerin işlevselliğini **kullanımı kolay bir komut satırı arayüzünde** sağlar. İki güzel şey yaparlar:

- Cloud API çağrılarında **kimlik bilgisi göndermeyi otomatikleştirir** (sen uğraşmazsın).
- Tek bir yaygın görevi tamamlamak için gereken **birden çok Cloud API çağrısını birleştirir.**

gcloud CLI ile Cloud API'lerin izin verdiği görevlerin çoğunu yaparsın — VM yönetmek, uygulama dağıtmak vb.

### gcloud CLI'nin içindeki araçlar

gcloud CLI birçok komut satırı aracı içerir:

- **gcloud** — Google Cloud servisleriyle etkileşir. Örnek: `gcloud compute instances list`, projendeki Compute Engine VM örneklerini listeler.
- **gsutil** — Cloud Storage bucket'larını ve nesnelerini yönetir.
- **bq** — BigQuery'de sorgu çalıştırır ve veri yönetir.

Diğer araçlar Kubernetes'i yönetmek, Firestore veya Pub/Sub gibi servisleri emüle etmek ya da **Terraform** ile altyapı oluşturmak için kullanılır.

### Bileşen (component) yönetimi

gcloud CLI, kendi araçlarını da yönetmeni sağlar:

- `gcloud components list` — her CLI bileşenini açıklar. Her bileşen şu durumlardan biriyle listelenir: **kurulu değil (not installed)**, **güncelleme mevcut (update available)** ya da **en güncel sürümde kurulu (installed at latest version).**
- `gcloud components install <bileşen>` — bir bileşeni mevcut CLI sürümünde kurar. Örnek: Kubernetes küme yöneticisi kubectl'i kurmak için `gcloud components install kubectl`.
- `gcloud components update` — Google Cloud CLI sürümünü günceller.

### gsutil, bq ve gcloud storage

Cloud Storage; güvenilir, güvenli ve yüksek performanslı nesne depolama sunar. Onu yönetmek için:

- **gsutil** komutu bucket ve nesne oluşturup yönetebilir.
- Ama artık **gcloud storage**, Cloud Storage'ı yönetmek için **tercih edilen (preferred)** komut satırı aracıdır. `gcloud storage`, gsutil'den **daha iyi performans** gösterir ve kullanımı diğer gcloud komutlarına benzer.
  - `gcloud storage buckets` — bucket'ları oluşturur, listeler, siler ve erişim kontrol listelerini (ACL) yönetir.
  - `gcloud storage objects` — nesneleri ve ACL'lerini yönetir.
  - `gcloud storage` komutları nesneleri kopyalar, taşır, listeler ve siler. Örnek: yerel makinendeki bir dosyayı bir Cloud Storage bucket'ına kopyalar.

**bq**, BigQuery için komut satırı aracıdır — BigQuery, Google Cloud'un **serverless, yüksek ölçeklenebilir ve uygun maliyetli veri ambarıdır (data warehouse).** bq; veri kümelerini (dataset), tabloları ve diğer BigQuery varlıklarını yönetebilir, ama **birincil amacı sorgu çalıştırmaktır.**

> **Sınav tuzağı:** Cloud Storage için modern/tercih edilen araç **gcloud storage**'dır (gsutil değil). gsutil hâlâ çalışır ama gcloud storage daha performanslıdır ve gcloud komut stiliyle uyumludur.

## Cloud Client Libraries

Doğrudan API çağrıları yapmak yerine bir **Cloud Client Library** kullanmak daha kolaydır ve uygulamalarından Google Cloud kaynaklarına erişmenin **önerilen (recommended)** yöntemidir.

Neden önerilir? Çünkü Cloud Client Libraries:

- Sunucuyla **düşük seviyeli iletişimi** (kimlik doğrulama dahil) senin yerine yönetir — optimize edilmiş bir geliştirici deneyimi sunar.
- **Geçici ağ hataları** için yeniden deneme (retry) mantığı sağlar (dayanıklılık bölümünde bahsettiğimiz otomatik retry burada geliyor).
- Seçtiğin dilin **doğal kurallarını ve stilini** kullanır — yani Python'da Python gibi, Java'da Java gibi hissettirir.
- Birçoğu, **gRPC Cloud API'lerini otomatik çağırarak** performans avantajı sağlar.

Desteklenen diller: **Python, Node.js, Java, Go, PHP, Ruby, C++** ve **.NET dilleri (C# dahil).** Uygulaman bu dillerden birini kullanıyorsa, muhtemelen karşılık gelen Cloud Client Library'yi kullanmak isteyeceksin.

Örnek akış (Python ile bir Cloud Storage bucket'ı oluşturmak): Her paket, bir API ile etkileşen bir **client (istemci)** sağlar. Uygulaman belirli bir **kimlikle** çalışır — bu tipik olarak bir **Service Account'tur.** Örnek kod: Cloud Storage istemci kütüphanesini içe aktarır, istemciyi Service Account'un sağladığı **varsayılan kimlik bilgileriyle** örnekler ve bir bucket oluşturur.

### SDK'yı kurma ve başlatma

Google Cloud SDK'yı **Linux, macOS ve Windows**'a indirip kurabilirsin. Kurulumdan sonra:

- `gcloud init` ile SDK **başlatılır (initialize).** Başlatıldıktan sonra hemen kullanmaya başlarsın.
- SDK bileşenlerini kurup yönetebilir; komut tamamlama ve seçenek önerileri sunan **gcloud CLI etkileşimli kabuğunu (interactive shell)** kullanabilirsin.
- gcloud CLI komutlarını **betikleştirerek (script)** süreçlerini otomatikleştirebilirsin.

## Cloud Shell

**Cloud Shell**, Google Cloud konsolundan kullanılan, **tarayıcı tabanlı komut satırı erişimi** olan ücretsiz bir yönetim makinesidir. Özellikleri:

- Sana **5 GB Persistent Disk depolaması** olan **geçici bir VM** örneği verir.
- Başlattığında, **Debian tabanlı bir Linux** işletim sistemi çalıştıran bir **Compute Engine VM'i** sağlar.
- Örnekler **kullanıcı başına, oturum başına** sağlanır. Örnekler yalnızca Cloud Shell oturumun etkinken var olur ve **bir saatlik hareketsizlikten sonra sonlanır.**
- Yeni bir örnek sağlanması gerektiğinde, önceki örnekle kullanılan **Persistent Disk'i korur** — yani dosyaların kaybolmaz.
- **Google Cloud SDK önceden kurulu** gelir ve projelerine/kaynaklarına **yerleşik yetkilendirmesi** vardır.
- **Theia tabanlı yerleşik bir kod düzenleyici (code editor)** ile gelir; dizinlere göz atar, VM'indeki dosyaları görüntüler ve düzenlersin.

> **İlk modülle bağ:** İlk modülde de Cloud Shell'i görmüştük (5 GB kalıcı home, hep kurulu/güncel/kimlik doğrulanmış gcloud). Burada ek ayrıntılar: bir saat hareketsizlikte sonlanır ama Persistent Disk korunur; Theia tabanlı editör dahildir.

## Cloud Code

**Cloud Code**, bulut uygulamalarını **favori IDE'nde (entegre geliştirme ortamı)** geliştirmene yardım eder. Google Cloud için bulut uygulamalarını oluşturmayı, dağıtmayı ve hata ayıklamayı kolaylaştıran bir dizi **IDE eklentisidir (plugins).**

Şunlar için mevcuttur: **Cloud Shell Editor, Visual Studio Code** ve **JetBrains IDE'leri** (Java için IntelliJ, Python için PyCharm dahil).

Cloud Code, IDE içindeki yaygın iş akışlarını sadeleştirir — önemsiz olmayan görevleri IDE'nin içindeki basit bir arayüzde birleştirir. Öne çıkan entegrasyonlar:

- **Secret Manager entegrasyonu.** Secret Manager, Google Cloud'un parolaları, anahtarları, sertifikaları ve diğer hassas verileri **güvenle saklama** servisidir. Bu entegrasyonla hassas verini IDE içinden yönetirsin.
- **Cloud API yönetimi.** Mevcut Cloud API'lere göz atar, programlama diline özel Cloud Client Library dokümantasyonunu görür, API'leri kullanan kod örneklerini bulup kopyalarsın.
- **Cloud Code for Kubernetes.** Kubernetes uygulamalarını IDE'nde geliştirirsin. Uygulamalarını **yerel bir kümede** ya da **GKE'de** çalıştırıp hata ayıklarsın. **Kubernetes Explorer**, kaynaklarını görselleştirip yönetmenin kolay yolunu sunar — ilgili CLI komutlarını hatırlaman gerekmez. Örnek: bir Pod'a sağ tıklayıp loglarını akıtabilir ya da etkileşimli bir terminal açabilirsin. Ayrıca karmaşık **YAML yapılandırma dosyaları** için **otomatik tamamlama ve satır içi dokümantasyon** ile yazım yardımı sağlar.
- **Cloud Run ile çalışma.** Cloud Run, otomatik ölçeklenen ve **sıfıra kadar inen (scales down to zero)** tam yönetilen serverless üründür. Cloud Code ile Cloud Run servisini geliştirir, **Cloud Run Emülatörü** ile yerel olarak çalıştırıp hata ayıklar, hazır olunca IDE'den dağıtırsın. **Cloud Run Explorer** ile servislerini IDE'den yönetirsin.

## Yerel emülatörler (local emulators)

gcloud CLI, uygulamalarında kullanabileceğin çeşitli Google Cloud servisleri için **yerel emülatörler** içerir. Emülatör, gerçek servisin yerel bir taklididir; geliştirirken gerçek servise bağlanmadan çalışmanı sağlar.

- `gcloud beta emulators` komutlarıyla emülatörleri kurar ve yönetirsin.
- Yerel emülatör ile gerçek Google Cloud servisi arasında geçiş yaparken **uygulama kodunu değiştirmen gerekmez.** Belirli ortam değişkenlerini ayarladığında, uygulamanın kullandığı Cloud Client Libraries, gerçek servis yerine **otomatik olarak yerel emülatöre bağlanır.** (Yapılandırmayı koddan ayırma ilkesinin somut faydasını burada görüyorsun.)
- Faydası: Kodunu **ilgili servislere bağlantı gerektirmeden** geliştirirsin ve **proje kaynağı tüketmezsin** (dolayısıyla maliyet çıkmaz).
- Şu anda yerel emülatörler şunlar için mevcut: **Bigtable, Datastore, Firestore, Pub/Sub ve Spanner.**

> **Neden emülatör?** Geliştirme sırasında her testte gerçek Firestore/Pub/Sub'a bağlanmak yavaş ve masraflıdır. Emülatör, hızlı ve bedava bir yerel döngü sağlar; kodu değiştirmeden sadece ortam değişkeniyle geçiş yaparsın.

## Cloud Workstations

**Cloud Workstations**, Google Cloud için **tam yönetilen ve güvenli, bulut tabanlı geliştirme ortamları** sağlar. Sorunu şöyle çözer: Geliştiricilerin yazılım kurup kurulum betikleri çalıştırması yerine, ortamını **yeniden üretilebilir (reproducible)** biçimde belirten bir **workstation yapılandırması** oluşturursun.

Faydaları:

- Geliştiriciler **hızlı ve tutarlı** geliştirme ortamlarına her yerden, her zaman erişir — tarayıcı, SSH ya da yerel IDE ile.
- Bir konteynerde çalışabilen **her kod düzenleyiciyi ve uygulamayı** destekler.
- BT yöneticileri, ekipteki tüm geliştiriciler için bulut geliştirme ortamlarını kolayca sağlar, ölçekler, yönetir ve güvenceye alır.
- Ortamlar, geliştiricilerin konumu ya da bilgisayar/ağ türü ne olursa olsun **tutarlıdır** — "bende çalışıyordu" sorununu ortadan kaldırır.
- **Geçici (ephemeral) Compute Engine VM'lerinde** çalışır; talep üzerine ya da IDE boştayken başlatılıp durdurularak **maliyet tasarrufu** sağlar.
- IDE, **müşterinin kendi VM'lerinde ve Persistent Disk'lerinde, müşterinin VPC'si içinde** çalışır — böylece geliştirici makineleri ve kod **güvende** kalır.

> **Cloud Shell vs Cloud Workstations:** Cloud Shell hızlı, geçici, 5 GB'lık bir yönetim kabuğudur (bir saatte sonlanır). Cloud Workstations ise ekip için standartlaştırılmış, yapılandırılabilir, güvenli ve kalıcı bir geliştirme ortamıdır — "geliştiricinin asıl çalışma tezgahı."

---

# Toplu Özet (Hızlı Tekrar)

Modülün tamamını bir arada görelim.

**En iyi uygulamalar — bulutun üç gerçeği:** küresel erişim, ölçeklenebilirlik/yüksek kullanılabilirlik, güvenlik (gerekirse veri ikametgâhı).

**Kod ve ortam:** Kodu Git'te tut; bağımlılıkları depoya gömme, dependency manager ile bildir/kur; config'i koddan ayır, ortam değişkeni kullan.

**Mimari:** Monolit yerine — maliyet/faydayı değerlendirerek — mikroservis; asenkron ve olay güdümlü işleme (Cloud Storage + Cloud Run tetikleyici); gevşek bağlılık (Eventarc olay kuyruğu, Pub/Sub mesaj kuyruğu; payload'da yalnızca gerekli alanlar); durumsuzluk (worker pattern, hızlı başlat/zarif kapan, durumu Firestore gibi ayrı DB'de tut).

**Dayanıklılık:** Geçici hata → retry + exponential backoff (Cloud Client Libraries otomatik yapar); kalıcı hata → circuit breaker; kullanıcıya → graceful degradation.

**Performans ve erişim katmanları:** Önbellek (Memorystore = Redis/Memcached, uygulama verisi; Cloud CDN = Edge, web içeriği); API gateway (Apigee — güvenlik, rate limiting, kota, analitik; legacy'ye modern API cephesi); kimlik devretme (Identity Platform / Firebase Authentication, federated identity).

**Operasyon:** Logları olay akışı gibi ele al (stdout'a yaz, Google Cloud Observability topla/izle); CI/CD (CI = entegrasyon+otomatik test; Delivery = üretime hazır, insan onaylı; Deployment = otomatik üretim); Cloud Build → Artifact Registry → Cloud Deploy; blue/green ve canary; büyük göçlerde strangler deseni.

**Araçlar:** Cloud API'ler (HTTP+JSON veya gRPC; credentials şart); Google Cloud SDK (CLI araçları + Cloud Client Libraries); gcloud CLI (gcloud, gsutil, bq; gcloud storage tercih edilen; components ile bileşen yönetimi); Cloud Client Libraries (önerilen erişim yolu, dile doğal, otomatik auth+retry, çoğu gRPC); Cloud Shell (tarayıcıdan, 5 GB kalıcı, Debian, 1 saatte sonlanır, Theia editör); Cloud Code (IDE eklentileri — VS Code, JetBrains, Cloud Shell Editor; Secret Manager, Kubernetes, Cloud Run emülatörü); yerel emülatörler (Bigtable, Datastore, Firestore, Pub/Sub, Spanner); Cloud Workstations (yönetilen, tutarlı, güvenli ekip geliştirme ortamı).

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Config nerede?** Kaynak kodda sabit **değil**, **ortam değişkeninde**. Böylece test edilen kod aynen üretimde çalışır.
- **Bağımlılıklar?** Depoya gömme; **dependency manager** ile sürümlü bildir/kur.
- **Monolit vs mikroservis:** Mikroservis bağımsız geliştirme/ölçekleme verir ama refactor maliyetlidir. Her zaman **maliyet/fayda** değerlendir; "modern" diye körü körüne seçme.
- **Gevşek bağlılık:** Eventarc = **olay kuyruğu**, Pub/Sub = **mesaj kuyruğu**. HTTP payload'da **yalnızca ihtiyacın olan alanlara** bağlan (geriye dönük uyumluluk).
- **Durumsuzluk:** Bileşen durum saklamaz/paylaşmaz; durum **ayrı veritabanında** (ör. Firestore). Worker pattern ile kolay ölçeklenir.
- **Geçici vs kalıcı hata:** Geçici → **retry + exponential backoff** (Cloud Client Libraries otomatik). Kalıcı → **circuit breaker**. Kullanıcıya → **graceful degradation** (hata gösterme, özelliği gizle).
- **Önbellek katmanları:** **Memorystore** (Redis/Memcached) = uygulama verisi; **Cloud CDN** (Edge) = web/statik içerik.
- **Apigee** = API gateway/proxy: güvenlik, rate limiting, kota, analitik; legacy sistemlere modern API cephesi.
- **Kimlik devretme:** **Identity Platform** / **Firebase Authentication**; e-posta/parola, SAML, OpenID Connect, MFA, harici sağlayıcılar. Kendi auth'unu yazma.
- **Loglar:** Dosya yönetme; **stdout**'a yaz, altyapı toplasın. Serverless için doğal yol. İzleme: **Google Cloud Observability**.
- **CI/CD:** CI = paylaşılan dala entegrasyon + otomatik test. **Delivery** = üretime hazır, **insan** dağıtır. **Deployment** = testler geçerse **otomatik** üretim. Araçlar: **Cloud Build → Artifact Registry → Cloud Deploy**.
- **Dağıtım stratejileri:** **Blue/green** (iki ortam, anında geçiş/geri dönüş); **canary** (küçük dilimden kademeli); **strangler** (legacy'yi parça parça boğ).
- **Cloud API çağrı biçimleri:** **HTTP + JSON** ya da **gRPC** (ikili, daha performanslı). Her ikisi de **credentials** gerektirir.
- **Cloud Storage CLI:** Tercih edilen **gcloud storage** (gsutil'den performanslı). BigQuery → **bq**. Bileşen yönetimi → **gcloud components**.
- **Cloud Client Libraries** = önerilen erişim yolu; dile doğal, otomatik auth + retry, çoğu gRPC kullanır; uygulama kimliği genelde **Service Account**.
- **Cloud Shell** = tarayıcı kabuğu, 5 GB Persistent Disk, Debian VM, 1 saat hareketsizlikte sonlanır (disk korunur), Theia editör. **Cloud Workstations** = ekip için yönetilen, tutarlı, güvenli, yapılandırılabilir geliştirme ortamı (müşteri VPC'sinde, ephemeral VM).
- **Cloud Code** = IDE eklentileri (VS Code, JetBrains, Cloud Shell Editor); Secret Manager, Kubernetes Explorer, YAML yardımı, Cloud Run emülatörü.
- **Yerel emülatörler:** Bigtable, Datastore, Firestore, Pub/Sub, Spanner. Kod değişmeden ortam değişkeniyle geçiş; kaynak tüketmez.

---

> **Kapanış:** Bu modül, altyapı bilgini "iyi bulut uygulaması nasıl yazılır" pratiğine dönüştürdü. Önce mimari ilkeleri (durumsuzluk, gevşek bağlılık, dayanıklılık, CI/CD) kavradın; sonra bunları hayata geçireceğin araçları (Cloud API'ler, SDK, CLI, Client Libraries, Cloud Code, emülatörler, Workstations) tanıdın. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; bir kavramda takılırsan ilgili ana bölüme dön ve "neden var olduğunu" yeniden kur. Sıradaki modüle geçmeye hazırsın.
