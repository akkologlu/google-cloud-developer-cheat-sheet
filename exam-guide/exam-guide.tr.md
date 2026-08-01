# Sınav Rehberi — Professional Cloud Developer

Bu bir sınav öncesi hızlı tekrar rehberidir, öğretici değildir. Sekiz [deep dive](../deep-dive) dosyasının içine serpiştirilmiş sınav tuzaklarını, karar tablolarını ve "en kritik ayrımlar" bölümlerini, taranabilir, senaryo bazlı tablolara damıtır.

## Bu rehberi nasıl kullanmalısın?

- **Deep dive'lar**, bir servisin **neden** o şekilde çalıştığını öğretir. Anlayışını inşa etmek için önce onları, bir kez, dikkatle oku.
- **Sözlük** (`glossary/glossary.tr.md`), bir terimi unuttuğunda tek satırlık tanımını verir.
- **Bu rehber**, konuyu zaten anladığını varsayar; sadece karar mekanizmasını pekiştirmen için var: *Bu senaryoda hangi servis, ve neden?*

Aşağıdaki her modül bölümü bir tablodur: **soru X'i tarif ediyorsa, cevap genellikle Y'dir, çünkü Z.** Son bölüm, modüller **arasında** karışan servisleri toplar — deep dive'larda yan yana durmadıkları için aralarındaki farkı bilinçli çalışmadıkça asla pekiştiremeyeceğin çiftler ve gruplar.

Önce deep dive'ı oku. Bu rehberi sınavdan önceki gece kullan.

---

## Modül 1 — Temeller (Fundamentals)

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| "Projenin görünen adını değiştirmek, Google'ın projeyi tanımlama şeklini değiştirir mi?" | Hayır — **Project ID** değişmezdir (immutable); değişebilen **project name**'dir | Project ID, Google'ın ve araçlarının kullandığı kalıcı kimliktir; sadece görünen ad düzenlenebilir |
| "...belirli bir zaman aralığından sonra sıfırlanır" ile "...kaç kaynak tutabileceğini sınırlar" | **Rate quota** (zamanla sıfırlanır) ile **allocation quota** (adet sınırı koyar) | İkisi de proje düzeyindedir ama biri hız, diğeri envanterle ilgilidir |
| Bir politika bir klasöre uygulanmış — içindeki projelere ulaşır mı? | Evet, politikalar hiyerarşide **aşağı doğru miras alınır** | Organizasyon → klasör → proje → kaynak tek yönlü bir miras zinciridir |
| Aynı principal üzerinde çelişen bir allow ve bir deny politikası | **Deny her zaman kazanır** — IAM önce deny politikalarını, sonra allow politikalarını kontrol eder | Deny, verilen herhangi bir rolden bağımsız olarak kesin bir geçersiz kılmadır |
| "Custom rolü klasör düzeyinde uygulayabilir miyim?" | Hayır — custom roller yalnızca **proje veya organizasyon** düzeyinde uygulanır | Klasör düzeyinde custom rol desteklenmez; predefined/basic roller orada hâlâ uygulanabilir |
| Basic, predefined ve custom rol arasında seçim | Gerçek senaryolarda varsayılan seçim **predefined**'dir; **custom**, predefined hâlâ fazla genişse kullanılır; **basic** üretimde nadiren uygundur | Predefined roller Google tarafından bakımı yapılan, servise özel rollerdir; basic roller ölçekte en az ayrıcalık ilkesini ihlal eder |
| "Bir Service Account bir kimlik mi, bir kaynak mı, yoksa ikisi de mi?" | **İkisi de** — bir kimlik olarak doğrulanır, ayrıca kendisine bir kaynak gibi IAM rolleri uygulanabilir | Bu ikili doğa, "kim bu Service Account gibi davranabilir"i, "Service Account'ın kendisi ne yapabilir"den ayrı kontrol etmeni sağlar |
| "VPC ağları bölgeler arasında yayılır mı?" | Evet — **VPC küreseldir**; **subnet'ler bölgeseldir** ve o bölgedeki zonları kapsayabilir | Bu, Google Cloud ağını yeni öğrenenler için en yaygın sürpriz noktasıdır |
| İçerik bazlı yönlendirme gereken HTTP(S) trafiği ile ham TCP/UDP trafiği | **Application Load Balancer** ile **Network Load Balancer** | Application LB L7'de (HTTP/HTTPS) çalışır; Network LB L4'te (TCP/UDP/diğer IP protokolleri) çalışır |
| "Load balancer bağlantıyı sonlandırıyor mu, kaynak IP'yi koruyor mu?" | **Proxy** Network LB bağlantıyı sonlandırır; **Passthrough** Network LB sonlandırmaz ve istemcinin kaynak IP'sini korur | Passthrough, doğrudan sunucu dönüşü ya da orijinal istemci IP'si gereken iş yükleri içindir |
| Hibrit bağlantı için garantili çalışma süresi SLA'sı gerekiyor | **Dedicated Interconnect / Partner Interconnect** (%99,99'a varan SLA), Direct/Carrier Peering değil | Peering bir Google SLA'sı kapsamında değildir; Interconnect kapsamındadır |
| Sürekli erişilen veri ile ayda bir erişilen veri ile üç ayda bir erişilen veri ile neredeyse hiç erişilmeyen (uyumluluk/arşiv) veri | **Standard → Nearline → Coldline → Archive** | Cloud Storage sınıfları tamamen erişim sıklığı ve maliyet dengesine göre sıralanır; Archive'ın 365 gün minimum saklama süresi vardır |
| "Gereksinimlerimi karşılayan en yüksek soyutlama düzeyini istiyorum" | Merdiveni yukarı çık: **IaaS (Compute Engine) → PaaS (App Engine) → Serverless (Cloud Run) → SaaS** | Daha az soyutlama = daha çok kontrol ama daha çok operasyonel yük; sınav "gereksinimleri karşılayan en yüksek soyutlama"yı tercih eder |
| "GKE node'larını hiç yönetmek istemiyorum" | **GKE Autopilot**, Standard değil | Autopilot hem control plane'i hem node'ları yönetir; Standard sadece control plane'i yönetir |
| Tam, uzun süre çalışan bir container/servis ile bir olayla tetiklenen tek amaçlı bir fonksiyon | **Cloud Run** ile **Cloud Run functions** | Fonksiyonlar olay güdümlü ve tek amaçlıdır; Cloud Run istekleri dinleyen tam bir durumsuz container çalıştırır — ikisi de en yakın 100 ms'ye faturalanır |
| Örneksiz bir prompt ile bir örnekli, iki-veya-daha-fazla örnekli, ve bir persona atanmış prompt | **Zero-shot / one-shot / few-shot / role prompting** | Bunlar dört prompt türüdür; daha fazla örnek genellikle önemsiz olmayan görevlerde doğruluğu artırır |

---

## Modül 2 — Google Cloud Geliştirmeye Başlarken

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| "Veritabanı kimlik bilgileri ya da API uç noktaları nerede tutulmalı?" | **Ortam değişkenleri**, kaynak kodda sabit olarak asla değil | Aynı test edilmiş kodun dev/test/prod boyunca değişmeden çalışmasını sağlar — twelve-factor app ilkesi |
| "Bu monoliti mikroservislere ayırmalı mıyım?" | **Maliyet-fayda** değerlendirmesi yap — "mikroservisler her zaman daha iyidir" değil | Mikroservisler bağımsız ölçekleme/dağıtım kazandırır ama operasyonel karmaşıklık bedeliyle; sınav "modern = doğru" mantığını reddeder |
| Bir olay tetikleyicisiyle mi yoksa bir mesaj kuyruğuyla mı gevşek bağlılık kurulacak | **Eventarc** (olay kuyruğu) ile **Pub/Sub** (mesaj kuyruğu) | İkisi de üreticiyi tüketiciden ayırır ve trafik ani yükselmelerini tamponlar; bir olaya mı tepki verdiğine yoksa bir mesaj mı ilettiğine göre seç |
| Bir HTTP API tüketicisi bir payload'dan yalnızca ihtiyacı olan alanları okumalı | Payload'ın **tamamına** değil, **belirli alanlarına** gevşek bağlan | Yayıncının, mevcut tüketicileri bozmadan yeni alanlar eklemesine izin verir (geriye dönük uyumluluk) |
| Paylaşılan bir darboğaz olmadan yatay ölçeklenen bileşen tasarımı | **Durumsuz (stateless)** bileşenler + durum için ayrı bir veritabanı (ör. Firestore) | Paylaşılan durum, klasik ölçeklenebilirlik darboğazıdır; durumsuz worker'lar serbestçe eklenip çıkarılabilir |
| Bir istek, geçici bir ağ sorunundan mı yoksa dakikalardır çökük bir arka uçtan mı başarısız oluyor | **Üstel geri çekilmeli yeniden deneme** (geçici) ile **devre kesici** (kalıcı) | Kalıcı olarak bozuk bir arka ucu sürekli yeniden denemek kaynak israf eder ve kesintiyi kötüleştirir; Cloud Client Libraries geçici hataları otomatik yeniden dener |
| Uygulama verisini (oturum, hesaplanmış değerler) mi yoksa web/statik içeriği mi önbelleğe alacaksın | **Memorystore** (Redis/Memcached, bellek içi) ile **Cloud CDN** (Google'ın kenar ağı) | Farklı katmanlar: uygulama verisi önbelleği ile kenar içerik önbelleği |
| "Legacy arka uç API'lerinin önünde oran sınırlama, kota ve bir cephe (facade) istiyorum" | **Apigee** | Google Cloud'un API gateway/yönetim platformudur |
| "Kendi giriş sistemimi yazmak istemiyorum" | **Identity Platform** / **Firebase Authentication** | Kimlik devretme, parola saklama, MFA ve oturum yönetimini sıfırdan yazmanın güvenlik riskinden kurtarır |
| "Serverless bir uygulama nasıl log yazmalı?" | **stdout**'a yaz, altyapı toplasın — log dosyalarını asla elle yönetme | Serverless platformlarda yönetilecek kalıcı disk yoktur; stdout doğal olay-akışı yaklaşımıdır |
| Bir feature branch'e commit otomatik derleme+test tetikliyor ama üretime otomatik ulaşmıyor | **Continuous Integration** | CI, "kod derleniyor ve testler geçiyor" noktasında durur, dağıtım yapmaz |
| Main'e push, staging testlerini ve bir release-candidate build'i tetikliyor ama üretime dağıtım için **manuel onay** gerekiyor | **Continuous Delivery** | Delivery, "dağıtıma hazır" noktasına kadar her şeyi otomatikleştirir — gitme kararı insanda kalır |
| Main'e push ve — testler geçerse — üretime dağıtım **hiçbir insan adımı olmadan** gerçekleşiyor | **Continuous Deployment** | Delivery ve Deployment neredeyse aynıdır; tek fark son adımı bir insanın onaylayıp onaylamadığıdır |
| Yeni sürümü önce küçük bir trafik yüzdesine açıp kademeli artırmak | **Canary release** | Kötü bir sürümün etkisini küçük bir kullanıcı dilimiyle sınırlar |
| İki eş ortam, anlık trafik geçişi ve anlık rollback | **Blue/green release** | Kademeli yüzde artışı değil, iki canlı ortam arasında atomik geçiştir |
| Bir göç sırasında legacy bir monolitin parçalarını kademeli değiştirmek | **Strangler pattern** | Bir cephe, istekleri eski ya da yeni bileşenlere yönlendirir; parça parça değiştirme, riskli bir "big bang" yeniden yazımından kaçınır |
| CLI'dan Cloud Storage bucket/nesne yönetimi | **`gcloud storage`**, `gsutil` değil | `gcloud storage` modern, daha hızlı, tercih edilen araçtır; `gsutil` hâlâ çalışır ama eski (legacy) sayılır |
| Uygulama kodundan Google Cloud API'lerini çağırmak | **Cloud Client Libraries**, ham API çağrıları değil | Otomatik kimlik doğrulama, geri çekilmeli yeniden deneme ve dile özgü kurallar ekler |
| Hızlı, tek kullanıcılı, kullan-at bir komut satırı ortamı ile kalıcı, standartlaştırılmış, güvenli bir ekip geliştirme ortamı | **Cloud Shell** ile **Cloud Workstations** | Cloud Shell geçicidir (bir saat hareketsizlikte sonlanır, 5 GB kalıcı home); Cloud Workstations, VPC'in içindeki geçici Compute Engine VM'lerinde çalışan, ekipler için yönetilen, yeniden üretilebilir bir ortamdır |
| Gerçek servise dokunmadan ya da onun için ödemeden Firestore/Pub/Sub/Spanner/Bigtable/Datastore'a karşı geliştirme yapmak | **Yerel emülatörler** | Kod değişmez — sadece bir ortam değişkeni emülatör ile gerçek servis arasında geçiş yapar |

---

## Modül 3 — Depolama

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Statik web sitesi, büyük ikili dosyalar, kullanıcı tarafından yüklenen foto/video | **Cloud Storage** | Yapılandırılmamış nesne depolama; anahtar = nesne adı, içerik opak byte, nesne başına 5 TB'a kadar |
| Esnek doküman, gerçek zamanlı senkron ve çevrimdışı destek isteyen mobil/web uygulaması | **Firestore** | Güçlü tutarlılık ve yerleşik çevrimdışı önbellek + senkron ile doküman/koleksiyon modeli |
| Milyarlarca satır, sub-10ms key-value aramalar, yüksek verimli tek-anahtarlı veri (IoT, clickstream, zaman serisi) | **Bigtable** | Devasa ölçekte, salt hızlı anahtar bazlı erişim için optimize edilmiş seyrek geniş sütunlu NoSQL |
| Aynı soruda "Bigtable" ve "BigQuery" birlikte geçiyor | İsim benzerliğine rağmen **birbirinin zıttıdır** — Bigtable operasyonel NoSQL'dir, BigQuery analitik veri ambarıdır | Bu isim çakışması, tüm depolama modülündeki en yaygın tuzaktır |
| Klasik ilişkisel web uygulaması, OLTP, minimal refactor ile MySQL/PostgreSQL/SQL Server göçü | **Cloud SQL** | Genel amaçlı yönetilen ilişkisel DB; Google replikasyonu, failover'ı, yedekleri yönetir |
| Çok daha yüksek performans ya da transactional + analitik (HTAP) karışımı isteyen PostgreSQL uyumlu uygulama | **AlloyDB** | Yalnızca PostgreSQL, ama compute/storage ayrımı sayesinde Columnar Engine ile 4 kat transactional ve 100 kata kadar analitik performans |
| En yüksek SLA gereken, mission-critical, küresel dağıtık, güçlü tutarlı ilişkisel veri | **Spanner** | Yatay ölçeklenebilirlik + güçlü tutarlılık + %99,999 SLA'yı bir arada sunan tek servis |
| "Analitik", "veri ambarı", "BI raporlama", "petabaytları SQL ile tara" | **BigQuery** | Serverless OLAP ambarı; milisaniyelik tekil satır işlemleri için tasarlanmadı |
| Redis ya da Memcached destekli hızlı bellek içi önbellek gerekiyor | **Memorystore** | Tam yönetilen, her iki motorla da protokol uyumlu; bir kayıt sistemi (source of truth) olarak tasarlanmadı |
| "Okuma güncellemeyi anında mı görür, yoksa gecikebilir mi?" | **Güçlü tutarlılık** (Spanner, Firestore'un depolama katmanı) ile **nihai (eventual) tutarlılık** | Güçlü tutarlılık, en son yazmanın her yerde anında görülmesini garanti eder |
| IP allowlist ya da SSL sertifikası yönetmeden güvenli DB erişimi gerekiyor | **Cloud SQL Auth Proxy** / **AlloyDB Auth Proxy** | Yerel bir proxy istemcisi, sunucu tarafındaki eşiyle güvenli bir tünel açar |
| "Tüm uygulamam için hangi tek veritabanını kullanmalıyım?" | Tuzak soru — **tek beden herkese uymaz**; her iş yüküne uyan servisi kullan | Asla tek bir veritabanıyla sınırlı değilsin ve boyut limitleri global değil veritabanı başınadır |

---

## Modül 4 — Kimlik Doğrulama ve Yetkilendirme

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Bir Google grubu / Workspace hesabı / Cloud Identity domain'i için "bu principal türü bir API isteğini doğrulayabilir mi?" | **Hayır** — üçü de kimlik oluşturamaz, sadece toplu izin yönetimini kolaylaştırır | Bir isteği fiilen doğrulayabilen tek şey **Google Account** (kişi) veya **Service Account**'tır (uygulama/workload) |
| Google Workspace account ile Cloud Identity domain | Workspace hesabının Workspace uygulamalarına (Gmail, Docs, Drive) erişimi var; Cloud Identity domain'in **yok** | Cloud Identity, Workspace satın almadan merkezi kimlik isteyen kuruluşlar içindir |
| `pubsub.subscriptions.consume` gibi bir izin dizesi | Format her zaman **`servis.kaynak.fiil`**dir, ve yalnızca bir **rol** üzerinden verilir, asla doğrudan atanmaz | Roller izinleri yönetilebilir bir birimde paketler |
| Düşük güvenlikli, salt okunur, gerçek bir kimlik gerekmiyor | **API key** — ama çoğu Google API'sinin bunu kabul bile etmediğini unutma | Sızmış bir API key, ilişkili projeye uzun süreli, kısıtlanmamış erişim verir |
| Gerçek bir kişi adına, onay gerektiren bir işlem | **User account / OAuth 2.0** token | Kullanıcının gerçekten izin verdiğiyle sınırlı ve süreli |
| Uygulamadan uygulamaya, sunucu tarafında, gözetimsiz bir Google API çağrısı | **Service account** | OAuth token'ının erişimi, Service Account'a eklenmiş rollerle sınırlıdır |
| İndirilen bir Service Account anahtarı genel bir depoya sızmışsa, ya da ayrıcalık yükseltmek için kullanılmışsa, ya da loglarda gerçek aktörü gizlemek için kullanılmışsa | Üç adlandırılmış risk: **credential leakage**, **privilege escalation** (anahtarı iptal etmek, zaten verilmiş ayrıcalıkları geri almaz), **identity masking** | Bu yüzden indirilen SA anahtarları son çare kimlik doğrulama yöntemidir |
| Yerel geliştirme, koda karşı Google Cloud'u test etme | `gcloud auth application-default login` | CLI'nin kendi oturumunu değil, Application Default Credentials'ı (ADC) besler |
| Terminalden `gcloud compute instances list` çalıştırma | `gcloud auth login` | CLI'nin kendisini doğrular, uygulama kodunu değil |
| Compute Engine ya da Cloud Run'da (GKE değil) çalışan üretim kodu | **Attached Service Account** | Tercih edilen üretim deseni — yönetilecek anahtar dosyası yok; Google kimlik bilgisi yaşam döngüsünü yönetir |
| GKE içinde çalışan üretim kodu | **Workload Identity** | Bir Kubernetes Service Account'ının bir IAM Service Account'ını impersonate etmesini sağlar — workload başına ince taneli kimlik |
| Google Cloud dışında (başka bir bulut, on-premises), OIDC destekleyen bir kimlik sağlayıcısıyla çalışan üretim kodu | **Workload Identity Federation** | Harici bir OIDC token'ını kısa ömürlü bir Google Cloud erişim token'ıyla değiştirir — Service Account anahtarı gerekmez |
| Federation hiçbir şekilde mümkün değil | **Service account anahtarı**, açık bir son çare olarak, "kendi public key'ini yükle" ve en az ayrıcalık mitigasyonlarıyla | Karar ağacındaki diğer her seçenek buna tercih edilir |
| "HTTPS uygulamama, hiç yetkilendirme kodu yazmadan ve VPN olmadan erişim kontrolü istiyorum" | **Identity-Aware Proxy (IAP)** | Ağ konumuna değil, kimliğe dayalı uygulama-seviyesi erişim kontrolü |
| Mobil/web uygulaması için hızlı, hazır giriş: parola, telefon, Google/Apple/GitHub girişi | **Firebase Authentication** | Backend + SDK'lar + hazır UI, uygulama geliştiricilerini hedefler |
| Kurumsal müşteriler için SAML/OIDC federasyonu, MFA ya da IAP entegrasyonu gerekiyor | **Identity Platform** | Firebase Auth ile aynı temel, artı kurumsal düzey özellikler |
| Bir API key, parola ya da sertifikayı güvenle saklama | **Secret Manager**, bir ortam değişkeni değil | Versiyonlama (immutable, silinebilir), IAM tabanlı en az ayrıcalık erişimi, denetim günlüğü ve isteğe bağlı Cloud KMS şifrelemesi ekler — ortam değişkenlerinin hiçbiri bunu sağlamaz |

---

## Modül 5 — Uygulamana Zekâ Ekleme

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Bir görseldeki nesneleri etiketleme, metin okuma (OCR), yüz/logo/nirengi noktası tespiti, ya da müstehcen içerik işaretleme | **Vision AI** | Önceden eğitilmiş görüntü anlama, ML uzmanlığı gerekmez |
| Sesi metne ya da metni sese çevirme, 110'dan fazla dilde | **Speech-to-Text / Text-to-Speech** | Sesli arayüzler kurmak için eşleşen bir çift |
| Keyfi metni dinamik olarak, talep üzerine çevirme | **Translation AI** | Hızlı, duyarlı, önceden hazırlanmış çeviri dosyası gerekmez |
| Müşteri metninden/sosyal medya paylaşımlarından duygu, varlık ya da niyet çıkarma | **Natural Language AI** | Ham metni yapılandırılmış anlama dönüştürür |
| Bir varlığın videonun çekimleri, kareleri ya da tamamı boyunca ne zaman/nerede göründüğünü bulma | **Video AI** | Vision AI'nin zaman boyutu eklenmiş karşılığı |
| Taranmış faturaları, sözleşmeleri ya da formları yapılandırılmış, sorgulanabilir alanlara çevirme | **Document AI** | Yapılandırılmamış belgeleri yapılandırılmış veriye dönüştürür |
| Kod yazmadan kendi verinle bir model eğitme | **Agent Platform AutoML** | Görüntüler, tablo verisi ya da video üzerinde kodsuz eğitim |
| Standart önceden eğitilmiş API'ler çok özel bir problemi kapsamıyor | **TensorFlow / PyTorch** özel modeli | Tam kontrol, ama gerçek ML uzmanlığı gerektirir |
| "İçerik hakkında dar bir evet/hayır sorusunu cevapla" ile "yepyeni içerik üret" | **Önceden eğitilmiş dar API** ile **Generative AI** | Dar ML, belirli bir sınıflandırmayı cevaplar; generative AI yeni, açık uçlu çıktı üretir |
| "Bir modeli foundation model yapan nedir, ve özellikle LLM yapan nedir?" | **Foundation model** = devasa çok modlu veriyle eğitilen modeller için genel terim; **LLM** = en popüler alt tür, yalnızca metinle eğitilir | Her LLM bir foundation model'dir; her foundation model bir LLM değildir |
| Geniş biçimde eğitilip sonra küçük bir alana özel veri kümesiyle uyarlanan bir model | **Pre-training** (geniş) ardından **fine-tuning** (spesifik) | Pre-training genel yetenek inşa eder; fine-tuning onu özelleştirir |
| Bir model kendinden emin bir şekilde yanlış ya da anlamsız bir cevap üretiyor | **Halüsinasyon**, yetersiz eğitim verisi, gürültülü veri, yetersiz bağlam ya da prompt'ta yetersiz kısıt yüzünden oluşur | İyi prompt mühendisliği (bağlam + örnek + persona + öz talimat) bunu azaltır ama tamamen ortadan kaldırmaz |
| Kod üretmek, açıklamak, düzeltmek, tamamlamak, belgelemek ya da çevirmek için bir AI asistanı kullanma | **Gemini** kod desteği | Sadece otomatik tamamlamayı değil, geliştirici iş akışının tamamını kapsar |

---

## Modül 6 — Uygulamaları Dağıtma

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Bir feature branch'e commit otomatik olarak derleme + birim testlerini tetikliyor | **Continuous Integration** | "Kod derleniyor ve testleri geçiyor" noktasında durur |
| Main'e push staging testlerini ve bir release candidate'i tetikliyor ama üretime dağıtım için **manuel onay** gerekiyor | **Continuous Delivery** | Yapıt üretime hazırdır; ne zaman fiilen çıkacağına bir insan karar verir |
| Main'e push ve — testler geçerse — üretime dağıtım **hiçbir insan adımı olmadan** gerçekleşiyor | **Continuous Deployment** | Delivery ve Deployment neredeyse aynıdır; tek fark son adımı bir insanın onaylayıp onaylamadığıdır |
| Önce küçük bir trafik yüzdesine yayıp sonra kademeli artırma | **Canary release** | Etkiyi kullanıcıların bir alt kümesiyle sınırlar |
| İki eş ortam, anlık trafik geçişi ve anlık rollback | **Blue/green release** | Kademeli yüzde artışı değil, atomik geçiştir |
| "Bu açık kaynak Java/Python paketleri Google tarafından doğrulanmış ve sürekli taranıyor mu?" | **Assured OSS** | Yalnızca Java ve Python'u kapsar |
| "Bu container imajının güvenilir bir kaynaktan, güvenilir bir süreçle inşa edildiğini kanıtlayabilir miyim?" | **Cloud Build'in doğrulanabilir build metadata'sı** | Bir tarama değil, bir köken (provenance) kaydıdır |
| "Depolanan imajlarımı bilinen güvenlik açıklarına karşı tara, ve yeni CVE'ler için sürekli yeniden kontrol et" | **Artifact Analysis** | Gözlemler ve raporlar — hiçbir şeyi **engellemez** |
| "Gerekli kontrollerimden geçmemiş herhangi bir imajın asla çalışmasını engelle" | **Binary Authorization** | Attestation'lar üzerinden politikayı zorunlu kılar — Artifact Analysis'in eksik olduğu uygulama (enforcement) katmanı |
| Ortamlar boyunca sıralı dağıtım, tek tık onay ve rollback ile | **Cloud Deploy** | Pipeline'ın teslim/orkestrasyon parçası |
| "Bir container sadece hafif bir VM midir?" | Hayır — bir VM **donanımı** sanallaştırır (her biri kendi OS kopyasına sahiptir); bir container **OS'u** sanallaştırır (process izolasyonu + namespace'ler) | Bu yüzden container'lar saniyenin bir kısmında başlar, VM'ler bir dakika ya da daha fazla sürer |
| Kendi izole araç ortamında çalışan bir build pipeline adımı | Her Cloud Build **adımı bir Docker container'ıdır** (`name` = hangi container, `images` = üretilecek imaj) | Her adımın araçlarının diğerlerini kirletmesini önler |
| Bir build adımından diğerine dosya/çıktı geçirme | **`/workspace`** dizini | Her adımın container'ına mount edilir; tüm pipeline boyunca kalıcıdır |
| Build sadece belirli bir dala ya da etikete yapılan commit'lerde başlamalı | **Trigger type** — branch bazlı ya da tag bazlı | Cloud Build'i hangi commit koşulunun tetikleyeceğini belirler |

---

## Modül 7 — Uygulaman İçin Compute Seçenekleri

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Lift-and-shift göçü, özel işletim sistemi, belirli donanıma bağlı lisanslı yazılım, ya da HTTP-dışı bir TCP protokolü | **Compute Engine** | Maksimum kontrol, ama OS yamalarını, ölçekleme yapılandırmasını ve sağlık kontrollerini sen üstlenirsin |
| Büyük, kesintiye toleranslı bir toplu (batch) iş, maliyet önceliği | **Preemptible VM** (Compute Engine) | En az %60 indirim; Google kapasiteyi istediği zaman geri alabilir |
| Hibrit/çoklu bulut taşınabilirliği, stateful bileşenler ya da HTTP-dışı protokoller gereken container'lı iş yükü | **GKE** | Container'ları on-prem ve diğer bulutlar boyunca orkestralar; StatefulSet'leri ve keyfi TCP'yi destekler |
| "Node'ları hiç yönetmek istemiyorum, sadece iş yüklerimi" | **GKE Autopilot** | Google hem control plane'i hem node'ları yönetir; ayrıca sıkılaştırma en iyi uygulamalarını otomatik uygular |
| "Node tipi, ağ ya da GPU/TPU node pool'ları üzerinde ince taneli kontrole ihtiyacım var" | **GKE Standard** | Google yalnızca control plane'i yönetir; node pool'ları sen yönetirsin |
| Kimlik/depolama gereksinimi olmayan bir iş yükü ile kararlı ağ kimliği ve kalıcı depolama gereken bir iş yükü | **Deployment** (stateless) ile **StatefulSet** (stateful) | Herhangi bir kopya durumsuz bir isteğe cevap verebilir; veritabanı benzeri bir iş yükünün kararlı bir kimliğe ihtiyacı vardır |
| Durumsuz container, sıfır altyapı yönetimi istiyor | **Cloud Run** | Tam yönetilen, sıfıra kadar ölçeklenir, en yakın 100 ms'ye faturalanır |
| "Sadece kaynak kodumun bir HTTPS uç noktasına dönüşmesini istiyorum, Dockerfile'ı düşünmek istemiyorum" | **Cloud Run kaynak-tabanlı dağıtım** (buildpacks + Cloud Build) | Dili otomatik tespit eder ve senin için güvenli, tutarlı bir imaj inşa eder |
| "İmajın tam olarak nasıl inşa edildiği üzerinde tam kontrole ihtiyacım var" | **Cloud Run container-tabanlı dağıtım** | İmajı doğrudan sen sağlarsın |
| Bir olayla (Pub/Sub, Eventarc, HTTP) tetiklenen tek amaçlı kod | **Cloud Run functions** | Arka planda bir Cloud Run servisi **olarak** dağıtılır — ayrı bir ürün değil |
| Bir kez ya da bir zamanlamayla çalışan, bir port dinlemeyen ve bitince çıkan bir görev | **Cloud Run jobs** | Sürekli istek dinleyen Cloud Run servislerinden farklıdır |
| AI çıkarım (LLM), video transkodlama ya da 3D render, ama yine de serverless ekonomisini istiyorsun | **GPU'lu Cloud Run** | Tam yönetilen GPU, talep üzerine, rezervasyonsuz, sıfıra kadar ölçeklenir |
| Belirli bir makineye bağlı bir GPU/TPU gerekiyor | **Compute Engine** | Doğrudan donanım bağlama |
| Orkestre edilmiş bir container filosu boyunca paylaşılan bir GPU/TPU gerekiyor | **GKE** (node pool'lar üzerinden) | GPU, tek bir VM'e değil bir node pool'a bağlanır |
| Öngörülebilir, istikrarlı, 7/24 trafik | **Compute Engine / GKE** ayrılmış (dedicated) fiyatlandırma | Tutarlı kapasite ihtiyaçları için daha öngörülebilir faturalama |
| Düzensiz, öngörülemeyen trafik, boşta kapasiteye ödeme yapmaktan kaçınmak istiyorsun | **Cloud Run** kullandığın kadar öde | Boşta geçen zaman için asla faturalanmazsın |
| "App Engine'de yepyeni bir serverless servis mi kurmalıyım?" | Hayır — **Cloud Run** kullan | Cloud Run modern haleftir; App Engine yeni projeler için önerilmez |
| "Yanlış compute platformunu seçersem sıkışır mıyım?" | Hayır — **Cloud Client Libraries**'e karşı yazılmış uygulamalar az bir eforla platformlar arasında taşınabilir | Bu, "serverless'la başla, ihtiyaç oldukça daha fazla kontrole geç"i güvenli bir varsayılan strateji yapar |

---

## Modül 8 — İzleme ve Performans Ayarı

| Soru şunu söylüyorsa... | Cevap genellikle... | Çünkü... |
| --- | --- | --- |
| Genel sağlık dashboard'u, zaman içindeki trend, özel ya da hazır | **Cloud Monitoring** | Google Cloud, diğer bulutlar ve on-prem boyunca metrik, olay ve metadata toplar |
| Bir metrik bir eşiği aştığında uyarı ver | **Cloud Monitoring** uyarı politikası | Pasif dashboard izlemesi değil, aktif tetikleme |
| Her dashboard'un en azından şu metrikleri izlemesi gerekir | **Four Golden Signals**: gecikme, trafik, hatalar, doygunluk | Herhangi bir servisin sağlığı için evrensel başlangıç noktası |
| "Gecikme metriğim başarılı istekleri başarısız olanlardan ayırmalı mı?" | Evet — hızlı bir HTTP 500, ortalama gecikmeyi yapay olarak düşürür ve gerçek sağlığı gizler | Bu, sınavın en sık test ettiği altın-sinyal tuzağıdır |
| "200 OK yanıtı her zaman başarı mıdır?" | Hayır — 200 içinde yanlış içerik, ya da hiçbir hata koduna sahip olmayan bir SLA/politika ihlali, ikisi de **hata** sayılır | Hata takibi, sadece HTTP 5xx'ten daha geniştir |
| "Doygunluk sadece %100 kullanımda mı önemlidir?" | Hayır — sistemler genellikle %100'e ulaşmadan **önce** bozulmaya başlar | Kullanım hedeflerini gerçekte gözlemlenen bozulma noktalarına göre belirle, saf bir %100 varsayımına göre değil |
| Belirli bir log deseni göründüğü anda bildirim istiyorsun | **Log-based alert** | Tek bir eşleşen olayda tetiklenir |
| Zaman içinde oluşumları saymak ve bir eşik aşıldığında uyarmak istiyorsun | **Log-based metric** | Anlık tekil-olay bildirimi için değil, trend/hacim takibi için tasarlanmıştır |
| Aranabilirlik ve log seviyesi filtrelemesi için text log mu yoksa structured (JSON) log mu tercih edilir | **Structured loglar** (`jsonPayload`, `severity`, `message`) | Text logların (`textPayload`) log seviyesi yoktur ve sorgulanması zordur |
| Bir Compute Engine VM'inde üçüncü taraf yazılımdan (NGINX, Tomcat) log/metrik toplama | **Ops Agent** (loglar için Fluent Bit, metrikler için OpenTelemetry Collector) | Amaca özel, iki bileşenli ajan; standart sistem metrikleri için sıfır konfigürasyon |
| "GKE pod loglarım pod gittikten sonra hâlâ orada olacak mı?" | **Cloud Logging**'e gönderilmedikçe hayır — container logları pod ile birlikte kaybolur, cluster olayları bir saat sonra sona erer | Kubernetes'in kendisi logları uzun vadeli olarak saklamaz |
| Prometheus'u kendin işletmeden Kubernetes'te PromQL dashboard'ları/uyarıları istiyorsun | **Google Cloud Managed Service for Prometheus**, **managed collectors** ile (GKE dahil tüm Kubernetes için önerilir) | Mevcut PromQL bilgini korurken ölçeklemeyi ve yüksek kullanılabilirliği Google halleder |
| "Hangi hata en sık / en yeni, ve kaç kullanıcıyı etkiliyor?" | **Error Reporting** | Hataları stack trace'e göre otomatik olarak gruplar ve deduplike eder |
| "Tek bir isteğin yolunda zaman nereye gitti?" | **Cloud Trace** | Trace = tüm isteğin süresi; span = içindeki bir alt-işlem |
| "Cloud Run istekleri otomatik izliyor, o yüzden servisimde tam görünürlüğüm var" | Yanlış — otomatik izleme yalnızca **giren/çıkan HTTP**'yi kapsar, dahili çağrıları değil | Dahili span'ları görmek için enstrümantasyon (OpenTelemetry + Cloud Trace Exporter) gerekir |
| "Üretimde hangi fonksiyon ya da kod yolu, uygulamayı yavaşlatmadan CPU/bellek tüketiyor?" | **Cloud Profiler** | İstatistiksel örnekleme, düşük ek yük — üretimde sürekli açık bırakmak güvenlidir |
| İkisi de "performans" dediğinde Cloud Trace mi Cloud Profiler mı | Trace **"hangi istek adımı yavaştı"**nı cevaplar (zaman ekseni); Profiler **"hangi kod satırı pahalı"**yı cevaplar (kaynak ekseni) | İkisi de performans aracı olsa da tamamen farklı eksenlerdir |

---

## Sık Karıştırılan Servisler (Modüller Arası)

Bu çiftler ve gruplar hiçbir tek deep dive'da yan yana durmaz, bu yüzden aralarındaki fark bilinçli olarak çalışılmadıkça kolayca gözden kaçar.

| Karışan grup | Tek satırlık ayrım |
| --- | --- |
| **Compute Engine ile GKE ile Cloud Run ile Cloud Run functions ile App Engine** | Compute Engine = VM'ler, tam kontrol. GKE = yönetilen container orkestrasyonu, orta kontrol. Cloud Run = tam yönetilen serverless container'lar, minimum kontrol. Cloud Run functions = tek amaçlı, olayla tetiklenen kod, bir Cloud Run servisi **olarak** dağıtılır. App Engine = eski (legacy) serverless PaaS; yeni projeler için önerilmez, Cloud Run onun yerini aldı. |
| **Cloud Monitoring ile Cloud Logging ile Error Reporting ile Cloud Trace ile Cloud Profiler** | Monitoring = "bir şey ters mi gidiyor?" (metrik/dashboard/uyarı). Logging = "tam olarak ne oldu?" (ham olay detayı). Error Reporting = "hangi hata, ne sıklıkta?" (gruplanmış/deduplike edilmiş çökmeler). Trace = "hangi istek adımı yavaştı?" (zaman ekseni). Profiler = "hangi kod satırı kaynak yiyor?" (kod ekseni). |
| **Artifact Analysis ile Binary Authorization** | Artifact Analysis tarar ve güvenlik açıklarını raporlar — sadece gözlem. Binary Authorization bir politikayı zorunlu kılar ve uygunsuz imajların çalışmasını engeller — uygulama (enforcement). |
| **Bigtable ile BigQuery** | Bigtable = operasyonel NoSQL key-value deposu, sub-10ms aramalar, canlı uygulamalar için. BigQuery = serverless analitik veri ambarı, petabaytlar üzerinde OLAP. Aynı isim öneki, zıt işler. |
| **Cloud SQL ile AlloyDB ile Spanner** | Cloud SQL = genel yönetilen ilişkisel DB (MySQL/PostgreSQL/SQL Server), tek bölge OLTP. AlloyDB = yalnızca PostgreSQL, yüksek performans, HTAP. Spanner = küresel dağıtık, yatay ölçeklenen, güçlü tutarlı, %99,999 SLA. |
| **Firestore ile Bigtable** | Firestore = doküman modeli, mobil/web uygulamaları, gerçek zamanlı senkron + çevrimdışı destek. Bigtable = seyrek geniş sütunlu depo, milyarlarca satır, sub-10ms yüksek verimli operasyonel/analitik iş yükleri. İkisi de "NoSQL" ama tamamen farklı kullanım senaryoları. |
| **Workload Identity ile Workload Identity Federation** | Workload Identity **GKE içindeki** iş yükleri içindir (Kubernetes Service Account'ı bir IAM Service Account'ını impersonate eder). Workload Identity Federation **Google Cloud dışındaki** iş yükleri içindir (harici OIDC token'ı bir Google Cloud erişim token'ıyla değiştirilir). Hiçbiri indirilmiş bir Service Account anahtarı gerektirmez. |
| **Identity Platform ile Firebase Authentication** | Aynı temel doğrulama altyapısı. Firebase Authentication mobil/web uygulama geliştiricilerini hedefler (parola, telefon, sosyal giriş, hazır UI). Identity Platform kurumsal özellikler ekler: SAML/OIDC federasyonu, MFA, IAP entegrasyonu. |
| **Memorystore ile Cloud CDN** | Memorystore, backend'in için **uygulama verisini** bellekte önbelleğe alır (Redis/Memcached). Cloud CDN, **web/statik içeriği** Google'ın kenarında, son kullanıcılara daha yakın önbelleğe alır. Farklı katman, farklı amaç. |
| **Continuous Delivery ile Continuous Deployment** | Delivery, üretime hazır, onaylanmış bir yapıta kadar her şeyi otomatikleştirir — ne zaman çıkacağına bir **insan** karar verir. Deployment bir adım öteye gider ve testler geçince **insan** kapısı olmadan otomatik olarak üretime çıkar. |
| **GKE Standard ile GKE Autopilot** | Standard: Google control plane'i yönetir, node pool'ları ve yapılandırmalarını sen yönetirsin. Autopilot: Google hem control plane'i hem node'ları yönetir — sen sadece iş yüklerini düşünürsün. |
| **API key ile User account (OAuth) ile Service account** | API key bir kimlik değil bir projeyi tanımlar ve sızarsa uzun süreli erişim verir — nadiren, düşük güvenlikli salt okunur API'ler için kullanılır. User account (OAuth) sınırlı, süreli bir token'la bir kişiyi temsil eder. Service account rol-sınırlı erişimle bir uygulama/workload'ı temsil eder. |
| **IAM rol türleri: Basic ile Predefined ile Custom** | Basic = geniş, proje çapında, üretimde nadiren uygun. Predefined = Google tarafından bakımı yapılan, servise özel — varsayılan seçim. Custom = tamamen kendi tanımladığın, predefined hâlâ fazla izin verici olduğunda ve en az ayrıcalık gerektiğinde. |
| **Cloud Build ile Cloud Deploy** | Cloud Build kodu derler, testleri çalıştırır ve Artifact Registry'ye gönderilen bir container imajı üretir — **entegrasyon** aşaması. Cloud Deploy bu imajı onay/rollback kontrolleriyle sıralı hedef ortamlar boyunca taşır — **teslim/dağıtım** aşaması. |
| **Kubernetes "Deployment" ile Google Cloud "Cloud Deploy"** | Bir Kubernetes **Deployment**'ı, bir dizi durumsuz pod kopyasını çalışır tutan bir nesnedir. **Cloud Deploy**, bir build'i ortamlar boyunca terfi ettirmeyi otomatikleştiren tamamen ayrı bir Google Cloud CI/CD servisidir. Aynı kelime, ilgisiz kavramlar — klasik bir sınav-ifadesi tuzağı. |
| **Prometheus (kendi yönettiğin) ile Google Cloud Managed Service for Prometheus ile Cloud Monitoring** | Prometheus, açık kaynak araç setinin kendisidir (PromQL, zaman serisi verisi) — kendin işletir ve ölçeklersin. Managed Service for Prometheus, aynı araç setinin Google tarafından tam yönetilen versiyonudur — aynı PromQL, operasyonel yük yok. Cloud Monitoring, kendisi bir PromQL sorgu arka ucu olarak da davranabilen, daha geniş Google Cloud gözlemlenebilirlik ürünüdür. |

---

> **Sınavdan önce:** Her modül tablosunu bir kez, mümkünse sesli olarak çalış — önce senaryoyu söyle, sonra cevabı, sonra nedenini, bakmadan. Sonra karışan-servisler tablosu için aynısını yap, çünkü bunlar aynı soruda çeldirici olarak çıkma ihtimali en yüksek çiftlerdir. Bir satır artık mantıklı gelmiyorsa, bu ilgili deep dive'a geri dönüp "neden"i yeniden kurman gerektiğinin işaretidir.
