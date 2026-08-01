# Alıştırma Soruları — Modül 6: Uygulamaları Dağıtma

**Deploying Applications** modülü (CI/CD pipeline anatomisi, Software Delivery Shield, container'lar ve Cloud Build) için senaryo tabanlı alıştırma soruları. Kaynak: [`deep-dive/06-deploying-applications/deploying-applications.md`](../../../deep-dive/06-deploying-applications/deploying-applications.md).

Cevap anahtarına bakmadan önce her soruyu kendin yanıtlamayı dene — sınava hazırlık değeri asıl burada.

---

## Sorular

**1.** Ekibin, kod kalitesi hakkında hızlı geri bildirim istiyor: bir geliştirici kendi feature branch'ine her commit yaptığında, kod otomatik olarak derlenmeli ve unit testler çalışmalı — hiçbir ortama, test ortamına bile, dağıtım yapılmamalı. Bu, pipeline'ın hangi bölümünü tarif ediyor?

A) Continuous Deployment
B) Continuous Delivery
C) Continuous Integration
D) Bir Binary Authorization politika kontrolü

**2.** `main` dalına bir değişiklik push edildikten sonra, pipeline'ın uygulamayı derliyor, staging'e dağıtıyor, entegrasyon ve performans testlerini çalıştırıyor ve başarılı bir derlemeyi release candidate olarak etiketliyor. Bu build, canary release olarak üretime çıkmadan önce bir release manager'ın hâlâ "onayla" demesi gerekiyor. Bu uygulamaya ne denir?

A) Continuous Integration
B) Continuous Deployment
C) Continuous Delivery
D) Blue-green release

**3.** Yukarıdakiyle aynı pipeline, ama yönetim artık onay adımını tamamen kaldırmak istiyor: staging testleri geçtiği sürece, release candidate hiçbir insan tıklamadan üretime dağıtılsın. Bu değişiklik pipeline'ı neye dönüştürür?

A) Continuous Delivery
B) Continuous Deployment
C) Continuous Integration
D) Sadece canary release, delivery aşaması olmadan

**4.** Bir ekip yeni bir sürümü yayınlarken önce üretim trafiğinin sadece %5'ini bu sürüme yönlendiriyor, Cloud Monitoring'de hataları izliyor ve her şey sağlıklı görünürse yüzdeyi kademeli olarak artırıyor. Bu hangi yayın stratejisidir?

A) Blue-green release
B) Canary release
C) Continuous deployment
D) Bir rolling VM değişimi (rolling VM replacement)

**5.** Bir ekip, tam donanımlı iki eş üretim ortamını sürekli çalışır halde tutuyor. Yayın yapmak için, eski ortamdan yeni ortama tüm trafiği bir anda kaydırıyorlar; bir şeyler ters giderse, trafiği anında eski ortama geri çeviriyorlar. Bu hangi yayın stratejisidir?

A) Canary release
B) Blue-green release
C) Continuous integration
D) Bir rolling update

**6.** Ekibin, Assured OSS'in açık kaynak bağımlılıklarının Google tarafından doğrulanmış, sürekli taranan sürümlerini sağlamasını istiyor — bu bağımlılıklara Node.js servislerinde kullanılan npm paketleri de dahil. Assured OSS bu npm paketlerini kapsayacak mı?

A) Evet, Assured OSS npm dahil tüm büyük paket ekosistemlerini kapsar
B) Hayır — Assured OSS yalnızca Java ve Python açık kaynak paketlerini kapsar
C) Evet, ama sadece paketler GKE'ye de dağıtılıyorsa
D) Hayır, çünkü Assured OSS'in yerini Binary Authorization aldı

**7.** Bir güvenlik incelemecisi, Artifact Registry'de duran belirli bir container imajının gerçekten güvenilir CI pipeline'ından, onaylı kaynak deposundan üretildiğini — yolda değiştirilmediğini ya da yerine başka bir şey konmadığını — kanıtlamak istiyor. Bu kanıtı doğrudan hangi yetenek sağlar?

A) Artifact Analysis güvenlik açığı tarama sonuçları
B) Cloud Build'in doğrulanabilir build metadata'sı
C) Tek başına bir Binary Authorization politikası
D) GKE güvenlik durumu (security posture) paneli

**8.** Artifact Registry'de duran her imajın, yeni bir CVE yayımlandığında otomatik olarak yeniden değerlendirilmesini istiyorsun — sadece push anında bir kere taranmasını değil. Herhangi bir şeyin engellenmesini istemiyorsun, sadece güncel ve görünür kalmasını istiyorsun. Bunu hangi servis yapar, ve bu servisin yaptığını **varsaymaman** gereken şey nedir?

A) Binary Authorization — ayrıca güvenlik açığı bulunan her imajı da engeller
B) Artifact Analysis — hem talep üzerine hem otomatik tarama yapar ve depodaki imajları yeni güvenlik açıkları için sürekli izlemeye devam eder, ama tek başına hiçbir şeyin dağıtılmasını engellemez
C) Cloud Deploy — güvenlik açığı bulunan imajların herhangi bir hedef ortama terfi etmesini engeller
D) Assured OSS — Artifact Registry'deki, kendi dahili paketlerin dahil, her paketi sürekli yeniden tarar

**9.** Güvenlik ekibinin politikası şu: yalnızca hem güvenlik açığı taramasından hem entegrasyon testinden geçtiğini kanıtlayan attestation'lara sahip imajlar GKE'ye dağıtılabilir. Bu attestation'lara sahip olmayan bir imajın çalışması aktif olarak engellenmeli. Bunu hangi servis zorunlu kılar?

A) Artifact Analysis
B) Cloud Build build metadata
C) Binary Authorization
D) Bir Cloud Monitoring uyarı (alerting) politikası

**10.** Bir geliştirici şöyle diyor: "Zaten Cloud Build'i uygulamamızı derlemek ve Docker imajını üretmek için kullanıyoruz, o zaman staging ve üretim arasındaki dağıtımı da tek tık onay ve rollback ile sıralamak için yine Cloud Build'i kullanalım." Bu ikinci iş için Cloud Build doğru araç mı?

A) Evet — Cloud Build hem imaj derlemeyi hem çok ortamlı dağıtımları sıralamayı yerleşik olarak yapar
B) Hayır — Cloud Build'in işi imajı derlemek, test etmek ve Artifact Registry'ye push etmekle biter; hedef ortamlar arasında tek tık onay ve rollback ile sıralı dağıtım Cloud Deploy'un işidir
C) Hayır — onay/rollback'li sıralı dağıtımlar `gcloud` ile elle script'lenmelidir; bunu sunan yönetilen bir servis yoktur
D) Evet, ama önce Binary Authorization devre dışı bırakılmalı

**11.** Bir meslektaşın şunu öneriyor: "Software Delivery Shield'ı açalım, uçtan uca güvence altına alınmış oluruz — tek bir API çağrısı ve tüm pipeline güvenli." Bu, Software Delivery Shield'ın doğru bir tanımı mı?

A) Evet — Assured OSS, Cloud Build, Artifact Analysis, Cloud Deploy ve Binary Authorization'ın yerini alan tek bir yönetilen API'dir
B) Hayır — Software Delivery Shield, Assured OSS, Cloud Build'in build metadata'sı, Artifact Analysis, Cloud Deploy, Binary Authorization ve GKE/Cloud Run çalışma zamanı güvenliğinin birleşimi için kullanılan bir şemsiye terimdir; tek başına açtığın tek bir araç değildir
C) Hayır — bu terim yalnızca Binary Authorization'ın politika motorunu ifade eder
D) Evet, ama yalnızca GKE workload'ları için kullanılabilir, Cloud Run için değil

**12.** Kıdemsiz bir mühendis şöyle diyor: "Container aslında daha küçük, daha hızlı bir VM'dir — her container yine de kendi işletim sistemini çalıştırır, sadece daha hafif bir tanesini." Bu ifadenin yanlışı nedir?

A) Hiçbir şey — bu doğru bir tanım, container'lar sadece küçültülmüş bir OS imajı kullanır
B) Container'lar, tek bir paylaşılan çekirdek (kernel) üzerinde process izolasyonu ve namespace'ler kullanarak işletim sistemi seviyesinde sanallaştırma yapar; bir VM'in aksine, container kendi OS kopyasını taşımaz
C) Container'lar tıpkı VM'ler gibi donanımı sanallaştırır, sadece önbellekleme (caching) sayesinde daha hızlı açılırlar
D) Container'lar da tıpkı VM'ler gibi bir hipervizöre ihtiyaç duyar

**13.** Bir geliştirici bir container imajını laptop'unda derleyip test ediyor. Aynı imaj, hiçbir yerde yeniden derlenmeden ya da yeniden paketlenmeden, önce bir entegrasyon ortamına, sonra üretime terfi ettiriliyor. Bu senaryo hangi container faydasını en iyi şekilde gösteriyor?

A) Uygulama izolasyonu (application isolation)
B) İş yükü taşınabilirliği (workload portability)
C) Sorumluluk ayrımı (separation of responsibility)
D) Binary Authorization uyumluluğu

**14.** Bir mühendis bir Cloud Build yapılandırma dosyasını düzenlerken şunu bilmesi gerekiyor: (1) belirli bir build step'ini fiilen çalıştırmak için hangi container'ın invoke edileceğini ne belirler, ve (2) tüm build'in ürettiği son container imajının adını ne belirler. Burada hangi iki alan (field) önemlidir?

A) `steps` ve `substitutions`
B) `name` (o adım için invoke edilecek container) ve `images` (build'in ürettiği imajın adı)
C) `source` ve `target`
D) `trigger` ve `tag`

**15.** Bir Cloud Build pipeline'ının, her biri kendi container'ında çalışan üç adımı var: 1. adım bağımlılıkları indiriyor, 2. adım bu bağımlılıkları kullanarak uygulamayı derliyor, 3. adım derlenmiş çıktıyı bir Docker imajına paketliyor. Bir adımın çıktısı, bir sonraki adım için nasıl kullanılabilir hale gelir?

A) Her adım kendi çıktısını, pipeline'da yapılandırılmış bir Cloud Storage bucket'ına elle yüklüyor
B) Cloud Build, `/workspace` dizinini her adımın container'ına mount ediyor ve oraya konan dosyalar kalıcı olarak sonraki adımlar için kullanılabilir hale geliyor
C) Adımlar birbirine shell ortam değişkenleri (environment variables) export ederek iletişim kuruyor
D) Her adımın çıktısı, bir sonraki adım tarafından okunabilmesi için önce Artifact Registry'ye push edilmeli

---

## Cevap Anahtarı ve Açıklamalar

**1. C — Continuous Integration.** CI, bir feature branch'e yapılan commit ile tetiklenir ve otomatik derleme + test'ten oluşur; ortaya çıkan yapıt saklanır (örneğin Artifact Registry'de) — bu aşamada hiçbir dağıtım gerçekleşmez. Tuzak, bunu Delivery ile karıştırmaktır: Delivery, `main` dalına yapılan bir push ile tetiklenir ve staging'e dağıtımı da içerir — sadece bir feature branch'i derleyip test etmekten ibaret değildir.

**2. C — Continuous Delivery.** Üretime geçmeden önceki manuel onay burada belirleyici ipucudur: Delivery, üretime hazır, test edilmiş bir build üretmeye kadar her şeyi otomatikleştirir, ama bu build'in fiilen üretime konma kararı hâlâ bir insana aittir. Continuous Deployment (B) cezbedici ama yanlış cevaptır, çünkü pipeline'ın geri kalanı (staging testi, release candidate, canary) aynıdır — sadece onay adımı farklıdır.

**3. B — Continuous Deployment.** Manuel onay kapısını kaldırmak, Delivery ile Deployment arasındaki tüm ayrımdır; pipeline'ın geri kalanı (derleme, staging testleri, release candidate etiketleme, canary/blue-green dağıtımı) aynı kalır. Bu, kaynak materyalde en açık şekilde belirtilen sınav tuzağıdır: "hiçbir insan müdahalesi olmadan" ifadesi ile "release candidate'i onaylıyorum" ifadesi arasındaki farkı ara.

**4. B — Canary release.** Canary, yeni bir sürümü önce trafiğin küçük bir yüzdesine açmak ve kademeli olarak artırmak demektir — böylece bir sorun çıkarsa bundan herkes değil, sınırlı bir kullanıcı kesimi etkilenir. Blue-green (A) yaygın çeldiricidir, ama blue-green iki eş ortam arasında bir anda gerçekleşen tam bir geçiştir, kademeli bir yüzde artışı değildir.

**5. B — Blue-green release.** Belirleyici özellikler, iki eş ortam ve anlık, tam bir trafik geçişidir (aynı şekilde anlık bir rollback ile geri dönülür). Canary (A) çeldiricidir çünkü her iki strateji de güvenli üretim yayınlarıyla ilgilidir, ama canary kademeli artarken blue-green bir anda tamamen geçiş yapar.

**6. B — Hayır, sadece Java ve Python.** Assured OSS açıkça, Google'ın kendi güvenli pipeline'larıyla inşa ettiği ve sürekli taradığı Java ve Python açık kaynak paketlerini kapsar — npm, Go modülleri ya da başka ekosistemlere uzanmaz. Bu, gözden kaçması kolay bir kapsam sınırlamasıdır, çünkü insanlar "açık kaynak güvenlik servisi" ifadesinin "tüm diller" anlamına geldiğini varsayar.

**7. B — Cloud Build'in doğrulanabilir build metadata'sı.** Bu metadata, bir yapıtın güvenilir bir kaynaktan ve güvenilir bir derleme sürecinden geldiğini kanıtlamanı sağlayan şeydir — özünde bir "menşe sertifikası"dır. Binary Authorization (C) cezbedici çeldiricidir çünkü o da güven konusuyla ilgilidir, ama Binary Authorization tek başına attestation'ları kullanarak politikayı zorunlu kılar; Cloud Build'in metadata'sının yaptığı gibi altta yatan build kökenini (provenance) üretmez.

**8. B — Artifact Analysis, ve hiçbir şeyi engellemez.** Artifact Analysis, depodaki imajlar için hem talep üzerine hem otomatik tarama sağlar, üstelik yeni keşfedilen güvenlik açıkları için sürekli izlemeye devam eder — ama bu tamamen bir gözlem/raporlama katmanıdır. Tuzak A şıkkıdır: insanlar genellikle bir güvenlik açığı tarayıcısının dağıtımı da engellediğini varsayar, ama bu zorunlu kılma (enforcement) rolü Artifact Analysis'e değil, Binary Authorization'a aittir.

**9. C — Binary Authorization.** Binary Authorization, attestation'ları (bir imajın tarama ya da test gibi gerekli adımlardan geçtiğinin kanıtı) toplar, bunları kuruluşunun politikasına göre kontrol eder ve politikaya uymayan imajların dağıtılmasını aktif olarak engeller. Artifact Analysis (A) burada klasik yanlış cevaptır — bir imajda güvenlik açığı olduğunu sana söyleyebilir, ama o imajın dağıtılmasını durduramaz.

**10. B — Hayır, bu Cloud Deploy'un işi.** Cloud Build'in rolü imajı derlemek, test etmek ve Artifact Registry'ye push etmektir; Cloud Deploy ise bir dizi hedef ortama, tek tık onay, rollback ve güvenlik içgörüleriyle sıralı teslimi otomatikleştiren servistir. İkisini karıştırmak kolay bir hatadır, çünkü her ikisi de isminde "Cloud" ve "Build/Deploy" geçirir ve ikisi de aynı pipeline içinde yer alır.

**11. B — Bu bir şemsiye terimdir, tek bir araç değildir.** Software Delivery Shield, Google'ın; Assured OSS, Cloud Build metadata'sı, Artifact Analysis, Cloud Deploy, Binary Authorization ve GKE/Cloud Run çalışma zamanı güvenliğinin birlikte çalışmasına verdiği isimdir — tek başına "Software Delivery Shield olan" tek bir düğme yoktur. Bunu tek, monolitik bir ürün gibi ele almak tuzaktır; altta yatan her servis yine de kendi başına anlaşılmalı ve yapılandırılmalıdır.

**12. B — Container'lar OS seviyesinde sanallaştırmadır, minik VM değildir.** Bir VM donanımı sanallaştırır ve her VM kendi tam OS kopyasını çalıştırır — bu yüzden VM'ler açılışta yavaştır (~1 dakika veya daha fazla) ve kaynak açısından ağırdır. Bir container ise işletim sisteminin kendisini sanallaştırır — tek bir paylaşılan çekirdek üzerinde process izolasyonu ve namespace'ler kullanır — bu yüzden container'lar saniyenin bir kısmında açılır ve hafiftir. Tam olarak bu "container = minik VM" yanılgısı, kaynak materyalde açıkça bir sınav tuzağı olarak işaretlenmiştir.

**13. B — İş yükü taşınabilirliği (workload portability).** Senaryo — aynı imaj, değiştirilmeden, laptop'tan entegrasyona, oradan üretime taşınıyor — tam olarak iş yükü taşınabilirliğinin tanımıdır: container'lar, geliştirme yaşam döngüsü boyunca minimal efor ile terfi ettirilebilecek kadar hafiftir ve neredeyse her yerde çalışabilir. Uygulama izolasyonu (A) çeldiricidir çünkü o da gerçek bir container faydasıdır, ama aynı host üzerindeki container'lar arasında bağımlılıkların/kaynakların izole edilmesini ifade eder — bir imajı ortamlar arasında değişmeden taşımayı değil.

**14. B — `name` ve `images`.** Bir Cloud Build yapılandırma dosyasında her adım aslında bir Docker container invoke'udur ve `name` özniteliği, o adımı çalıştırmak için hangi container imajının invoke edileceğini belirler; (üst seviyedeki) `images` özniteliği ise build'in nihayetinde ürettiği ve push ettiği container imajının adını belirtir. Bu iki alan, ikisi de "imaj" ile ilgili olduğu için karıştırılması kolaydır, ama farklı soruları cevaplarlar — adımı hangi container çalıştırıyor, build'den hangi imaj çıkıyor.

**15. B — `/workspace` dizini.** Cloud Build, kaynak kodunu her adımın container'ı içindeki `/workspace` dizinine mount eder ve bir adımın oraya yazdığı her şey kalıcı hale gelir ve sonraki her adım tarafından kullanılabilir — her adım kendi izole container'ında çalışıyor olsa bile. Bu, çok adımlı pipeline'ların (bağımlılıkları indir → derle → paketle) her adımın dosyaları aktarmak için harici bir depolamaya ihtiyaç duymadan çalışmasını sağlayan şeydir.
