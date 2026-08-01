# Google Cloud Temelleri: Çekirdek Altyapı — Baştan Sona Öğretici

> Bu metin, "Google Cloud Fundamentals: Core Infrastructure" modülünde anlatılan **her şeyi** öğretmek için yazıldı. Amaç ezber yaptırmak değil, kavratmak. Bir konuyu okuduğunda "tamam, bunu neden anlattılar, ne işe yarıyor, ne zaman kullanırım" sorularının hepsine cevap verebilmen hedefleniyor. Acele etme; her bölümü sindirerek oku. Sınav notları ve tuzaklar konuların içine serpiştirildi.

---

## Bu modül tam olarak neyi öğretiyor?

Google Cloud Developer sertifikasına giden yolda ilk durak, "çekirdek altyapı" dediğimiz temel yapı taşlarını tanımaktır. Kod yazmaya başlamadan önce, kodun **üzerinde çalışacağı dünyayı** anlaman gerekir: Sunucular nerede duruyor? Verilerini nasıl saklıyorsun? Bir uygulamaya kim erişebiliyor, kim erişemiyor? Trafik yoğunlaşınca ne oluyor?

Bu modül yedi ana bölümden oluşuyor ve şu yolculuğu izliyor:

1. **Bulut bilişim nedir ve neden var** — IaaS, PaaS, serverless, SaaS; Google'ın ağı; bölgeler ve zonlar; güvenlik; fiyatlandırma.
2. **Kaynakların nasıl organize edildiği** — kaynak hiyerarşisi (organizasyon → klasör → proje → kaynak), IAM (kim, neyi, nerede yapabilir), Service Account'lar, Cloud Identity, Google Cloud'a erişim yolları.
3. **Compute Engine ve sanal ağ** — VM'ler, VPC, otomatik ölçekleme, load balancing, güvenlik duvarları, ağları birbirine bağlama.
4. **Depolama seçenekleri** — Cloud Storage, Cloud SQL, Spanner, Firestore, Bigtable ve hangisini ne zaman seçeceğin.
5. **Konteynerler ve Kubernetes** — konteyner mantığı, Kubernetes kavramları, GKE.
6. **Uygulama geliştirme** — Cloud Run ve Cloud Run functions.
7. **Üretken yapay zeka ve prompt mühendisliği** — Gemini'yi verimli kullanmak.

Şimdi başlayalım.

---

# BÖLÜM 1 — Bulut Bilişimin Temelleri

## Bulut aslında nedir? (Beş özellik testi)

"Bulut" kelimesi bugün her yerde, ama çoğu insan onu net tanımlayamaz. ABD'deki Ulusal Standartlar ve Teknoloji Enstitüsü (NIST) bu terimi ortaya attı ve şu netliği getirdi: Bir sistemin gerçekten "bulut" sayılması için **beş özelliğin hepsine** birden sahip olması gerekir. Bu beş özellik eşit derecede önemlidir; biri eksikse tam anlamıyla bulut değildir.

1. **İsteğe bağlı ve self-servis (on-demand, self-service).** Kaynağa ihtiyacın olduğunda bir web arayüzünden kendin alırsın. İşlem gücü, depolama, ağ... Hiçbir insanla konuşmana, form doldurup onay beklemene gerek yoktur. Sen tıklarsın, kaynak gelir.

2. **İnternet üzerinden geniş erişim.** Bağlantın olan her yerden kaynaklara ulaşabilirsin. Ofiste, evde, uçakta — fark etmez.

3. **Kaynak havuzu (resource pooling).** Bulut sağlayıcısının devasa bir kaynak havuzu vardır ve senin ihtiyacını bu havuzdan karşılar. Sağlayıcı toptan alım yaptığı için maliyeti düşürür ve bu tasarrufu sana yansıtır. Sen kaynağın fiziksel olarak tam olarak nerede durduğunu bilmek zorunda değilsin, umursaman da gerekmez.

4. **Elastiklik (elasticity).** Kaynaklar esnektir, yani sen de esnek olabilirsin. Daha fazla lazımsa hızlıca alırsın; daha az lazımsa geri verirsin. Talep arttığında büyür, azaldığında küçülürsün.

5. **Kullandığın kadar öde (pay-as-you-go).** Sadece kullandığın ya da rezerve ettiğin kadar ödersin. Kullanmayı bıraktığında ödeme de durur.

İşte bulutun tanımı budur. Bir hizmete "bulut" diyebilmek için bu beş şartın karşılanması gerekir.

> **Neden bu önemli?** Çünkü sertifikada ve gerçek hayatta "şu servis gerçekten bulut mantığında mı çalışıyor" diye düşünmen gerektiğinde bu beş maddeye bakarsın. Örneğin kullandığın kadar ödemiyorsan, sabit bir sunucu kiralamış olabilirsin ama bu "bulut" felsefesinin tamamını yakalamamış olur.

## Buluta nasıl geldik? Üç dalga

Bulut bir anda ortaya çıkmadı; üç dalga halinde evrildi. Bu tarihi bilmek, bugün neden konteynerlerden ve yönetilen servislerden bu kadar bahsettiğimizi anlamanı sağlar.

**Birinci dalga — Colocation (ortak yerleşim).** Eskiden şirketler kendi veri merkezlerini, yani binalarını, elektriğini, soğutmasını inşa ederlerdi. Bu çok pahalıydı. Colocation ile şirketler kendi bina yatırımı yapmak yerine, hazır bir veri merkezinde **fiziksel alan kiraladılar**. Böylece emlak maliyetinden kurtuldular ama donanım hâlâ onlarındı.

**İkinci dalga — Sanallaştırılmış veri merkezleri (virtualization).** Burada fiziksel bileşenler — sunucular, CPU'lar, diskler, load balancer'lar — sanal cihazlara dönüştü. Yapı taşları aynıydı ama artık yazılımla tanımlanıyordu. Ancak dikkat: Sanallaştırmada altyapıyı hâlâ **işletme kendisi yönetir**. Ortam kullanıcı tarafından kontrol edilir ve yapılandırılır. Yani sunucuyu görmesen de, onu kurmak, güncellemek, ölçeklemek yine senin işindir.

**Üçüncü dalga — Konteyner tabanlı, tam otomatik, elastik bulut.** Google birkaç yıl önce şunu fark etti: Sanallaştırma modelinin sınırları içinde işi yeterince hızlı büyütemiyordu. Bu yüzden konteyner tabanlı bir mimariye geçti. Bu üçüncü dalgada **servisler altyapıyı otomatik olarak sağlar ve yapılandırır**. Sen uygulamana odaklanırsın, altyapı arka planda kendini ayarlar. İşte Google Cloud, bu üçüncü dalga bulutu müşterilerine sunar.

Google'ın felsefesi şudur: Gelecekte her şirket — büyüklüğü veya sektörü ne olursa olsun — kendini rakiplerinden teknolojiyle ayıracak. Bu teknoloji giderek yazılım biçiminde olacak. İyi yazılım ise kaliteli veriye dayanır. Dolayısıyla **her şirket er ya da geç bir veri şirketi haline gelecek**. Bulut, bu dönüşümü mümkün kılan zemindir.

## Servis modelleri: IaaS, PaaS, Serverless, SaaS

Sanallaştırma dalgasıyla birlikte iki yeni sunum biçimi ortaya çıktı ve bunları anlamak, Google Cloud servislerini doğru sınıflandırabilmen için kritik.

### IaaS — Altyapı Hizmet Olarak (Infrastructure as a Service)

IaaS, sana **ham işlem, depolama ve ağ** yeteneği sunar. Bunlar fiziksel bir veri merkezine benzeyen sanal kaynaklar olarak düzenlenmiştir. Yani sana sanal bir sunucu verilir; işletim sistemini sen seçer, yazılımı sen kurar, her şeyi sen yönetirsin. Google Cloud'daki örneği **Compute Engine**'dir.

IaaS'ın ödeme mantığı şudur: **Önceden ayırdığın (allocate ettiğin) kaynak için ödersin.** Bir VM'i açtıysan, üzerinde iş yapmasan bile o kaynak sana ayrılmıştır ve ödemesini yaparsın.

> **Analoji:** IaaS, boş bir daire kiralamaya benzer. Bina hazırdır ama içini nasıl döşeyeceğine, nasıl kullanacağına sen karar verirsin. Mobilyayı da bakımı da sen halledersin.

### PaaS — Platform Hizmet Olarak (Platform as a Service)

PaaS, kodunu altyapıya erişim sağlayan kütüphanelere **bağlar**. Böylece sen altyapıyla değil, uygulama mantığıyla ilgilenirsin. Google Cloud'daki örneği **App Engine**'dir.

PaaS'ın ödeme mantığı IaaS'tan farklıdır: **Gerçekten kullandığın kaynak için ödersin.** Kaynak boşta beklerken değil, iş yaparken faturalanır.

> **Analoji:** PaaS, tam donanımlı bir ofise taşınmaya benzer. Elektrik, internet, mobilya, temizlik hazırdır. Sen sadece gelip çalışmaya başlarsın.

### Yönetilen servislere doğru kayış

Bulut olgunlaştıkça momentum **yönetilen altyapı ve yönetilen servislere** kaydı. Yönetilen kaynak ve servisleri kullanmak, şirketlerin teknik altyapı kurup bakmak yerine iş hedeflerine odaklanmasını sağlar. Ürünü ve hizmeti müşteriye daha hızlı ve güvenilir biçimde ulaştırırsın.

> **Temel sezgi:** Bir servis ne kadar "yönetilen" ise, senin üzerine düşen operasyonel iş o kadar azdır. Bu, tüm Google Cloud'u anlamanın anahtarıdır. Servisleri "ne kadarını Google yönetiyor, ne kadarını ben yönetiyorum" ekseninde düşün.

### Serverless — Sunucusuz

Serverless, bu evrimin bir sonraki adımıdır. Geliştiricinin **hiçbir altyapı yönetimi yapmadan** sadece koduna odaklanmasını sağlar. Sunucu yapılandırması diye bir dert kalmaz. Google'ın serverless teknolojileri:

- **Cloud Run** — konteyner tabanlı mikroservis uygulamanı tam yönetilen bir ortamda çalıştırır.
- **Cloud Run functions** — olay güdümlü (event-driven) kodu, kullandıkça öde mantığıyla yönetir.

### SaaS — Yazılım Hizmet Olarak (Software as a Service)

SaaS, kursun kapsamı dışında ama nereye oturduğunu bilmek iyidir. SaaS, **tüm uygulama yığınını** sunar; yani hazır bir bulut uygulamasını doğrudan kullanırsın. Bilgisayarına kurmazsın; bulutta bir servis olarak çalışır ve internet üzerinden tüketilir. **Gmail, Docs, Drive** (Google Workspace'in parçaları) birer SaaS örneğidir.

> **Zihinde tut — soyutlama merdiveni:** IaaS → PaaS → Serverless → SaaS ilerledikçe sen daha az şey yönetir, uygulamana daha çok odaklanırsın. Sınavda "en yüksek soyutlama düzeyini seç, gereksinimlerini karşıladığı sürece" mantığı sık sık karşına çıkar.

**Hızlı sınıflandırma tablosu:**

| Model | Google Cloud örneği | Sen ne yönetirsin? | Ödeme mantığı |
| --- | --- | --- | --- |
| IaaS | Compute Engine | İşletim sistemi + yukarısı | Ayrılan kaynak |
| PaaS | App Engine | Sadece uygulama | Kullanılan kaynak |
| Serverless | Cloud Run, Cloud Run functions | Sadece kod | İstek işlenirken (ms hassasiyetle) |
| SaaS | Gmail, Docs, Drive | Hiçbir şey (sadece kullanırsın) | Abonelik/kullanım |

## Google'ın küresel ağı

Google Cloud, Google'ın **kendi küresel ağı** üzerinde çalışır. Bu, türünün en büyük ağıdır ve Google yıllar boyunca milyarlarca dolar yatırım yaptı. Ağın amacı, uygulamalarına mümkün olan en yüksek verimi (throughput) ve en düşük gecikmeyi (latency) sağlamaktır.

Bunu nasıl yapar? Dünya çapında **100'den fazla içerik önbellek (content caching) düğümü** ile. Yüksek talep gören içerik bu düğümlerde önbelleğe alınır, böylece kullanıcı isteği en hızlı yanıt verecek konumdan karşılanır. Bunun yanına yüksek bant genişlikli **denizaltı kabloları** ve yedekli bulut bölgelerini ekle; sonuç, kullanıcın dünyanın neresinde olursa olsun hizmeti hızlı almasıdır.

## Konumlar: Coğrafya, bölge, zon, çoklu bölge

Bu kavram sertifikada ve gerçek mimaride sürekli karşına çıkacağı için iyice oturt.

Google Cloud'un altyapısı **yedi büyük coğrafi alanda** bulunur: Kuzey Amerika, Güney Amerika, Avrupa, Afrika, Orta Doğu, Asya ve Avustralya.

Neden birden fazla konum önemli? Çünkü uygulamanı nereye koyduğun şu üç niteliği doğrudan etkiler:

- **Kullanılabilirlik (availability)** — hizmetin ne kadar ayakta kaldığı.
- **Dayanıklılık (durability)** — verinin kaybolmadan kalıcılığı.
- **Gecikme (latency)** — bir veri paketinin kaynaktan hedefe ulaşma süresi.

Bu coğrafyalar **bölgelere (region)** ve **zonlara (zone)** ayrılır:

- **Bölge (region):** Bağımsız bir coğrafi alandır ve zonlardan oluşur. Örneğin Londra, yani `europe-west2`, bir bölgedir ve şu anda üç farklı zondan oluşur.
- **Zon (zone):** Google Cloud kaynaklarının fiilen konuşlandırıldığı alandır. Örneğin Compute Engine ile bir VM başlatırsan, belirttiğin zonda çalışır.

Neden hem farklı zonlar hem farklı bölgeler var?

- **Farklı zonlar** → kaynak yedekliliği (redundancy). Aynı bölge içinde işini birden fazla zona yayarsan, bir zon çökse bile diğeri ayakta kalır.
- **Farklı bölgeler** → hem uygulamayı dünyadaki kullanıcılara yaklaştırmak, hem de bir doğal afet gibi tüm bir bölgeyi etkileyen sorunlara karşı korunmak.

### Çoklu bölge (multi-region)

Bazı servisler kaynaklarını **çoklu bölgeye** yerleştirmeni destekler. Örneğin **Spanner**'ın çoklu bölge yapılandırması, veritabanını sadece birden fazla zona değil, **birden fazla bölgedeki birden fazla zona** kopyalar (replicate). Bu ek kopyalar sayesinde, yapılandırmadaki bölgelere (örneğin Hollanda ve Belçika) yakın konumlardan düşük gecikmeyle okuma yapabilirsin.

> **Not:** Google'ın desteklediği bölge ve zon sayısı sürekli artıyor. En güncel liste `cloud.google.com/about/locations` adresinde.

## Sürdürülebilirlik: Veri merkezleri ve enerji

Sanal dünya fiziksel altyapı üzerine kuruludur ve o uğuldayan sunucu rafları çok enerji harcar. Mevcut veri merkezleri dünya elektriğinin yaklaşık **%2'sini** kullanıyor. Bu yüzden Google, veri merkezlerini olabildiğince verimli çalıştırmaya çalışır — hem gezegen için hem de kendi çevresel hedefleri olan müşterileri için (senin iş yükünü Google Cloud'da çalıştırman, senin çevresel hedeflerine de katkı sağlayabilir).

Somut kanıtlar:

- Google'ın veri merkezleri **ISO 14001** sertifikasını alan ilk merkezlerdir. Bu standart, kaynak verimliliğini artırıp atığı azaltarak çevresel performansı iyileştirme çerçevesi çizer.
- **Hamina, Finlandiya** veri merkezi, filonun en gelişmiş ve verimli merkezlerinden biridir. Soğutmasını **Finlandiya Körfezi'nin deniz suyuyla** yapar — dünyada türünün ilk örneğidir.
- Google, ilk on yılında **karbon-nötr** olan ilk büyük şirket oldu.
- İkinci on yılında **%100 yenilenebilir enerjiye** ulaşan ilk şirket oldu.
- **2030 hedefi:** Günün 24 saati, haftanın 7 günü, yılın 365 günü **karbonsuz enerjiyle** çalışan ilk büyük şirket olmak.

## Güvenlik: Katmanlı savunma

Google'ın dokuz servisinin her biri bir milyardan fazla kullanıcıya sahip. Bu ölçekte güvenlik her an akılda. Google güvenliği **ilerleyen katmanlar** halinde tasarlar: en alttaki fiziksel güvenlikten başlayıp, donanım ve yazılıma, oradan operasyonel süreçlere doğru çıkar. Bu katmanları sırayla anlatalım — çünkü her katmanın kendine has koruma mekanizması var.

**1. Donanım altyapısı katmanı.** Üç ana özellik:

- **Donanım tasarımı ve kökeni (provenance):** Sunucu kartları ve ağ ekipmanları Google tarafından özel tasarlanır. Google ayrıca özel çipler tasarlar; buna sunuculara ve çevre birimlerine yerleştirilen **donanım güvenlik çipi** de dahildir.
- **Güvenli önyükleme yığını (secure boot stack):** Sunucular doğru yazılım yığınını yüklediğinden emin olmak için BIOS, önyükleyici, çekirdek ve temel işletim sistemi imajı üzerinde kriptografik imzalar kullanır.
- **Tesis güvenliği:** Google kendi veri merkezlerini tasarlar ve inşa eder; çok katmanlı fiziksel korumalar içerir. Erişim, çok az sayıda çalışanla sınırlıdır. Üçüncü taraf veri merkezlerinde barınan sunucularda bile Google'ın kendi kontrol ettiği ek fiziksel güvenlik önlemleri vardır.

**2. Servis dağıtım katmanı.** Ana özellik: **servisler arası iletişimin şifrelenmesi.** Google servisleri birbiriyle **RPC (uzak prosedür çağrısı)** ile konuşur. Altyapı, veri merkezleri arasındaki tüm RPC trafiğini otomatik olarak şifreler. Google, donanımsal kriptografik hızlandırıcılar devreye alarak bu varsayılan şifrelemeyi veri merkezleri **içindeki** tüm RPC trafiğine de yayıyor.

**3. Kullanıcı kimliği katmanı.** Google'ın merkezi kimlik servisi (kullanıcıya Google giriş sayfası olarak görünür) basit kullanıcı adı-parolanın ötesine geçer. Risk faktörlerine göre — örneğin daha önce aynı cihazdan veya benzer konumdan mı giriş yapıldı — kullanıcıya **akıllıca ek doğrulama** sorar. Kullanıcılar ayrıca **U2F (Universal 2nd Factor)** açık standardına dayalı ikinci faktör cihazları kullanabilir.

**4. Depolama servisleri katmanı.** Ana özellik: **durağan halde şifreleme (encryption at rest).** Uygulamalar fiziksel depolamaya doğrudan değil, depolama servisleri üzerinden erişir; merkezi yönetilen anahtarlarla şifreleme tam bu servis katmanında uygulanır. Ayrıca sabit disklerde ve SSD'lerde donanımsal şifreleme desteği vardır.

**5. İnternet iletişim katmanı.** İki özellik:

- **Google Front End (GFE):** İnternete açılan Google servisleri kendini GFE'ye kaydeder. GFE, tüm TLS bağlantılarının bir açık-özel anahtar çifti ve bir Sertifika Otoritesinden (CA) alınmış X.509 sertifikasıyla sonlandırılmasını sağlar; ayrıca "perfect forward secrecy" gibi en iyi uygulamaları izler.
- **DoS (hizmet reddi) koruması:** Altyapının devasa ölçeği, birçok DoS saldırısını kendiliğinden emmesini sağlar. Bunun üstüne çok katmanlı DoS korumaları GFE arkasındaki servislere yönelen riski daha da azaltır.

**6. Operasyonel güvenlik katmanı.** Dört özellik:

- **Saldırı tespiti (intrusion detection):** Kurallar ve makine zekâsı, olası olaylar için güvenlik ekiplerini uyarır. Google, tespit ve müdahale etkinliğini ölçüp iyileştirmek için **Red Team** tatbikatları yapar.
- **İç risk azaltma (insider risk):** Yönetici erişimi verilmiş çalışanların faaliyetleri agresif biçimde sınırlanır ve aktif izlenir.
- **Çalışan U2F kullanımı:** Kimlik avına (phishing) karşı çalışan hesapları U2F uyumlu güvenlik anahtarları gerektirir.
- **Sıkı yazılım geliştirme pratikleri:** Merkezi kaynak kontrolü ve yeni kod için iki kişilik inceleme zorunludur. Geliştiricilere belirli hata sınıflarını önleyen kütüphaneler verilir. Ayrıca bir **Vulnerability Rewards Program** (açık bildirene ödül) yürütülür.

> Daha fazlası: `cloud.google.com/security/security-design`.

## Açık kaynak ve satıcıya bağımlılık (vendor lock-in)

Bazı kurumlar buluta geçmekten çekinir çünkü belirli bir satıcıya kilitlenmekten korkar. Google bu korkuya şu cevabı verir: Eğer bir müşteri Google'ın artık ihtiyaçlarına uygun olmadığına karar verirse, uygulamalarını **başka yerde çalıştırma imkânı** sunulur.

Google, teknolojisinin kilit parçalarını **açık kaynak lisanslarıyla** yayınlar ve böylece Google dışında seçenekler sunan ekosistemler kurar. Örnekler:

- **TensorFlow** — Google içinde geliştirilen açık kaynak makine öğrenmesi kütüphanesi.
- **Kubernetes ve GKE** — mikroservisleri farklı bulutlarda karıştırıp eşleştirmeni sağlar.
- **Google Cloud Observability** — iş yüklerini birden fazla bulut sağlayıcıda izlemeni sağlar.

Yani interoperabilite (birlikte çalışabilirlik) yığının birçok katmanında sunulur.

## Fiyatlandırma ve maliyet kontrolü

Bu bölüm, faturanı şişirmeden bulut kullanmayı öğretir.

**Saniye bazlı faturalandırma.** Google, IaaS işlem hizmeti Compute Engine için **saniye bazlı** faturalandırmayı sunan ilk büyük sağlayıcıydı. Bu artık GKE, Managed Service for Apache Spark (Hadoop'un servis hali) ve App Engine esnek ortam VM'leri için de geçerlidir.

**Sürekli kullanım indirimleri (sustained-use discounts).** Compute Engine'de otomatik uygulanır. Bir VM'i ayın **%25'inden fazla** çalıştırdığında, o örnek için kullandığın her ek dakikaya otomatik indirim gelir. Hiçbir şey yapmana gerek yok; kendiliğinden işler.

**Özel makine tipleri (custom machine types).** VM'lerinin vCPU ve bellek miktarını iş yüküne göre ince ayar yaparak, fiyatını da iş yüküne göre şekillendirebilirsin.

**Fiyat hesaplama aracı.** Maliyetini önceden tahmin etmek için: `cloud.google.com/products/calculator`.

Peki "yanlışlıkla dev bir fatura çıkarmayı" nasıl önlersin? Üç mekanizma:

1. **Bütçeler (budgets).** Faturalandırma hesabı düzeyinde veya proje düzeyinde bütçe tanımlarsın. Bütçe sabit bir limit olabilir ya da başka bir metriğe bağlanabilir (örneğin geçen ayki harcamanın bir yüzdesi).

2. **Uyarılar (alerts).** Maliyet bütçe limitine yaklaşınca haber almak için uyarı kurarsın. Örneğin 20.000 dolarlık bütçe ve %90 uyarı ile, harcaman 18.000 dolara gelince bildirim alırsın. Uyarılar genellikle **%50, %90 ve %100** düzeyinde ayarlanır ama özelleştirilebilir. (Not: Uyarı gelmesi harcamayı durdurmaz; sadece seni bilgilendirir.)

3. **Raporlar (reports).** Google Cloud konsolundaki görsel araçtır; harcamanı proje veya servis bazında izlemeni sağlar.

**Kotalar (quotas).** Bir hata ya da kötü niyetli saldırı yüzünden kaynakların aşırı tüketilmesini önler; hem hesap sahibini hem tüm topluluğu korur. İki tür kota vardır, ikisi de **proje düzeyinde** uygulanır:

- **Rate quotas (oran kotaları):** Belirli bir süre sonra sıfırlanır. Örneğin GKE varsayılan olarak her projeden API'sine **her 100 saniyede 3.000 çağrı** izin verir; süre dolunca sıfırlanır.
- **Allocation quotas (tahsis kotaları):** Projelerinde bulundurabileceğin kaynak sayısını yönetir. Örneğin varsayılan olarak her proje en fazla **15 VPC ağına** izin verir.

Projeler aynı kotalarla başlar ama bazılarını Google Cloud Support'tan artış talep ederek değiştirebilirsin.

> **Sınav ipucu:** "Rate quota mı allocation quota mı" ayrımını iyi bil. *Zamanla sıfırlanan* = rate. *Kaç adet tutabilirsin* = allocation.

---

# BÖLÜM 2 — Google Cloud'un Yapısı: Hiyerarşi, IAM, Kimlik

## Kaynak hiyerarşisi: Dört katman

Google Cloud'da her şey bir hiyerarşi içinde durur. Bu hiyerarşiyi anlamak sadece "düzen" meselesi değil; **politikaların (policy) nasıl uygulandığını** belirlediği için kritiktir. Alttan üste dört katman:

```text
Organizasyon Düğümü (Organization)   ← en üst
        │
     Klasörler (Folders)             ← alt klasörler de olabilir
        │
      Projeler (Projects)
        │
     Kaynaklar (Resources)           ← en alt (VM, bucket, tablo...)
```

- **Kaynaklar (resources):** VM'ler, Cloud Storage bucket'ları, BigQuery tabloları — Google Cloud'daki her somut şey. En alt katman.
- **Projeler (projects):** Kaynaklar projeler içinde organize edilir. İkinci katman.
- **Klasörler (folders):** Projeler klasörlere, hatta alt klasörlere gruplanabilir. Üçüncü katman.
- **Organizasyon düğümü (organization node):** Kuruluşundaki tüm projeleri, klasörleri ve kaynakları kapsar. En üst katman.

### En kritik kural: Politika mirası (inheritance)

Politikalar proje, klasör ve organizasyon düğümü düzeylerinde tanımlanabilir (bazı servisler tekil kaynağa da politika uygulatır). **Politikalar aşağı doğru miras alınır.** Yani bir klasöre politika uygularsan, o klasör içindeki tüm projelere de uygulanır.

> Bunu sindirmen çok önemli: Üstte tanımladığın bir izin, altındaki her şeye akar. Bu hem gücün hem de tehlikenin kaynağıdır. Organizasyon düzeyinde geniş bir yetki verdiysen, o yetki tüm alt kaynaklara iner.

## Projeler — İkinci katmana yakından bakış

Proje, Google Cloud servislerini etkinleştirip kullanmanın temelidir: API yönetimi, faturalandırma, iş birlikçileri ekleme/çıkarma, diğer Google servislerini açma — hepsi proje düzeyinde döner.

- Her proje, organizasyon düğümü altında **ayrı bir varlıktır** ve her kaynak **tam olarak bir projeye** aittir.
- Projeler ayrı ayrı faturalandırılıp yönetildiği için farklı sahiplere ve kullanıcılara sahip olabilir.

Her projenin **üç tanımlayıcı özelliği** vardır:

| Özellik | Kim atar? | Değiştirilebilir mi? | Not |
| --- | --- | --- | --- |
| **Project ID** | Google (küresel benzersiz) | Hayır — **değişmez (immutable)** | Google'a hangi projeyle çalışacağını söylerken kullanılır |
| **Project name** | Kullanıcı | Evet, istediğin zaman | Benzersiz olmak zorunda değil |
| **Project number** | Google (benzersiz) | — | Çoğunlukla Google'ın dahili takibi için |

> **Sınav tuzağı:** "Project ID değiştirilebilir mi?" → **Hayır, immutable.** "Project name?" → Evet, değişebilir. Bu ikisini karıştırma.

**Resource Manager.** Projeleri programatik yönetmek için tasarlanmış bir API'dir. Bir hesaba bağlı tüm projeleri listeler, yeni proje oluşturur, günceller, siler; hatta daha önce silinen projeleri kurtarabilir. RPC API ve REST API üzerinden erişilir.

## Klasörler — Üçüncü katman

Klasörler, politikaları **istediğin ayrıntı düzeyinde** atamanı sağlar. Bir klasördeki kaynaklar, o klasöre atanmış politika ve izinleri miras alır. Bir klasör; projeler, başka klasörler veya ikisinin karışımını içerebilir.

Ne işe yarar? Diyelim kuruluşunda birden fazla departman var ve her birinin kendi kaynakları var. Klasörlerle bu kaynakları departman bazında gruplarsın. Ayrıca ekiplere **yönetim haklarını devrederek** bağımsız çalışma imkânı verirsin.

Somut örnek: Aynı ekibin yönettiği iki proje varsa, politikaları ortak bir klasöre koyarsın; ikisi de aynı izinlere sahip olur. Alternatif — aynı politikaların kopyasını iki projeye ayrı ayrı koymak — hem yorucu hem hataya açıktır. İzinleri değiştirmen gerekirse tek yerde değil iki yerde uğraşırsın.

> **Önemli koşul:** Klasör kullanabilmen için bir **organizasyon düğümüne** sahip olman gerekir.

## Organizasyon düğümü — En üst katman

Hiyerarşinin en tepesindeki kaynaktır. Hesaba bağlı her şey — klasörler, projeler, diğer kaynaklar — bu düğümün altına gelir. Klasörler ve projeler, organizasyon düğümünün "çocukları" (children) sayılır.

Bu düğüme özel bazı roller vardır:

- **Organization Policy Administrator (organizasyon politikası yöneticisi):** Yalnızca yetkili kişilerin politikaları değiştirebilmesini sağlar.
- **Project Creator (proje oluşturucu):** Kimin proje oluşturabileceğini — dolayısıyla kimin para harcayabileceğini — kontrol etmenin harika bir yoludur.

**Organizasyon düğümü nasıl oluşur?** Bu, şirketinin Google Workspace müşterisi olup olmadığına bağlıdır:

- **Workspace domainin varsa:** Google Cloud projeleri otomatik olarak organizasyon düğümüne ait olur.
- **Yoksa:** **Cloud Identity** (Google'ın kimlik, erişim, uygulama ve uç nokta yönetim platformu) kullanarak bir tane üretirsin.

## IAM — Identity and Access Management

Bir organizasyon düğümünde çok sayıda klasör, proje ve kaynak olunca, "kim neye erişebilir" sorusunu yönetmen gerekir. Bunun aracı **IAM (Identity and Access Management)**'dir. IAM ile "**kim**, **neyi**, **hangi kaynak üzerinde** yapabilir" politikalarını tanımlarsın.

### "Kim" — Principal (asıl)

Politikadaki "kim" kısmı şunlardan biri olabilir:

- Bir Google hesabı
- Bir Google grubu
- Bir Service Account
- Bir Cloud Identity domaini

Bu "kim"e **principal** denir. Her principal'ın kendi tanımlayıcısı vardır — genellikle bir e-posta adresi.

### "Neyi yapabilir" — Rol (role)

Politikanın "neyi yapabilir" kısmı bir **rol** ile tanımlanır. Bir IAM rolü, **izinler (permissions) koleksiyonudur**. Bir principal'a rol verdiğinde, o rolün içerdiği tüm izinleri vermiş olursun.

Örnek: Bir projedeki VM'leri yönetmek için onları oluşturabilmen, silebilmen, başlatabilmen, durdurabilmen ve değiştirebilmen gerekir. Bu izinler tek bir rolde gruplanır ki anlaşılması ve yönetilmesi kolay olsun.

Bir principal'a hiyerarşinin belirli bir öğesinde rol verildiğinde, ortaya çıkan politika **hem o öğeye hem de altındaki tüm öğelere** uygulanır (yine miras kuralı).

### Deny (reddetme) politikaları

Belirli principal'ların belirli izinleri **kullanmasını engelleyen** deny kuralları tanımlayabilirsin — verilen rollerden bağımsız olarak. Kritik nokta: **IAM her zaman önce deny politikalarını, sonra allow politikalarını kontrol eder.** Yani bir deny varsa, allow olsa bile geçersiz kalır. Deny politikaları da allow gibi hiyerarşi boyunca miras alınır.

### Üç tür rol

**1. Temel roller (basic roles).** Kapsamı çok geniştir. Bir projeye uygulandığında o projedeki tüm kaynakları etkiler. Dört tanesi:

- **Viewer (görüntüleyici):** Kaynaklara erişir ama değişiklik yapamaz.
- **Editor (düzenleyici):** Kaynaklara erişir ve değişiklik yapar.
- **Owner (sahip):** Erişir, değiştirir, ayrıca **rolleri/izinleri yönetir ve faturalandırmayı ayarlar**.
- **Billing administrator (faturalandırma yöneticisi):** Faturalandırmayı yönetir ama kaynakları değiştiremez. (Şirketler çoğu zaman birinin fatura kontrolünü isteyip kaynaklara dokunmasını istemez; bu rol tam bunun içindir.)

**Uyarı:** Hassas veri içeren bir projede birden fazla kişi çalışıyorsa, temel roller muhtemelen **fazla geniştir**.

**2. Önceden tanımlı roller (predefined roles).** Belirli Google Cloud servisleri, işe göre hazırlanmış rol setleri sunar ve bu rollerin nerede uygulanabileceğini de tanımlar. Örneğin Compute Engine'de `instanceAdmin` gibi bir rolü belirli bir projeye, klasöre veya tüm organizasyona uygulayabilirsin. Bu, temel rollere göre çok daha isabetli, işe özel izinler sağlar.

**3. Özel roller (custom roles).** Daha da spesifik izinler gerektiğinde devreye girer. Birçok şirket **en az ayrıcalık (least-privilege)** modeli kullanır: Herkese işini yapmaya yetecek **en küçük** yetki verilir. Örneğin bazı kullanıcıların Compute Engine VM'lerini durdurup başlatmasına izin verip, yeniden yapılandırmasına izin vermeyen bir `instanceOperator` rolü tanımlayabilirsin. Custom roller bu kesin izinleri tanımlamanı sağlar.

Custom rol oluşturmadan önce iki önemli detay:

- **İzinleri sen yönetirsin.** Bu ek yük yüzünden bazı kuruluşlar predefined rollerle yetinmeyi tercih eder.
- **Custom roller yalnızca proje veya organizasyon düzeyine** uygulanabilir; **klasör düzeyine uygulanamaz.**

> **Sınav tuzağı:** "Custom rol klasöre uygulanır mı?" → **Hayır.** Sadece proje veya organizasyon.

**Rol türü seçim özeti:**

| Rol türü | Kapsam | Ne zaman |
| --- | --- | --- |
| Basic | Çok geniş (tüm proje) | Küçük ekip, hassas olmayan iş |
| Predefined | Servise/işe özel | Çoğu gerçek senaryo |
| Custom | Tam senin belirlediğin | En az ayrıcalık gerektiğinde; yönetim yükünü kabul ediyorsan |

## Service Account'lar

Şu senaryoyu düşün: Bir Compute Engine VM'inde çalışan bir program, düzenli olarak başka bulut servislerine erişmek zorunda. Her seferinde bir insanın elle erişim vermesini beklemek saçma olur. Çözüm: **VM'in kendisine gerekli izinleri vermek.** İşte Service Account'lar bunun içindir.

Service Account'lar, bir VM'e belirli izinler atamanı sağlar; böylece VM, **insan müdahalesi olmadan** diğer servislerle etkileşir. Örnek: VM'de çalışan bir uygulama Cloud Storage'a veri yazmak istiyor ama internetteki kimsenin değil, **yalnızca o VM'nin** erişmesini istiyorsun. Bir Service Account oluşturup o VM'yi Cloud Storage'a kimlik doğrulatırsın.

Service Account'ların özellikleri:

- Bir **e-posta adresiyle** adlandırılır, ama parola yerine **kriptografik anahtarlar** kullanır.
- Örneğin bir Service Account'a Compute Engine "Instance Admin" rolü verilmişse, o Service Account'a sahip bir VM'de çalışan uygulama başka VM'ler oluşturabilir, değiştirebilir, silebilir.

**İki yönlü doğası — hem kimlik hem kaynak.** Service Account'lar da yönetilmelidir. Diyelim Alice hangi Google hesaplarının Service Account gibi davranabileceğini yönetmeli, Bob ise sadece Service Account'ların listesini görmeli. Güzel haber: Service Account bir kimlik olmanın yanında **bir kaynaktır** da; dolayısıyla kendisine IAM politikaları eklenebilir. Böylece Alice'e Service Account'ta editor rolü, Bob'a viewer rolü verirsin — tıpkı başka bir kaynakta olduğu gibi.

> **Kavramsal düğüm:** Service Account hem "erişim yapan" (identity) hem de "üzerine erişim tanımlanan" (resource) olabilir. Bu ikili doğayı anlamak önemlidir.

## Cloud Identity

Yeni müşteriler genellikle Google Cloud konsoluna bir **Gmail hesabıyla** girer ve ekip arkadaşlarıyla **Google Grupları** üzerinden çalışır. Başlangıç için kolaydır ama sonradan sorun çıkarır: Kimlikler **merkezî olarak yönetilmez.** Biri kuruluştan ayrıldığında, onun bulut kaynaklarına erişimini anında kaldırmanın kolay yolu yoktur.

**Cloud Identity** bunu çözer. Kuruluşlar, kullanıcı ve gruplarını **Google Admin Console** üzerinden politikalarla yönetir. Yöneticiler, mevcut Active Directory veya LDAP sistemlerindeki aynı kullanıcı adı ve parolayla Google Cloud kaynaklarını yönetebilir. Biri ayrıldığında yönetici, Admin Console'dan hesabını devre dışı bırakır ve gruplardan çıkarır.

Cloud Identity **ücretsiz** ve **premium** (mobil cihaz yönetimi ekleyen) sürümlerde gelir. Zaten Google Workspace müşterisiysen bu işlev sende hazırdır.

## Google Cloud'a erişimin dört yolu

Google Cloud ile etkileşmenin dört yolu vardır. Hepsini bilmen gerekir çünkü hangi işi hangi araçla yapacağın sınavda ve pratikte sorulur.

**1. Google Cloud Console.** Grafik arayüz (GUI). Web tabanlıdır; kaynakları dağıtır, ölçekler, üretim sorunlarını teşhis edersin. Kaynaklarını bulur, sağlıklarını kontrol eder, tam yönetim kontrolüne sahip olur ve bütçe kurabilirsin. Ayrıca arama olanağı ve **tarayıcıdan SSH ile örneklere bağlanma** imkânı sunar.

**2. Google Cloud SDK ve Cloud Shell.**

- **Cloud SDK:** Kaynakları ve uygulamaları yönetmek için bir araç setidir. İçinde **gcloud CLI** (ana komut satırı arayüzü) ve **bq** (BigQuery için komut satırı aracı) bulunur. Kurulduğunda tüm araçlar `bin` dizini altında yer alır.
- **Cloud Shell:** Tarayıcıdan doğrudan komut satırı erişimi verir. **Debian tabanlı bir VM'dir**, kalıcı **5 GB'lık home dizini** vardır. İçinde gcloud ve diğer araçlar hep kurulu, güncel ve tam kimlik doğrulanmış gelir — hiçbir kurulum yapman gerekmez.

**3. API'ler.** Google Cloud'u oluşturan servisler API sunar; böylece yazdığın kod onları kontrol edebilir. Konsoldaki **Google APIs Explorer** hangi API'lerin, hangi sürümlerde mevcut olduğunu gösterir ve onları etkileşimli deneyebilirsin. Sıfırdan kod yazmak zorunda değilsin: Google, **Cloud Client Libraries** ve **Google API Client Libraries** sağlar. Desteklenen diller: Java, Python, PHP, C#, Go, Node.js, Ruby, C++.

**4. Google Cloud mobil uygulaması (app).** Compute Engine örneklerini başlatır, durdurur, SSH ile bağlanır ve loglarını görürsün. Cloud SQL örneklerini durdurup başlatabilir; App Engine uygulamalarını yönetebilir (hataları görme, dağıtımı geri alma, trafik bölme değiştirme). Güncel faturalandırma bilgisi ve bütçe uyarıları verir; CPU kullanımı, ağ kullanımı, saniyedeki istek, sunucu hataları gibi metrikler için özelleştirilebilir grafikler sunar; uyarı ve olay yönetimi içerir. İndirme: `cloud.google.com/app`.

---

# BÖLÜM 3 — Compute Engine ve Sanal Ağ

## VPC (Virtual Private Cloud)

Çoğu kullanıcı Google Cloud'a ya kendi VPC'sini tanımlayarak ya da varsayılan VPC ile başlar. Peki VPC nedir?

**VPC**, bir genel bulutun (Google Cloud gibi) **içinde barınan, güvenli, size özel, izole bir bulut bilişim modelidir.** Bir VPC'de kod çalıştırır, veri saklar, web sitesi barındırır — sıradan bir özel bulutta yapabileceğin her şeyi yaparsın — ama bu özel bulut uzaktan, bir genel bulut sağlayıcısı tarafından barındırılır. Yani VPC, genel bulutun ölçeklenebilirliği ve kolaylığı ile özel bulutun veri izolasyonunu **birleştirir**.

VPC ağları, Google Cloud kaynaklarını hem birbirine hem de internete bağlar. Bu; ağları bölümleme, örneklere erişimi kısıtlamak için güvenlik duvarı kuralları, trafiği belirli hedeflere yönlendiren statik rotalar gibi işleri kapsar.

### Yeni kullanıcıları şaşırtan gerçek: VPC ağları küreseldir (global)

Çoğu yeni kullanıcı buna şaşırır: **Google VPC ağları geneldir.** Dünyadaki herhangi bir Google Cloud bölgesinde **alt ağlar (subnet)** olabilir. Alt ağ, daha büyük ağın bölümlenmiş bir parçasıdır ve bir bölgeyi oluşturan **zonları kapsayabilir**.

Bu neden önemli? Çünkü:

- Küresel kapsamlı ağ düzenlerini kolayca tanımlarsın.
- Kaynaklar aynı alt ağda ama **farklı zonlarda** olabilir.
- Bir alt ağın boyutunu, ona ayrılan IP adresi aralığını genişleterek büyütebilirsin ve bu, **zaten yapılandırılmış VM'leri etkilemez.**

Somut örnek: `vpc1` adlı bir VPC ağın olsun, `asia-east1` ve `us-east1` bölgelerinde iki alt ağı bulunsun. VPC'ye üç Compute Engine VM'i bağlıysa ve bunlar aynı alt ağdaysa, **farklı zonlarda olsalar bile "komşu" sayılırlar.** Bu, hem kesintilere dayanıklı hem de basit ağ düzenine sahip çözümler kurmanı sağlar.

> **Analoji:** VPC'yi bir şehir gibi düşün; alt ağlar (subnet) o şehrin mahalleleri, güvenlik duvarı kuralları ise mahallelere giriş çıkışı denetleyen güvenlik kapılarıdır.

## Compute Engine — Google Cloud'un IaaS çözümü

Compute Engine ile Google altyapısında **VM'ler** oluşturup çalıştırırsın. Peşin yatırım yoktur; hızlı ve tutarlı performans için tasarlanmış bir sistemde binlerce sanal CPU çalışabilir.

Her VM, tam teşekküllü bir işletim sisteminin gücünü ve işlevini taşır. Yani fiziksel bir sunucu gibi yapılandırırsın: ne kadar CPU gücü, ne kadar bellek, ne kadar ve ne tür depolama, hangi işletim sistemi.

- **Nasıl oluşturulur?** Google Cloud Console, gcloud CLI veya Compute Engine API ile.
- **İşletim sistemi:** Google'ın sağladığı Linux ve Windows Server imajları ya da bunların özelleştirilmiş sürümleri. Başka işletim sistemlerinin imajlarını da kurup çalıştırabilirsin.

**Cloud Marketplace.** Hızlı başlangıç için Google'ın ve üçüncü tarafların sunduğu hazır çözümlerdir. Yazılımı, VM örneklerini, depolamayı ya da ağ ayarlarını elle yapılandırmana gerek kalmaz (gerekirse başlatmadan önce çoğu değiştirilebilir). Çoğu paket, normal Google Cloud kullanım ücretinin ötesinde ek ücret istemez; bazıları (özellikle ticari lisanslı üçüncü taraf yazılımları) kullanım ücreti alır ama başlatmadan önce **aylık tahmini maliyeti gösterir.**

### Compute Engine fiyatlandırması — dört önemli mekanizma

Bunları iyi öğren; hem sınavda hem maliyet optimizasyonunda karşına çıkar.

1. **Saniye bazlı faturalandırma, bir dakika minimum.** VM'ler saniye bazında faturalanır, en az bir dakika sayılır.

2. **Sürekli kullanım indirimleri (sustained-use discounts).** Ayın **%25'inden fazla** çalışan her VM için, sonraki her dakikaya otomatik indirim uygulanır. Kendiliğinden çalışır.

3. **Taahhütlü kullanım indirimleri (committed-use discounts).** Kararlı ve öngörülebilir iş yükleri için belirli miktarda vCPU ve belleği **1 yıl veya 3 yıllık** kullanım taahhüdüyle satın alırsın; karşılığında normal fiyattan **%57'ye varan** indirim alırsın.

4. **Spot VM'ler.** İnsan gözetimi gerektirmeyen iş yükleri (örneğin büyük bir veri kümesini analiz eden toplu iş / batch job) için mükemmeldir. Standart fiyata göre **%90'a varan** tasarruf sağlar. Eski "Preemptible" modelinden farklı olarak **maksimum çalışma süresi yoktur**; kapasite mevcut oldukça çalışabilir. Bedeli şu: Google, kaynaklar başka yerde gerekirse örneği **sonlandırma hakkını** saklı tutar. Bu yüzden iş yükün, ilerlemesini kaybetmeden **durdurulup yeniden başlatılabilir** olmalıdır.

**Depolama.** Yüksek verim için özel bir seçenek ya da makine tipi gerekmez; işlemci ile kalıcı diskler arasındaki yüksek verim **varsayılandır ve ek ücreti yoktur.**

**Özel makine tipleri.** Örneklerinin özelliklerini — vCPU sayısı, bellek miktarı — önceden tanımlı makine tiplerinden seçebilir ya da kendi özel makine tipini oluşturabilirsin. Böylece sadece ihtiyacın olan kadarını ödersin.

> **Karar rehberi (fiyat modelleri):** Öngörülemeyen, kesintiye dayanıklı iş → **Spot VM**. Kararlı, uzun vadeli iş → **committed-use**. Genel kullanım → saniye bazlı + otomatik sustained-use indirimi.

## Otomatik ölçekleme (autoscaling)

Compute Engine'in **Autoscaling** özelliği, yük metriklerine göre uygulamaya VM ekler ya da çıkarır. İşin diğer yarısı gelen trafiği VM'ler arasında **dengelemektir** (load balancing — birazdan).

Çok büyük VM'ler de yapılandırabilirsin (bellek içi veritabanları, CPU yoğun analizler için harika) ama çoğu müşteri **yukarı (up) değil, dışa (out) ölçeklenerek** başlar. Yani tek dev makine yerine çok sayıda orta makine. VM başına maksimum CPU sayısı, makinenin "makine ailesine" bağlıdır ve kullanıcının zon bazlı kotasıyla da sınırlanır.

> **Terim ayrımı:** *Scale up* = tek makineyi büyütmek (dikey). *Scale out* = makine sayısını artırmak (yatay). Bulutun doğal eğilimi scale out'tur.

## VPC uyumluluk özellikleri

Fiziksel ağlara benzer şekilde VPC'lerin de özellikleri vardır — ama çoğunu sen kurmak/yönetmek zorunda değilsin.

**Yönlendirme tabloları (routing tables).** Yerleşiktir; bir yönlendirici (router) sağlaman ya da yönetmen gerekmez. Trafiği bir örnekten diğerine — aynı ağ içinde, alt ağlar arasında, hatta zonlar arasında — **harici IP adresi gerektirmeden** iletir.

**Güvenlik duvarı (firewall).** VPC'ler **küresel dağıtık bir güvenlik duvarı** sağlar; gelen ve giden trafiği kısıtlamak için kontrol edebilirsin. Bunu da sağlaman/yönetmen gerekmez. Kuralları **ağ etiketleri (network tags)** ile tanımlarsın — çok kullanışlı. Örneğin tüm web sunucularını `WEB` diye etiketler, sonra "80 veya 443 portundaki trafik, IP adresi ne olursa olsun tüm `WEB` etiketli VM'lere girebilir" diye bir kural yazarsın.

**VPC Peering.** Şirketinin birden fazla projesi varsa ve VPC'lerin birbiriyle konuşması gerekiyorsa, iki VPC arasında **VPC Peering** ilişkisi kurup trafiği değiştirirsin.

**Shared VPC.** Bir projedeki neyin, başka bir projedeki VPC ile etkileşebileceğini IAM'in tüm gücüyle kontrol etmek istersen Shared VPC yapılandırırsın. (Özet kapanışta bu, "daha az ağ yönetimi" sağlayan özellikler arasında sayılır.)

## Cloud Load Balancing

Sorun şu: Uygulaman bir an 4 VM, başka bir an 40 VM ile sunuluyorsa, müşterin ona nasıl ulaşacak? Cevap: **Cloud Load Balancing.**

Load balancer'ın görevi, kullanıcı trafiğini bir uygulamanın **birden fazla örneğine dağıtmaktır.** Yükü yayarak performans sorunları riskini azaltır. Cloud Load Balancing:

- **Tam dağıtık, yazılım tanımlı, yönetilen** bir servistir. VM'lerde çalışmadığı için ölçeklemesi ya da yönetimiyle uğraşmazsın.
- Her tür trafiğin önüne konabilir: HTTP/HTTPS, diğer TCP ve SSL trafiği, UDP trafiği.
- **Bölgeler arası load balancing** ve **otomatik çoklu bölge yük devri (failover)** sağlar; arka uçlar sağlıksızlaşırsa trafiği yavaşça kesirler halinde kaydırır.
- Kullanıcı, trafik, ağ ve arka uç sağlığındaki değişimlere hızla tepki verir.
- **Ön ısıtma (pre-warming) gerektirmez.** Ani talep patlaması beklesen bile Google'a önceden haber vermek için destek talebi açman gerekmez.

### Load balancer türleri (OSI katmanına göre)

Google Cloud, load balancer'ları çalıştıkları **OSI katmanına** ve işlevlerine göre sınıflandırır.

**Application Load Balancer.**
- **Uygulama katmanında** çalışır, HTTP ve HTTPS trafiği içindir.
- İçerik tabanlı yönlendirme ve SSL/TLS sonlandırma gibi gelişmiş özellikler ister web uygulamaları için idealdir.
- **Ters proxy (reverse proxy)** olarak çalışır; gelen trafiği tanımladığın kurallara göre birden çok arka uca dağıtır.
- Hem internete açık (external) hem dahili (internal) yapılandırılabilir.

**Network Load Balancer.**
- **Taşıma katmanında** çalışır; TCP, UDP ve diğer IP protokollerini verimli işler.
- İki alt türü vardır:
  - **Proxy Network Load Balancer:** Ters proxy gibi davranır; istemci bağlantısını sonlandırıp arka uca yeni bağlantı kurar. Gelişmiş trafik yönetimi sunar; hem on-premises hem çeşitli bulut ortamlarındaki arka uçları destekler.
  - **Passthrough Network Load Balancer:** Bağlantıyı **değiştirmez ya da sonlandırmaz.** Trafiği doğrudan arka uca iletir ve **kaynak IP adresini korur.** Doğrudan sunucu dönüşü (direct server return) gereken ya da daha geniş IP protokol yelpazesi işleyen uygulamalar için uygundur.

> **Sınav ayrımı:** *HTTP(S), içerik tabanlı yönlendirme* → Application LB. *TCP/UDP* → Network LB. *Bağlantıyı sonlandırıyor mu?* Proxy = evet, Passthrough = hayır (kaynak IP korunur).

## Cloud DNS ve Cloud CDN

**DNS nedir?** İnternet ana bilgisayar adlarını (hostname) adreslere çevirir. Google'ın en ünlü ücretsiz servislerinden biri **8.8.8.8**'dir; dünyaya açık bir Public DNS sunar.

**Cloud DNS.** Peki Google Cloud'da kurduğun uygulamaların hostname ve adreslerini dünya nasıl bulacak? Cloud DNS ile. Bu, Google ile aynı altyapıda çalışan **yönetilen bir DNS servisidir**; düşük gecikme, yüksek kullanılabilirlik ve uygun maliyet sağlar. Yayınladığın DNS bilgisi dünya çapında yedekli konumlardan sunulur. **Programlanabilir**dir: milyonlarca DNS zone ve kaydını konsol, komut satırı veya API ile yönetebilirsin.

**Cloud CDN (Content Delivery Network).** Google'ın küresel bir **kenar önbelleği (edge cache)** sistemi vardır. Kenar önbelleği, içeriği son kullanıcıya daha yakın önbellek sunucularında saklamak demektir. Cloud CDN ile içerik dağıtımını hızlandırırsın; sonuç:

- Müşteriler **daha düşük ağ gecikmesi** yaşar.
- İçeriğinin kaynağı (origin) **daha az yük** görür.
- Hatta para tasarrufu edersin.

Bir Application Load Balancer kurulduktan sonra Cloud CDN **tek bir onay kutusuyla** etkinleştirilir. Zaten başka bir CDN kullanıyorsan, muhtemelen Google'ın **CDN Interconnect** ortak programının parçasıdır ve kullanmaya devam edebilirsin.

## Ağları birbirine bağlama (hybrid ve multi-cloud connectivity)

Birçok müşteri, Google VPC'sini kendi sistemindeki başka ağlara — on-premises (şirket içi) ağlara ya da başka bulutlardaki ağlara — bağlamak ister. Birkaç etkili yol vardır. Bunları teker teker anlamak, "hangi durumda hangisi" sorusunun cevabıdır.

**1. Cloud VPN (+ Cloud Router).** İnternet üzerinden bir sanal özel ağ (VPN) bağlantısıyla başlar; **Cloud VPN** bir "tünel" bağlantısı kurar. Bağlantıyı dinamik yapmak için **Cloud Router** kullanılır. Cloud Router, diğer ağlarla Google VPC arasında **BGP (Border Gateway Protocol)** üzerinden rota bilgisi değiştirir. Böylece Google VPC'ne yeni bir alt ağ eklersen, on-premises ağın ona giden rotaları **otomatik olarak** alır.

İnternet üzerinden bağlanmak her zaman en iyisi değildir (güvenlik kaygısı ya da bant genişliği güvenilirliği yüzünden). Bu yüzden başka seçenekler var:

**2. Direct Peering (doğrudan eşleme).** Bir yönlendiriciyi Google'ın bir **varlık noktasıyla (point of presence, PoP)** aynı genel veri merkezine koyup ağlar arasında trafik değişimi yaparsın. Google'ın dünya çapında PoP'ları var.

**3. Carrier Peering.** Bir PoP'ta yoksan, **Carrier Peering** programındaki bir ortakla çalışıp bağlanırsın. Bir servis sağlayıcının ağı üzerinden on-premises ağından Google Workspace'e ve bir/birden fazla genel IP ile ifşa edilebilen Google Cloud ürünlerine doğrudan erişim verir.

> **Peering'in bir dezavantajı:** Bir Google **SLA'sı (Hizmet Seviyesi Anlaşması)** kapsamında değildir. En yüksek çalışma süreleri (uptime) önemliyse aşağıdaki interconnect seçeneklerine bak.

**4. Dedicated Interconnect.** Google'a bir veya daha fazla **doğrudan, özel bağlantı** kurar. Bağlantı topolojileri Google'ın spesifikasyonlarını karşılıyorsa **%99,99'a varan SLA** kapsamına girebilir. Daha da güvenilirlik için bir VPN ile yedeklenebilir.

**5. Partner Interconnect.** On-premises ağ ile VPC ağı arasında **desteklenen bir servis sağlayıcı üzerinden** bağlantı sağlar. Şu durumlarda kullanışlı: Veri merkezin bir Dedicated Interconnect colocation tesisine erişemeyen bir konumdaysa, ya da veri ihtiyacın tam 10 Gbps'lik bir bağlantıyı hak etmiyorsa. Kullanılabilirlik ihtiyacına göre kritik servisleri ya da bir miktar kesintiyi tolere edebilen uygulamaları destekleyecek şekilde yapılandırılır. Topoloji uygunsa **%99,99'a varan SLA** kapsamına girer — ancak Google, üçüncü taraf sağlayıcının sunduğu Partner Interconnect kısmından ya da kendi ağı dışındaki sorunlardan **sorumlu değildir.**

**6. Cross-Cloud Interconnect.** Google Cloud ile **başka bir bulut sağlayıcı** arasında yüksek bant genişlikli özel bağlantı kurar. Google, iki ağ arasında özel bir fiziksel bağlantı sağlar; Google VPC'ni başka bir bulutta barınan ağınla eşlersin. **Çoklu bulut (multicloud)** stratejisini destekler; daha az karmaşıklık, site-to-site veri transferi ve şifreleme sunar. İki boyutta gelir: **10 Gbps veya 100 Gbps.**

### Hangi ağ seçeneği? Üç soruyla karar ver

Doğru seçenek uygulamana ve iş gereksinimlerine bağlıdır. Şu üç soruyu cevapla:

1. Özel adreslemeli (private addressing) on-premises sunucuların/kullanıcı bilgisayarların, özel adreslemeli Google Cloud kaynaklarına bağlanmak zorunda mı?
2. Google servislerine mevcut bağlantının bant genişliği ve performansı iş gereksinimlerini karşılıyor mu?
3. Google'ın bir PoP konumunda erişim/yönlendirme ekipmanı kurup yönetmeye zaten sahip misin ya da istekli misin?

Buna göre:

| Durum | Önerilen |
| --- | --- |
| Private-to-private lazım **ve** internet bağlantın yeterli | **Cloud VPN** |
| Private erişim gerekmiyor **ve** internet bağlantın yeterli | Genel IP'lerle doğrudan Google servislerine bağlan |
| Private gerekmiyor **ama** bağlantı yetersiz | **Peering** |
| PoP'ta ayak izin var / colocation kiralayıp ekipman kurmaya istekli | **Direct Peering** |
| Ekipman kuramıyorsun / sağlayıcıyla çalışmak istiyorsun (peering için) | **Carrier Peering** |
| Private + yüksek performans lazım ama ekipman kuramıyorsun / sağlayıcı tercih ediyorsun | **Partner Interconnect** |
| Google'a private, özel devre istiyorsun + PoP'ta ekipman kuruyorsun | **Dedicated Interconnect** |

---

# BÖLÜM 4 — Depolama Seçenekleri

Her uygulama veri saklar — akıtılacak medya, cihazlardan gelen sensör verisi, kullanıcı kayıtları... Ama farklı uygulamalar farklı depolama/veritabanı çözümleri ister. Google Cloud; yapılandırılmış (structured), yapılandırılmamış (unstructured), işlemsel (transactional) ve ilişkisel (relational) veri için seçenekler sunar. Bu bölümde **beş çekirdek depolama ürününü** öğreneceğiz: **Cloud Storage, Cloud SQL, Spanner, Firestore, Bigtable.** Uygulamana göre birini ya da birkaçını birlikte kullanabilirsin.

## Cloud Storage — Nesne depolama

Cloud Storage, **dayanıklı ve yüksek erişilebilir nesne depolama (object storage)** sunar. Ama önce: nesne depolama nedir?

**Nesne depolama**, veriyi bir dosya-klasör hiyerarşisi (file storage) ya da diskin parçaları (block storage) olarak değil, **"nesneler" (objects)** olarak yöneten bir mimaridir. Her nesne paketlenmiş bir formatta saklanır ve şunları içerir:

- Verinin gerçek ikili (binary) hali,
- İlgili meta veri (oluşturulma tarihi, yazar, kaynak türü, izinler),
- **Küresel olarak benzersiz bir tanımlayıcı.**

Bu benzersiz anahtarlar **URL biçimindedir**, dolayısıyla nesne depolama web teknolojileriyle çok iyi çalışır. Nesne olarak sıkça saklanan veriler: video, fotoğraf, ses kayıtları.

**Cloud Storage ne için kullanılır?** İstediğin kadar veriyi saklar ve gerektikçe geri alırsın. Tam yönetilen, ölçeklenebilir bir servistir. Tipik kullanımlar:

- Web sitesi içeriği sunmak,
- Arşiv ve felaket kurtarma (disaster recovery) için veri saklamak,
- Büyük veri nesnelerini son kullanıcılara doğrudan indirmeyle dağıtmak,
- **BLOB (binary large object)** — video, foto gibi çevrimiçi içerik,
- Yedek/arşiv verisi ve işleme akışlarında ara sonuçları saklamak.

### Bucket'lar (kovalar)

Cloud Storage dosyaları **bucket** adı verilen kaplara organize edilir. Bir bucket:

- **Küresel olarak benzersiz bir isme** ihtiyaç duyar,
- Saklanacağı belirli bir **coğrafi konuma** ihtiyaç duyar. İdeal konum, gecikmenin en aza indiği yerdir. Kullanıcıların çoğu Avrupa'daysa, ya belirli bir Avrupa bölgesini ya da EU çoklu bölgesini seçersin.

### Nesneler değişmezdir (immutable) ve versiyonlama

Cloud Storage nesneleri **değişmezdir (immutable)**; yani onları düzenlemezsin, her değişiklikte **yeni bir versiyon** oluşur. İki seçenek var:

- Her yeni versiyon eskisini **tamamen üzerine yazsın** (varsayılan davranış), ya da
- Bucket'ta **versiyonlamayı (versioning)** açarak her değişikliğin (üzerine yazma veya silme) ayrıntılı geçmişini tut.

Versiyonlama açıkken: arşivlenmiş versiyonları listeleyebilir, bir nesneyi eski haline döndürebilir ya da bir versiyonu kalıcı silebilirsin. Kapalıysa, yeni versiyon her zaman eskisini üzerine yazar.

### Erişim kontrolü: IAM ve ACL

Veri nesnelerinde çoğu zaman kişisel tanımlanabilir bilgi (PII) bulunabilir; bu yüzden erişim kontrolü şarttır. **En az ayrıcalık** ilkesiyle her kullanıcı yalnızca işine yetecek erişime sahip olmalıdır. İki seçenek:

- **IAM** — çoğu amaç için yeterlidir. Roller proje → bucket → nesne şeklinde miras alınır.
- **ACL (Access Control List)** — daha ince kontrol gerekirse. Her ACL iki bilgi taşır:
  - **Scope (kapsam):** Kim erişebilir/işlem yapabilir (belirli kullanıcı ya da grup).
  - **Permission (izin):** Hangi işlem yapılabilir (okuma, yazma...).

### Yaşam döngüsü yönetimi (lifecycle management)

Büyük miktarda nesneyi saklamak pahalı olabilir. Cloud Storage **yaşam döngüsü politikaları** sunar. Örnekler:

- 365 günden eski nesneleri sil,
- 1 Ocak 2013'ten önce oluşturulan nesneleri sil,
- Versiyonlaması açık bir bucket'ta her nesnenin yalnızca **en yeni 3 versiyonunu** tut.

Böylece gereğinden fazla ödemezsin.

### Depolama sınıfları (storage classes) — Çok önemli

Cloud Storage'da dört ana depolama sınıfı vardır. Aradaki fark, **verinin ne sıklıkla erişildiği** ve **maliyettir.**

| Sınıf | En uygun kullanım | Erişim sıklığı | Not |
| --- | --- | --- | --- |
| **Standard** | Sık erişilen ("hot") veri; kısa süreli saklama | Sürekli | En hızlı erişim |
| **Nearline** | Seyrek erişilen veri | ~Ayda bir veya daha az | Yedekler, uzun kuyruk multimedya, arşivleme |
| **Coldline** | Daha da seyrek erişilen veri | ~90 günde bir | Nearline'dan daha ucuz |
| **Archive** | Arşiv, çevrimiçi yedek, felaket kurtarma | Yılda birden az | **En düşük maliyet**; erişim/işlem pahalı, **365 gün minimum saklama** |

Dört sınıfın da **ortak** özellikleri:

- Sınırsız depolama, minimum nesne boyutu şartı yok,
- Dünya çapında erişilebilirlik ve konumlar,
- Düşük gecikme, yüksek dayanıklılık,
- Güvenlik/araçlar/API'lerde tek tip (uniform) deneyim,
- Çoklu bölge veya çift bölgede (dual-region) saklanırsa **coğrafi yedeklilik (geo-redundancy)** — fiziksel sunucular coğrafi olarak farklı veri merkezlerine konur; felaketlere karşı korur ve trafik yük dengelenir.

**Autoclass.** Cloud Storage, her nesnenin erişim desenine göre onu **otomatik olarak uygun sınıfa taşıyan** bir özellik sunar. Erişilmeyen veri daha soğuk sınıflara iner (maliyet düşer), erişilen veri Standard'a geri çıkar (gelecekteki erişimler hızlanır). Maliyet tasarrufunu otomatikleştirir.

**Diğer notlar:** Minimum ücret yoktur — sadece kullandığın kadar ödersin, önceden kapasite ayırmana gerek yok. Güvenlik açısından Cloud Storage veriyi **her zaman sunucu tarafında, diske yazmadan önce şifreler** (ek ücretsiz). Cihazın ile Google arasındaki veri varsayılan olarak **HTTPS/TLS** ile şifrelenir.

### Cloud Storage'a veri taşımanın yolları

Hangi sınıfı seçersen seç, veriyi içeri almanın birkaç yolu vardır:

- **Çevrimiçi transfer:** `gcloud storage` komutuyla (Cloud SDK).
- **Sürükle-bırak:** Cloud Console'da (Google Chrome ile).
- **Storage Transfer Service:** Büyük miktarda çevrimiçi veriyi hızlı ve uygun maliyetle içeri aktarır. Başka bir bulut sağlayıcıdan, farklı bir Cloud Storage bölgesinden veya bir HTTP(S) uç noktasından toplu transferleri zamanlar ve yönetir.
- **Transfer Appliance:** Google Cloud'dan kiraladığın, rafa takılabilir yüksek kapasiteli bir depolama sunucusudur. Ağına takar, veriyle doldurur, bir yükleme tesisine gönderirsin; oradan Cloud Storage'a yüklenir. Tek cihazla **bir petabayta kadar** veri taşırsın. (Terabayt/petabayt ölçeğinde veri için idealdir.)
- **Entegrasyonlar:** BigQuery ve Cloud SQL tabloları içe/dışa aktarılabilir; App Engine logları, Firestore yedekleri, Compute Engine imajları ve başlangıç betikleri gibi nesneler saklanabilir.

## Cloud SQL — Yönetilen ilişkisel veritabanı

Cloud SQL, **tam yönetilen ilişkisel veritabanları** sunar: **MySQL, PostgreSQL, SQL Server.** Amacı, sıkıcı ama gerekli işleri Google'a devretmektir — yama/güncelleme uygulama, yedek yönetimi, replikasyon yapılandırma — böylece sen uygulamaya odaklanırsın.

- Yazılım kurulumu/bakımı gerektirmez.
- **128 işlemci çekirdeğine, 864 GB RAM'e ve 64 TB depolamaya** kadar ölçeklenir.
- **Otomatik replikasyon** senaryolarını destekler (Cloud SQL birincil örneğinden, harici birincil örnekten, harici MySQL örneklerinden).
- **Yönetilen yedekler** — güvenle saklanır; örnek maliyeti **yedi yedeği** kapsar.
- Veriyi Google'ın iç ağlarındayken ve tablolarda/geçici dosyalarda/yedeklerde saklanırken **şifreler.**
- Bir **ağ güvenlik duvarı** içerir; her veritabanı örneğine ağ erişimini denetler.
- Diğer Google Cloud servisleri ve hatta harici servisler tarafından erişilebilir. App Engine ile standart sürücülerle (Java için Connector/J, Python için MySQLdb) kullanılır; Compute Engine örnekleri yetkilendirilebilir (VM ile aynı zonda yapılandırılması önerilir). SQL Workbench, Toad gibi araçlarla standart MySQL sürücüleriyle çalışır.

## Spanner — Yatay ölçeklenen ilişkisel veritabanı

Spanner, **tam yönetilen, yatay ölçeklenen (horizontally scalable), güçlü tutarlı (strongly consistent) ve SQL konuşan** bir ilişkisel veritabanı servisidir. Google'ın kendi kritik uygulamalarında savaş testinden geçmiştir; **Google'ın 80 milyar dolarlık işini** çalıştıran servistir.

Ne zaman Spanner? Şunlara ihtiyacın varsa:

- Join'ler ve ikincil indeksler içeren bir SQL ilişkisel veritabanı yönetim sistemi,
- Yerleşik yüksek kullanılabilirlik,
- **Güçlü küresel tutarlılık,**
- Çok yüksek IOPS (saniyede giriş/çıkış işlemi) — saniyede on binlerce okuma/yazma veya daha fazlası.

> **Cloud SQL mı Spanner mı?** Cloud SQL çoğu web uygulaması için yeterlidir. Ama **yatay ölçeklenebilirliğe** (sadece okuma kopyalarıyla değil) ihtiyacın varsa, Spanner'a geçersin.

## Firestore — NoSQL belge veritabanı

Firestore, **esnek, yatay ölçeklenen bir NoSQL bulut veritabanıdır**; mobil, web ve sunucu geliştirme içindir.

- Veri **dokümanlarda (documents)** saklanır ve **koleksiyonlar (collections)** halinde organize edilir.
- Dokümanlar iç içe geçmiş karmaşık nesneler ve alt koleksiyonlar içerebilir.
- Her doküman bir dizi **anahtar-değer çifti** taşır. Örneğin bir kullanıcı dokümanının `firstname` ve `lastname` anahtarları vardır.
- **NoSQL sorguları** ile tek bir dokümanı ya da bir koleksiyondaki eşleşen tüm dokümanları getirirsin. Sorgular zincirlenmiş filtreler + sıralama içerebilir.
- **Varsayılan olarak indekslidir**; sorgu performansı veri kümesinin değil, **sonuç kümesinin** boyutuyla orantılıdır.

En güçlü yanı: **Veri senkronizasyonu.** Firestore, bağlı her cihazda veriyi günceller. Ayrıca basit, tek seferlik getirmeleri de verimli yapar. Uygulamanın aktif kullandığı veriyi **önbelleğe alır**, böylece cihaz **çevrimdışıyken bile** yazma/okuma/dinleme/sorgulama yapabilirsin. Cihaz tekrar çevrimiçi olunca yerel değişiklikleri geri senkronize eder. Google Cloud altyapısından yararlanır: otomatik çoklu bölge replikasyonu, güçlü tutarlılık, atomik toplu işlemler, gerçek işlem (transaction) desteği.

## Bigtable — NoSQL büyük veri veritabanı

Bigtable, Google'ın **NoSQL büyük veri (big data) veritabanı** servisidir — Search, Analytics, Maps ve Gmail gibi çekirdek Google servislerini çalıştıran veritabanının aynısı. **Tutarlı düşük gecikme ve yüksek verimle devasa iş yüklerini** işlemek için tasarlanmıştır; hem operasyonel hem analitik uygulamalar için harikadır (IoT, kullanıcı analizi, finansal veri analizi).

Müşteriler genelde şu durumlarda Bigtable seçer:

- **1 TB'tan fazla** yarı yapılandırılmış veya yapılandırılmış veriyle çalışıyorsan,
- Veri hızlı, yüksek verimli veya hızla değişiyorsa,
- NoSQL veriyle çalışıyorsan (güçlü ilişkisel semantik gerekmiyor),
- Veri zaman serisi (time-series) ya da doğal anlamsal sıralamaya sahipse,
- Büyük veride asenkron toplu veya senkron gerçek zamanlı işleme yapıyorsan,
- Veri üzerinde makine öğrenmesi algoritmaları çalıştırıyorsan.

**Entegrasyon.** API'lerle veri, bir veri servis katmanı (Managed VMs, HBase REST Server ya da HBase istemcili Java sunucusu) üzerinden okunur/yazılır. Tipik olarak uygulamalara, panolara (dashboards) veri sunar. Veri; Dataflow Streaming, Spark Streaming, Storm gibi akış çerçeveleriyle akıtılabilir; akış mümkün değilse Hadoop MapReduce, Dataflow veya Spark gibi toplu süreçlerle okunur/yazılır. Sıklıkla özetlenmiş/yeni hesaplanmış veri Bigtable'a ya da alt akıştaki bir veritabanına geri yazılır.

## Depolama seçeneklerinin karşılaştırması — Hangisini seçmeli?

Bu tablo, "hangi servis hangi iş için" sorusunun özüdür. Sınavda buradan çok soru gelir.

| İhtiyaç | Servis | Kapasite | Maksimum birim | En iyi kullanım |
| --- | --- | --- | --- | --- |
| 10 MB'tan büyük değişmez blob (büyük resim, film) | **Cloud Storage** | Petabaytlar | 5 TB / nesne | Medya, yedek, arşiv |
| OLTP için tam SQL desteği | **Cloud SQL** | 64 TB'a kadar (makineye göre) | — | Web çerçeveleri, mevcut uygulamalar, kullanıcı kimlikleri, siparişler |
| OLTP + **yatay ölçeklenebilirlik** | **Spanner** | Petabaytlar | — | Cloud SQL yetmediğinde; küresel, güçlü tutarlı |
| Devasa ölçek + gerçek zamanlı sorgu + çevrimdışı destek | **Firestore** | Terabaytlar | 1 MB / varlık (entity) | Mobil/web uygulamaları için veri saklama, senkron, sorgu |
| Çok sayıda yapılandırılmış nesne, ağır okuma/yazma | **Bigtable** | Petabaytlar | 10 MB / hücre, 100 MB / satır | Analitik veri: AdTech, finans, IoT |

Önemli notlar:

- **Bigtable SQL sorgularını ve çok satırlı işlemleri (multi-row transactions) desteklemez.**
- Uygulamana göre birini ya da birkaçını birlikte kullanabilirsin.
- **BigQuery neden yok?** Çünkü BigQuery, veri depolama ile veri işleme arasında bir sınırda durur. Onu genelde büyük veri analizi ve etkileşimli sorgulama için kullanırsın; saf bir depolama ürünü değildir. Bu yüzden bu bölümde anlatılmaz (başka kurslarda derinlemesine işlenir).

> **Karar sezgisi:** Önce sor — *İlişkisel (SQL) mı, NoSQL mi?* İlişkisel ve tek bölge yeter → Cloud SQL; küresel + yatay ölçek → Spanner. NoSQL ve mobil/senkron → Firestore; NoSQL ve büyük veri/analitik → Bigtable. Yapılandırılmamış blob → Cloud Storage.

---

# BÖLÜM 5 — Konteynerler ve Kubernetes

## Konteyner neden var? IaaS ve PaaS arasındaki boşluk

Önce sorunu görelim. **IaaS**, VM'lerle donanımı sanallaştırır. Her geliştirici kendi işletim sistemini kurar, donanıma erişir, kendi içine kapalı ortamında uygulamasını inşa eder (RAM, dosya sistemi, ağ arayüzleri...). Çok esnektir — istediğin runtime'ı, web sunucusunu, veritabanını kurarsın.

Ama bu esnekliğin bir bedeli var: **En küçük işlem birimi, kendi VM'iyle birlikte bir uygulamadır.** Misafir işletim sistemi (guest OS) gigabaytlarca olabilir ve açılması dakikalar sürer. Talep artınca, her yeni örnek için **tüm VM'i kopyalayıp guest OS'u yeniden açman** gerekir — yavaş ve pahalı.

**PaaS** tarafında ise iş yüklerini bağımsız ölçekleyebilirsin ama OS ve donanım soyutlanmıştır; o kadar esnek değildir.

İşte **konteyner** tam bu boşluğu doldurur: PaaS'ın bağımsız ölçeklenebilirliğini, IaaS'ın OS/donanım soyutlamasını **birleştirir.**

### Konteyner tam olarak nedir?

Konteyner, kodunun ve bağımlılıklarının etrafındaki **görünmez bir kutudur**; dosya sisteminin ve donanımın kendi bölümüne sınırlı erişimi vardır. Özellikleri:

- Oluşturmak için sadece **birkaç sistem çağrısı** gerekir; bir işlem (process) kadar hızlı başlar.
- Her ana bilgisayarda (host) gereken tek şey: konteynerleri destekleyen bir **OS kernel** ve bir **konteyner çalışma zamanı (runtime).**
- Özünde **işletim sistemi sanallaştırılır** (VM'de donanım sanallaştırılırken).
- **PaaS gibi ölçeklenir ama neredeyse IaaS kadar esneklik verir.**

Sonuç: Kod **ultra taşınabilir** olur; OS ve donanım bir kara kutu (black box) gibi ele alınır. Geliştirmeden test ortamına (staging), oradan üretime (production), ya da laptop'tan buluta — **hiçbir şeyi değiştirmeden ya da yeniden inşa etmeden** geçersin.

> **Analoji:** Docker imajı bir **tarif**, konteyner o tariften pişmiş **yemektir.** Aynı tariften her yerde aynı yemeği çıkarırsın.

### Konteynerlerle ölçekleme ve mikroservisler

Bir web sunucusunu ölçeklemek istediğini düşün. Konteynerle bunu **saniyeler içinde** yapar, tek bir host'ta onlarca ya da yüzlerce konteyner çalıştırırsın. Ama gerçek güç şurada: Uygulamanı **çok sayıda konteynerle** kurarsın; her biri kendi işlevini görür — tıpkı **mikroservisler** gibi. Bunları ağ bağlantılarıyla birbirine bağlarsan:

- Modüler olurlar,
- Kolay dağıtılırlar,
- Bir grup host boyunca **bağımsız ölçeklenirler.**

Host'lar talep değiştikçe ya da bazıları çökünce yukarı/aşağı ölçeklenir, konteynerleri başlatır/durdurur. **GKE**, bu iki modelin arasında köprü kurar: PaaS'ın otomasyonunu IaaS'ın ayrıntılı kontrolüyle birleştirir.

## Kubernetes — Konteyner orkestrasyonu

Konteynerlerin yönetimi ve ölçeklenmesi elle yapılamayacak kadar karmaşıklaşınca **Kubernetes** devreye girer. Kubernetes, **konteynerli iş yüklerini ve servisleri yönetmek için açık kaynaklı bir platformdur.** Zaman ve emek kazanmak için **GKE (Google Kubernetes Engine)** ile başlatılabilir (bootstrap).

Kubernetes ne yapar? Çok sayıda host üzerindeki çok sayıda konteyneri orkestralar, onları mikroservisler olarak ölçekler, dağıtımları (rollout) ve geri almaları (rollback) kolaylaştırır.

En üst düzeyde Kubernetes, **konteynerleri bir dizi düğüm (node) üzerine dağıtmak için kullandığın bir API kümesidir.** Bu düğüm kümesine **cluster** denir. Sistem ikiye ayrılır:

- **Control plane (kontrol düzlemi)** olarak çalışan birincil bileşenler,
- Konteynerleri çalıştıran **node'lar.**

> **Terim tuzağı:** Kubernetes'te bir **node**, bir hesaplama örneğini (makine) temsil eder. Bu, Google Cloud'daki bir "node"dan farklıdır — Google Cloud'da node, Compute Engine'de çalışan bir VM'dir.

Kubernetes'in temel felsefesi: Sen uygulamaların kümesini ve nasıl etkileşeceklerini **tanımlarsın**, Kubernetes bunu nasıl gerçekleştireceğine **kendisi karar verir.**

### Temel Kubernetes kavramları

**Pod.** Node'lar üzerinde konteyner dağıtmak, bir veya daha fazla konteynerin etrafına bir **sarmalayıcı (wrapper)** koymak demektir; işte bu bir **Pod**'u tanımlar.

- Pod, Kubernetes'te oluşturabileceğin/dağıtabileceğin **en küçük birimdir.**
- Cluster'ında çalışan bir süreci temsil eder — uygulamanın bir bileşeni ya da tamamı olabilir.
- Genelde **Pod başına bir konteyner** olur; ama sıkı bağımlılığı olan birden çok konteyner varsa, onları tek bir Pod'da paketleyip ağ ve depolamayı paylaştırabilirsin.
- Pod, konteynerlerine **benzersiz bir ağ IP'si** ve bir dizi port sağlar.

**Deployment.** Bir Pod'u çalıştırmanın bir yolu `kubectl run` komutudur; bu, içinde konteyner çalışan bir Pod'la birlikte bir **Deployment** başlatır.

- Deployment, **aynı Pod'un kopyalarının (replicas) bir grubunu** temsil eder.
- Çalıştığı node'lar çökse bile Pod'larını **çalışır durumda tutar.**
- Bir uygulama bileşenini ya da tüm uygulamayı temsil edebilir.
- Çalışan Pod'ları görmek için: `kubectl get pods`.

**Service.** Deployment'lar Pod oluşturup yok ettikçe, Pod'lar kendi IP adreslerini alır ama bu adresler **zamanla sabit kalmaz.** Bu bir sorun: Pod'un adresi değişirse ona nasıl güvenilir şekilde ulaşırsın? Çözüm **Service**'tir.

- Kubernetes, Pod'ların için **sabit IP adresli bir Service** oluşturur.
- Service, bir dizi Pod'un **mantıksal kümesini** ve onlara erişim politikasını tanımlayan bir soyutlamadır.
- Pod'lara **kararlı bir uç nokta (stable endpoint / fixed IP)** sağlar.
- Örnek: `frontend` ve `backend` diye iki Pod kümen olsun, her biri kendi Service'inin arkasında. Backend Pod'ları değişse bile, frontend bunu fark etmez; sadece **backend Service'ine** başvurur.

Bir denetleyici (controller) şöyle der: "Bu Service'e, dışarıdan erişilebilsin diye genel IP'li bir **harici load balancer** eklemem gerek." **GKE'de bu load balancer bir Network Load Balancer olarak** oluşturulur. O IP'ye ulaşan her istemci, Service arkasındaki bir Pod'a yönlendirilir.

**Ölçekleme.** Bir Deployment'ı ölçeklemek için `kubectl scale` komutunu çalıştırırsın. Örneğin üç Pod oluşturulur, hepsi Service'in arkasına konur ve tek bir sabit IP'yi paylaşır. Otomatik ölçekleme de kullanabilirsin — örneğin "CPU kullanımı belirli bir sınıra ulaşınca Pod sayısı artsın."

### İmperatif vs. Deklaratif

Şimdiye kadar `expose` ve `scale` gibi **imperatif (buyurgan)** komutlar gördük. Bunlar öğrenmek ve adım adım test için iyidir. Ama Kubernetes'in gerçek gücü **deklaratif (bildirimsel)** çalışmaktan gelir.

- **İmperatif:** "Şunu yap, bunu yap" diye komut verirsin.
- **Deklaratif:** Kubernetes'e **istediğin son durumu (desired state)** anlatan bir yapılandırma dosyası verirsin; nasıl yapacağını Kubernetes bulur.

Bunu bir **Deployment yapılandırma dosyasıyla** yaparsın.

- Doğru sayıda replika çalışıyor mu kontrol: `kubectl get deployments` veya `kubectl describe deployments`.
- Üçten beşe çıkarmak için: config dosyasını güncelle, sonra `kubectl apply` ile uygula.
- Service'in harici IP'sini almak için: `kubectl get services`.

**Güncelleme (rollout).** Uygulamanın yeni sürümünü kullanıcılara vermek istersin ama tüm değişiklikleri **aynı anda** yaymak risklidir. Bu yüzden `kubectl rollout` kullanır ya da deployment config dosyanı değiştirip `kubectl apply` ile uygularsın. Yeni Pod'lar, belirlediğin **güncelleme stratejisine** göre oluşturulur. Örneğin: her seferinde yeni sürüm Pod'unu tek tek oluştur ve **yeni Pod hazır olmadan eski Pod'u yok etme** — böylece kesinti yaşanmaz.

## GKE — Google Kubernetes Engine

GKE, bulutta **Google tarafından barındırılan, yönetilen bir Kubernetes servisidir.** Ortam, bir cluster oluşturmak için gruplanan birden çok makineden — özellikle **Compute Engine örneklerinden** — oluşur.

**GKE, Kubernetes'ten nasıl farklı?** Kullanıcı açısından çok daha basit:

- GKE, **control plane bileşenlerinin hepsini** senin yerine yönetir.
- Kubernetes API isteklerini göndereceğin bir IP adresi yine sunulur, ama arkasındaki tüm control plane altyapısını **GKE sağlar ve yönetir.**
- Ayrı bir control plane kurma ihtiyacını ortadan kaldırır.

### İki mod: Autopilot ve Standard

Node yapılandırması ve yönetimi, kullandığın GKE moduna bağlıdır.

**Autopilot (önerilen).** GKE, altta yatan altyapıyı senin yerine yönetir: node yapılandırması, otomatik ölçekleme, otomatik yükseltmeler, temel güvenlik yapılandırmaları, temel ağ yapılandırması. Avantajları:

- Üretim için optimize edilmiştir,
- Güçlü bir güvenlik duruşu (security posture) sağlar,
- Operasyonel verimliliği artırır.

**Standard.** Altta yatan altyapıyı **sen yönetirsin**, tek tek node'ları yapılandırmak dahil. Autopilot ile aynı işlevselliğe sahiptir, ama yapılandırma, yönetim ve optimizasyon sorumluluğu sende.

> **Öneri:** GKE Standard'ın sunduğu belirli düzeyde yapılandırma kontrolüne özel olarak ihtiyacın yoksa, **Autopilot** kullan.

**Cluster oluşturma ve avantajlar.** Cluster'ı Google Cloud Console ya da gcloud ile oluşturursun. Cluster'lar özelleştirilebilir; farklı makine tipleri, node sayısı ve ağ ayarlarını destekler. GKE cluster'ı çalıştırmanın getirdiği gelişmiş yönetim özellikleri:

- Compute Engine örnekleri için load balancing,
- **Node pool'lar** — cluster içinde alt node kümeleri, ek esneklik için,
- Cluster'ın node sayısının otomatik ölçeklenmesi,
- Node yazılımının otomatik yükseltilmesi,
- **Node auto-repair** — node sağlığını ve kullanılabilirliğini korur,
- Google Cloud Observability ile loglama ve izleme.

Autopilot cluster başlatmak için tek komut:

```bash
gcloud container clusters create-auto k1 --region <bölge>
```

---

# BÖLÜM 6 — Uygulama Geliştirme: Cloud Run ve Cloud Run functions

## Cloud Run

Cloud Run, **durumsuz (stateless) konteynerleri** web istekleri ya da Pub/Sub olayları üzerinden çalıştıran, **yönetilen bir işlem platformudur.**

- **Serverless'tır.** Tüm altyapı yönetim işlerini kaldırır; sen uygulamaya odaklanırsın.
- **Knative** üzerine kuruludur — Kubernetes üzerine inşa edilmiş açık bir API ve çalışma zamanı ortamı. Bu sayede tam yönetilen Google Cloud'da, GKE'de ya da Knative'in çalıştığı **herhangi bir yerde** çalışabilir.
- **Hızlıdır.** **Sıfırdan** otomatik ölçeklenir (aşağı da, yukarı da) ve neredeyse anında. Sadece kullandığın kaynak için, **100 milisaniye hassasiyetle** ücretlendirilir. Aşırı sağlanmış (over-provisioned) kaynak için asla ödemezsin.

### Cloud Run geliştirme akışı (üç adım)

1. **Yaz.** Uygulamanı favori dilinle yaz. Uygulama, web isteklerini dinleyen bir **sunucu başlatmalıdır.**
2. **Paketle.** Uygulamanı bir **konteyner imajına** derleyip paketle.
3. **Dağıt.** İmaj **Artifact Registry**'ye gönderilir; Cloud Run oradan dağıtır.

Dağıttıktan sonra benzersiz bir **HTTPS URL** alırsın. Cloud Run, istekleri karşılamak için konteynerini **talep üzerine** başlatır ve konteyner ekleyip çıkararak tüm gelen isteklerin karşılandığından emin olur.

### İki iş akışı: konteyner tabanlı ve kaynak tabanlı

- **Konteyner tabanlı (container-based):** Kendi imajını sağlarsın; şeffaflık ve esneklik verir.
- **Kaynak tabanlı (source-based):** Konteyner imajı yerine **kaynak kodunu** dağıtırsın. Cloud Run kaynağı derler ve uygulamayı bir konteyner imajına paketler. Bunu **Buildpacks** (açık kaynak proje) ile yapar. İmajının güvenli, iyi yapılandırılmış ve tutarlı biçimde inşa edilmesini istiyorsan bu yol uygundur.

Cloud Run **HTTPS sunumunu senin yerine halleder** — sen sadece web isteklerini işlersin, şifrelemeyi Cloud Run ekler.

### Fiyatlandırma

Cloud Run'ın fiyat modeli özeldir: Sadece **bir konteyner web isteği işlerken** kullandığın sistem kaynakları için ödersin (**100 ms** granülerlik), artı başlatma/kapatma anları. Konteynerin istek işlemiyorsa **hiçbir şey ödemezsin.** Ayrıca her **bir milyon istek** için küçük bir ücret vardır. Konteyner süresinin fiyatı CPU ve bellekle artar — daha çok vCPU/bellek = daha pahalı.

### Ne çalıştırabilirsin?

Linux 64-bit için derlenmiş **herhangi bir ikili (binary)** çalıştırabilirsin. Yani popüler dillerde yazılmış web uygulamaları: Java, Python, Node.js, PHP, Go, C++. Hatta daha az popüler diller: Cobol, Haskell, Perl. Uygulaman web isteklerini işlediği sürece sorun yok.

## Cloud Run functions

Birçok uygulamanın **olay güdümlü (event-driven)** parçaları vardır. Örnek: Kullanıcıların resim yüklediği bir uygulama. Resim yüklendiğinde, birkaç şey yapılması gerekebilir — standart formata çevirmek, farklı boyutlarda küçük resim (thumbnail) üretmek, her yeni dosyayı bir depoya kaydetmek.

Bu işlevi doğrudan uygulamaya gömebilirsin, ama o zaman **saniyede bir de olsa günde bir de olsa** onun için sürekli işlem kaynağı sağlaman gerekir. **Cloud Run functions** ile bunun yerine, gerekli resim işlemlerini yapan **tek amaçlı bir fonksiyon** yazarsın ve yeni resim yüklendiğinde **otomatik çalışmasını** ayarlarsın.

Cloud Run functions nedir?

- **Hafif, olay tabanlı, asenkron** bir işlem çözümüdür.
- Bulut olaylarına yanıt veren **küçük, tek amaçlı fonksiyonlar** oluşturmanı sağlar — sunucu ya da çalışma zamanı ortamı yönetmeden.
- Bireysel iş mantığı görevlerinden **uygulama iş akışları** kurmakta kullanılır; bulut servislerini bağlar ve genişletir.
- **100 milisaniye** hassasiyetle, **yalnızca kodun çalışırken** faturalanır.
- Diller: Node.js, Python, Go, Java, .NET Core, Ruby, PHP.

**Tetikleyiciler (triggers).** Cloud Storage ve Pub/Sub olayları, Cloud Run functions'ı **asenkron** tetikleyebilir; ya da **senkron** çalıştırma için **HTTP çağrısı** kullanırsın.

> **Cloud Run vs. Cloud Run functions:** Cloud Run, tam bir web servisini/konteyneri çalıştırır (HTTP sunan uzun ömürlü uygulama). Cloud Run functions, belirli bir olaya yanıt veren tek amaçlı küçük bir fonksiyon içindir. İkisi de serverless, ikisi de 100 ms hassasiyetle faturalanır.

---

# BÖLÜM 7 — Üretken Yapay Zeka, LLM ve Prompt Mühendisliği

Bu bölüm, Google Cloud bilgini **prompt mühendisliğiyle** birleştirip Gemini'den daha iyi yanıtlar almayı öğretir. Önce kavramları netleştirelim.

## Gen AI ve LLM — Aynı değiller

Bu iki terim çoğu yerde birbirinin yerine kullanılır ama **aynı değildir:**

- **Üretken yapay zeka (Generative AI / gen AI):** Metnin ötesinde çeşitli içerik türleri üretebilen **daha geniş** bir model yelpazesidir.
- **Büyük dil modeli (Large Language Model / LLM):** Gen AI'ın, **dil görevlerine** odaklanan bir alt kümesidir.

Yani her LLM bir gen AI'dır ama her gen AI bir LLM değildir.

**Gen AI nedir?** Yapay zekânın, üretken modeller kullanarak — genellikle prompt'lara yanıt olarak — metin, görsel veya başka veri **oluşturabilen** bir alt kümesidir. 2021'den beri popülerliği patladı ama yapay zeka 1950'lerin ortasından beri var. Gen AI modelleri, girdi eğitim verisindeki **desenleri ve yapıyı öğrenir**, sonra benzer özelliklere sahip yeni veri üretir. Yazılım geliştirme, sağlık, finans, eğlence, müşteri hizmetleri, satış gibi pek çok sektörde kullanılır.

> **Prompt nedir?** Bir eylemi/yanıtı başlatmak için bir programa (ya da kişiye) verilen **belirli bir talimat, soru veya işarettir.**

### Senaryo: Sasha

Bu bölümde bir örnek üzerinden ilerlenir. **Sasha** bir bulut mimarıdır; **Cymbal Bank** için bir Google Cloud VPC ağ mimarisi prototipi tasarlaması gerekir. Mevcut mimari bilgisini gen AI araçlarıyla birleştirerek zaman kazanmak ister. **Gemini** onu heyecanlandırır çünkü Google Cloud Console'un içinde olması, ek kurulum olmadan erişebileceği anlamına gelir. Sasha'ya bölüm boyunca döneceğiz.

## LLM'ler nasıl çalışır?

LLM'ler, metin ya da görsel olabilen **devasa miktarda veriyle** eğitilmiş, son derece karmaşık bilgisayar programlarıdır. "Large" (büyük) iki şeyi ifade eder:

- **Eğitim veri kümesinin boyutu** — bazen **petabayt** ölçeğinde.
- **Parametre sayısı** — parametreler, modelin eğitim sırasında öğrendiği **hafızalar ve bilgidir.** Bir problemi (örneğin metni tahmin etmeyi) çözme yeteneğini belirler ve **milyarlara, hatta trilyonlara** ulaşabilir.

**General-purpose (genel amaçlı):** Modeller, insan dilinin ortaklığı sayesinde, belirli göreve bakılmaksızın yaygın problemleri yeterince çözebilir.

**Pre-trained + fine-tuned (ön eğitimli + ince ayarlı):**

- **Pre-training:** Model, dilin altındaki yapı ve desenleri öğrensin diye ona **büyük bir metin/görsel/kod veri kümesi** beslenir. Bir prompt gönderdiğinde, model ön eğitimli modelinden **doğru yanıtın olasılığını** hesaplar. Bu yüzden LLM, "süslü bir otomatik tamamlama (autocomplete)" gibidir — en olası doğru yanıtı önerir.
- **Fine-tuning:** Sonra çok daha küçük bir veri kümesiyle belirli hedefler için **ince ayar** yapılır.

## Halüsinasyon (hallucination)

Bazen LLM **tamamen yanlış** bir cevap verir. Buna **halüsinasyon** denir — genellikle anlamsız ya da dilbilgisel olarak hatalı kelime/ifadelerdir. Neden olur? Çünkü LLM'ler:

- Yalnızca **eğitildikleri bilgiyi** anlar; senin işletmenin özel/alan bilgisini bilmeyebilir,
- **Gerçek zamanlı bilgiye erişimi yoktur,**
- Yalnızca **prompt'ta açıkça verilen** bilgiyi anlar; genelde prompt'un **doğru olduğunu varsayar,**
- Daha fazla bağlam **isteyemez,**
- Eğitildiği şeyin dışında bir şey **bilmez** ve o bilginin doğru olup olmadığını gerçekten **bilemez.**

Halüsinasyona yol açan faktörler:

- Model **yeterli veriyle eğitilmemiştir,**
- Model **gürültülü/kirli veriyle** eğitilmiştir,
- Modele **yeterli bağlam** verilmemiştir,
- Modele **yeterli kısıt (constraint)** verilmemiştir.

Halüsinasyonlar çıktıyı anlamayı zorlaştırır ve yanlış/yanıltıcı bilgi üretme olasılığını artırır. Ama prompt mühendisliğiyle bu sorunu **en aza indirebiliriz.**

## Gemini

Google Cloud, **Gemini** adlı bir gen AI modeli sunar; **her zaman açık bir iş birlikçi** gibi davranabilir. Geliştiriciler, veri bilimciler ve operatörler dahil geniş bir kullanıcı yelpazesine yardım eder. **Birçok Google Cloud ürününe gömülüdür.** Gemini, Google Cloud dokümantasyonu, öğreticileri ve örnekleri dahil devasa bir veri yelpazesine erişir. Doğru prompt'larla:

- Hangi kaynakların işine yarayacağına dair ayrıntılı öneriler ve rehberler üretir,
- Ayrıntılı **gcloud komutları** oluşturup Sasha için Cloud Shell'e ekleyebilir.

Sasha sadece ihtiyaçlarını, Gemini'den en iyi yanıtı alacak şekilde **ifade etmelidir.** Örneğin: *"IPv4 ve IPv6 adresleri kullanan bir ağı nasıl oluştururum?"* prompt'u, tam da bunu anlatan bir yanıt getirir.

## Prompt mühendisliği nedir?

Bir LLM, devasa bir veri kümesi içeren büyük bir modeldir. Peki ihtiyacın olan bilgiyi bu kümeden nasıl çıkarırsın? İşte **prompt mühendisliği** budur. **Prompt**, modele beslediğin metindir; **prompt mühendisliği** ise prompt'larını, modelden en iyi yanıtı alacak şekilde **ifade etme sanatıdır.** Prompt ne kadar iyi yapılandırılırsa, çıktı o kadar iyi olur.

### Dört prompt türü

Prompt'lar soru biçiminde olabilir ve dört kategoriye ayrılır:

1. **Zero-shot (sıfır örnek):** Modele yardımcı olacak **hiç bağlam ya da örnek içermez.** Örnek: *"Fransa'nın başkenti nedir?"* — "başkent" nedir diye bir örnek verilmez. Basit sorularda sorun değil, ama teknik prompt'larda örnek yanıtı iyileştirir.

2. **One-shot (tek örnek):** Modele bağlam için **bir örnek** verir. Örnek: Yine Fransa'nın başkentini sorarız ama örnek olarak *"İtalya → Roma"* veririz.

3. **Few-shot (birkaç örnek):** Modele **en az iki örnek** verir. Örneğe *"Japonya → Tokyo"*yu da ekleriz.

4. **Role (rol) prompt'ları:** Model yanıt verirken çalışacağı bir **referans çerçevesi** gerektirir. Örnek: *"Senden bir işletme profesörü gibi davranmanı istiyorum. Sana bir terim vereceğim, sen anlamını doğru açıklayacaksın. Cevaplarının her zaman doğru olduğundan emin ol. ROI nedir?"* Sasha'nın ihtiyacı için **rol prompt'ları en iyi çözüm olabilir** — ne gerektiğini ve hangi bağlamda gerektiğini tanımlar, böylece LLM'in net bir referans noktası olur.

### Bir prompt'un iki öğesi: preamble ve input

- **Preamble (giriş):** Ana sorundan/istekten önce modele bağlam ve talimat vermek için sağladığın **giriş metnidir.** LLM'in ne istediğini daha iyi anlaması için **sahneyi kurar.** Görevin bağlamını, görevin kendisini ve rehberlik edecek bazı örnekleri içerebilir.
- **Input (girdi):** LLM'e yaptığın **asıl istektir** — talimatın/görevin üzerinde işlem yapacağı şey. Örnek: *"Yorum: Video hakkında ne düşüneceğimi bilmiyorum. İnceleme:"* Preamble'a göre Gemini bu girdiyi inceler ve incelemenin olumlu, nötr ya da olumsuz olduğunu önerir.

Not: **Tüm bileşenler zorunlu değildir** ve format göreve göre değişebilir; öğe sırası da değişebilir.

**Gemini bağlamı korur.** Sasha'nın orijinal prompt'unu değiştirip bir rol ekleyelim ve dual-stack subnet ihtiyacını belirtelim: *"Google Cloud'da bir bulut mimarı gibi davranmanı istiyorum. gcloud kullanarak IPv4 ve IPv6 subnetleri kullanan bir ağı nasıl oluştururum?"* Gemini **kendi etkileşim bağlamını koruduğu** için, Sasha aslında şunu da sorabilirdi: *"...verilen gcloud komutunu, subnet'in dual stack olmasını sağlayacak şekilde nasıl ayarlarım?"* — çünkü önceki konuşmayı hatırlar.

## Prompt mühendisliği en iyi uygulamaları

Bunları ezberden çok, mantığıyla benimse:

1. **Ayrıntılı ve açık talimatlar yaz.** Prompt ne kadar belirsizse, kullanılamaz sonuç çıkma ihtimali o kadar yüksektir. Net ve öz ol.

2. **Sınırları tanımla.** Modele **ne yapmaması gerektiğini** değil, **ne yapması gerektiğini** söylemek daha iyidir. Model takılırsa, çeşitli durumlarda işe yarayan birkaç **"yedek" (fallback) çıktı** ver — örneğin emin olmadığında *"Bunu hâlâ öğreniyorum"* diyebilsin.

3. **Bir persona (rol) benimset.** Modele bir persona eklemek, ilgili sorulara odaklanmasına yardımcı olan **anlamlı bağlam** sağlar ve doğruluğu artırır. Bu, Sasha'nın Cymbal Bank için ağ mimarisi prototipine başlamasına yardım eder.

4. **Her cümleyi kısa tut.** Uzun cümleler bazen suboptimal sonuç verir. Uzun cümleleri **bir dizi kısa cümleye** ve daha basit görevlere böl.

### Sasha'nın son prompt'u

Öğrendiklerimizle Sasha prompt'unu şöyle günceller:

> *"Sen bir bulut mimarısın. Merkezî yönetilebilen bir Google Cloud VPC ağı kurmak istiyorsun. Ayrıca şirketinin diğer bölgelerindeki diğer VPC ağlarına da bağlanıyorsun. Bakımı gereken çok sayıda farklı güvenlik duvarı politikası setine sahip olmak istemiyorsun. Ne tür bir ağ mimarisi önerirsin?"*

Bu yeni prompt'la Gemini, Sasha'nın ihtiyacına tam uyan bir **hub-and-spoke ağ mimarisi** önerir. Prompt'larını rafine ederek Sasha, gereksinimlerini Gemini'nin **doğru odak ve ayrıntı düzeyinde** yanıtlayabileceği biçimde ifade etmiş olur.

> **Prompt mühendisliğinin özü:** Modelin bildiği şey sınırlıdır ve bağlamı senin verdiğin kadardır. İyi prompt = net talimat + yeterli bağlam + uygun örnekler + doğru persona + kısa cümleler. Bu beşi, halüsinasyonu azaltır ve çıktıyı işine yarar hale getirir.

---

# Modülün Toplu Özeti (Hızlı Tekrar)

Kursu bitirirken tüm modülleri bir arada görelim. Bu, sınavdan hemen önce göz gezdireceğin harita.

**Modül 1 — Bulut ve Google Cloud'a giriş:** Yönetilen altyapı ve servisler (IaaS, PaaS); Google Cloud ağı; altyapı boyunca güvenlik; açık kaynak lisanslarıyla teknoloji yayınlama; fiyatlandırma ve faturalandırma araçları.

**Modül 2 — Kaynak hiyerarşisi:** Dört seviye (kaynaklar, projeler, klasörler, organizasyon düğümü); politikaların tanımı ve aşağı doğru mirası; IAM ne zaman kullanılır; Google Cloud'a erişimin dört yolu (Console, Cloud SDK + Cloud Shell, API'ler, mobil uygulama).

**Modül 3 — Compute Engine:** VPC; Autoscaling; VPC uyumluluk özellikleri (routing tables, firewalls, VPC peering, shared VPC) — hepsi daha az ağ yönetimi demek; Cloud Load Balancing; on-premises/diğer bulut ağlarını Google VPC ile birbirine bağlama.

**Modül 4 — Depolama:** Beş çekirdek seçenek (Cloud Storage, Bigtable, Cloud SQL, Spanner, Firestore); Cloud Storage'ın dört sınıfı (Standard = sıcak; Nearline ve Coldline = serin; Archive = en soğuk).

**Modül 5 — Konteynerler:** Kod ve bağımlılıkların etrafındaki görünmez kutular; Kubernetes (konteynerli iş yüklerini yöneten açık kaynak platform); GKE (Google tarafından barındırılan yönetilen Kubernetes).

**Modül 6 — Uygulama geliştirme:** Cloud Run (web istekleri/Pub/Sub ile durumsuz konteynerler); Cloud Run functions (hafif, olay tabanlı, asenkron, tek amaçlı fonksiyonlar).

**Modül 7 — Gen AI ve prompt mühendisliği:** Gen AI nedir, LLM nedir, prompt mühendisliği nedir; en iyi uygulamalar.

---

# Sertifikaya Giderken En Kritik Ayrımlar (Cebinde Taşı)

Bu liste, transkriptteki en çok karıştırılan ve en çok sorulan noktaların özüdür.

- **IaaS (Compute Engine)** = OS ve yukarısını sen yönetirsin, ayrılan kaynağı ödersin. **PaaS (App Engine)** = kodu ver, kullandığını öde. **Serverless (Cloud Run)** = sadece kod, ms hassasiyetle öde.
- **Region** = bağımsız coğrafi alan (zonlardan oluşur). **Zone** = kaynağın fiilen çalıştığı yer. Yedeklilik için **çok zon**; küresel yakınlık/afet koruması için **çok bölge**.
- **Project ID** değişmez (immutable); **project name** değişebilir.
- Politikalar hiyerarşide **aşağı doğru** miras alınır. **Deny her zaman allow'dan önce** kontrol edilir.
- **Basic** rol = geniş; **predefined** = servise özel; **custom** = tam senin belirlediğin ama **klasöre uygulanamaz** (sadece proje/organizasyon).
- **Service account** hem kimlik hem kaynaktır; parola yerine kriptografik anahtar kullanır.
- **Rate quota** zamanla sıfırlanır; **allocation quota** adet sınırıdır. İkisi de proje düzeyinde.
- **VPC küreseldir**, subnet'ler bölgeseldir ve zonları kapsar.
- Load balancer: **HTTP(S) → Application LB**; **TCP/UDP → Network LB** (proxy = bağlantı sonlandırır; passthrough = kaynak IP korunur).
- Hibrit bağlantıda **peering SLA kapsamında değildir**; **Dedicated/Partner Interconnect %99,99'a varan SLA** sunabilir.
- Depolama: blob → **Cloud Storage**; SQL tek bölge → **Cloud SQL**; SQL + yatay ölçek → **Spanner**; NoSQL mobil/senkron → **Firestore**; NoSQL büyük veri → **Bigtable** (SQL ve çok satırlı transaction **yok**).
- Cloud Storage sınıfları erişim sıklığına göre: **Standard → Nearline (~ayda bir) → Coldline (~90 gün) → Archive (yılda birden az, 365 gün min.)**. **Autoclass** bunu otomatikleştirir.
- **GKE Autopilot** (önerilen) = Google node'ları yönetir; **Standard** = sen yönetirsin.
- **Cloud Run** = konteyner/servis; **Cloud Run functions** = olay tetiklemeli tek amaçlı fonksiyon. Her ikisi de serverless, 100 ms faturalanır.
- LLM = gen AI'ın dil alt kümesi. **Halüsinasyon**, yetersiz veri/bağlam/kısıt yüzünden olur. İyi **prompt** (net talimat + bağlam + örnek + persona + kısa cümle) onu azaltır.
- Prompt türleri: **zero-shot** (örnek yok), **one-shot** (bir örnek), **few-shot** (iki+ örnek), **role** (referans çerçevesi). Prompt öğeleri: **preamble** + **input**.

---

> **Kapanış:** Bu öğretici, modülde anlatılan her kavramı — bulutun tanımından prompt mühendisliğine kadar — neden var olduğu, nasıl çalıştığı ve ne zaman kullanılacağıyla birlikte aktardı. Sınav öncesinde "En Kritik Ayrımlar" ve "Toplu Özet" bölümlerini tekrar oku; bir kavramda takılırsan ilgili ana bölüme geri dön ve mantığını yeniden kur. Başarılar!
