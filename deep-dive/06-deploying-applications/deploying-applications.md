# Uygulamaları Dağıtma (Deploying Applications) — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 6 — Deploying Applications** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Modül üç katmanlı bir hikâye anlatıyor: önce **neden** otomatik bir CI/CD (continuous integration / continuous delivery) hattına ihtiyacın olduğunu ve bu hattın hangi aşamalardan geçtiğini görüyorsun; sonra bu hattın **her adımının** nasıl kötüye kullanılabileceğini ve Google Cloud'un bunu **Software Delivery Shield** şemsiyesi altında nasıl güvenceye aldığını öğreniyorsun; son olarak, bu hattın somut yapı taşlarına — container'lara ve **Cloud Build**'e — iniyor ve bir Docker imajının nasıl inşa edilip **Artifact Registry**'ye gönderildiğini adım adım görüyorsun. Amaç, "şu servis şunu yapar" listesini ezberletmek değil; **"bir satır kod commit'ten üretime giden yolda hangi adımlar var, her adım neden var, ve bu yolu kim/ne güvence altına alıyor"** sorularının hepsine cevap verebilmeni sağlamak. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül neyi öğretiyor ve neden önemli?

Bir düşünce deneyiyle başlayalım. Diyelim ki beş kişilik bir mühendislik ekibisin ve her biriniz uygulamanın farklı bir parçası üzerinde çalışıyorsunuz. Birisi ödeme akışını değiştiriyor, birisi bildirim sistemini, birisi de arayüzü. Eğer her değişikliği elle test edip elle sunucuya kopyalıyorsanız, iki şey kaçınılmaz olarak olur: **Yayın hızınız düşer** (çünkü her değişiklik saatler süren manuel bir süreçten geçer) ve **hatalar üretime sızar** (çünkü insan eli her adıma karıştıkça, bir adımı atlama ya da yanlış yapma ihtimali artar). Modülün açılış cümlesi tam olarak bunu söylüyor: **"Güvenilir servisler çalıştırmak için güvenilir yayın süreçlerine sahip olmalısın."**

Çözüm, süreci mümkün olduğunca **otomatikleştirmek**tir: kod her commit edildiğinde otomatik olarak derlenip test edilsin, testler geçerse otomatik olarak paketlensin, paket otomatik (ya da tek tıkla onaylı) olarak dağıtılsın. İşte bu otomatik zincire **CI/CD pipeline (sürekli entegrasyon / sürekli teslim hattı)** denir. Modül, bu hattı üç ana başlık altında işliyor:

1. **CI/CD pipeline'ın anatomisi** — sürekli entegrasyon (continuous integration), sürekli teslim (continuous delivery) ve sürekli dağıtım (continuous deployment) arasındaki fark; bir değişikliğin commit'ten üretime giden yolculuğu.
2. **Bu hattın güvenliği** — Google Cloud'un **Software Delivery Shield** adı altında topladığı; Assured OSS, Cloud Build'in doğrulanabilir build metadata'sı, Artifact Analysis, Cloud Deploy, Binary Authorization ve GKE/Cloud Run'ın güvenlik özellikleri.
3. **Container'lar ve Cloud Build** — uygulamanı neden bir sanal makine yerine bir container içinde paketlemen gerektiği, ve Cloud Build'in bu container imajını nasıl inşa edip Artifact Registry'ye gönderdiği.

Bu üç başlık aslında tek bir hikâyenin parçalarıdır: **"Kodum commit edildikten sonra kullanıcıya ulaşana kadar hangi yoldan geçiyor, bu yol nasıl otomatikleşiyor, ve bu yolun her adımı nasıl güvence altına alınıyor?"** Şimdi baştan başlayalım.

> **Önceki modülle bağ:** İkinci modülde, geliştirici verimliliğini işlerken CI/CD'yi kısaca tanıtmış ve şu akışı çizmiştik: **commit → Cloud Build (derle + test) → Artifact Registry (yapıtı sakla) → Cloud Deploy (test/üretime dağıt).** Orada ayrıca **Delivery**'nin "üretime hazır, insan onaylı" ve **Deployment**'ın "testler geçerse otomatik üretim" anlamına geldiğini söylemiştik. Bu modül, tam olarak o özet cümlenin **açılımıdır** — her kutunun içine girip "bu kutu tam olarak ne yapıyor, neden var, hangi güvenlik katmanlarıyla korunuyor" sorularına cevap verir.

---

# PARÇA 1 — CI/CD Pipeline'ın Anatomisi

## Neden otomasyon şart?

Modülün gerekçesi son derece nettir: **Çoğu kullanıcıya dönük yazılım ekibi, yeni özellikler ve hata düzeltmeleriyle sık sık yayın yapmak ister.** Bu "yüksek yayın hızını" (release velocity) mümkün kılmak için **derleme (build), test ve yayın (release) süreçlerinin mümkün olduğunca otomatikleştirilmesi gerekir.** Bunun tersini düşün: Eğer her yayın manuel bir süreçse, ekip büyüdükçe ve değişiklik sayısı arttıkça bu süreç bir darboğaz haline gelir — insanlar, makinelerin saniyeler içinde yapabileceği tekrarlayan işleri saatlerce yaparak zaman kaybeder, üstelik bu sırada hata yapma riski de artar.

CI/CD pipeline, bu soruna **"stabil, tekrarlanabilir bir derleme ve dağıtım süreci"** sunarak cevap verir. "Tekrarlanabilir" kelimesine dikkat et: Pipeline'ın değeri, her seferinde **aynı adımların aynı sırayla, aynı şekilde** çalışmasından gelir — bu da insan hatasının önündeki en büyük kaynaklardan birini (tutarsızlık) ortadan kaldırır.

## Continuous Integration (Sürekli Entegrasyon) — İlk halka

**Sürekli entegrasyon (CI)**, geliştiricilerin değişikliklerini bir kod deposunda bir **feature branch**'e (özellik dalı) commit etmesiyle başlar. Bu commit, **Cloud Build gibi bir derleme servisini otomatik olarak tetikler.**

Burada kritik olan şudur: Tetiklenen bu derleme, keyfi bir süreç değildir — **senin önceden belirlediğin kurallar**, uygulamanın container'larının ve çalıştırılabilir dosyalarının (executable) nasıl üretileceğini yönlendirir. Derleme tamamlandığında, ortaya çıkan **uygulama yapıtları (artifacts)** bir depoda — örneğin **Artifact Registry**'de — saklanır.

> **Sezgi:** CI'ı bir kalite kontrol kapısı gibi düşün. Her geliştirici kendi değişikliğini commit ettiğinde, bu kapı otomatik olarak açılır ve "bu kod derleniyor mu, testler geçiyor mu" sorusuna cevap verir — **insan beklemeden, dakikalar içinde.** Bu, "acaba benim değişikliğim başkasının kodunu bozdu mu" endişesiyle günler geçirmek yerine, her commit'ten sonra anında geri bildirim almanı sağlar.

## Continuous Delivery (Sürekli Teslim) — İkinci halka

**Sürekli teslim (CD - delivery)**, değişiklikler kod deposundaki **ana dala (main branch)** push edildiğinde tetiklenir. Burada akış şöyle işler:

1. **Derleme sistemi** kodu derler ve uygulama imajlarını (application images) oluşturur.
2. **Dağıtım sistemi** — örneğin **Cloud Deploy** — bu uygulama imajlarını **staging (hazırlık) ortamında** Cloud Run'a ya da GKE'ye dağıtır ve orada **entegrasyon ve performans testlerini otomatik olarak çalıştırır.**
3. Tüm testler geçerse, derleme bir **release candidate (yayın adayı) build** olarak etiketlenir.
4. Sen bu release candidate build'i **manuel olarak onaylayabilirsin.**
5. Bu onay, üretim ortamına bir **canary** ya da **blue-green release** olarak dağıtımı tetikleyebilir.
6. Üretim ortamındaki uygulamanın performansını **Cloud Monitoring** ile izleyebilirsin.
7. Yeni dağıtım beklendiği gibi çalışıyorsa, tüm trafiği bu yeni sürüme **kaydırabilirsin (switch over).**
8. Bir sorun keşfedersen, hızlıca **son stabil sürüme geri dönebilirsin (rollback).**

Bu akışta iki kavram öne çıkıyor ve her ikisi de sınavda sık sorulur:

- **Canary release.** Yeni sürümü, kullanıcı trafiğinin **küçük bir yüzdesine** açarsın; her şey yolundaysa yüzdeyi kademeli olarak artırırsın. Mantık şu: Bir sorun varsa, bunu **tüm kullanıcı kitlesi değil, küçük bir kesim** yaşasın — hasar sınırlı kalsın.
- **Blue-green release.** İki eş (identical) üretim ortamı tutarsın — "blue" (mevcut, çalışan sürüm) ve "green" (yeni sürüm). Trafiği bir anda blue'dan green'e **kaydırırsın.** Bir sorun çıkarsa, trafiği anında geri blue'ya çevirerek **ani bir rollback** yaparsın.

> **Neden manuel onay burada duruyor?** Çünkü sürekli teslim (delivery), **"üretime dağıtmaya hazır bir yapıt üretmeyi"** otomatikleştirir, ama **"bu yapıtı gerçekten üretime koyma kararını"** insana bırakır. Bu, iş açısından hassas kararların (örneğin bir Cuma akşamı büyük bir dağıtım yapmak mı istiyorsun) hâlâ bir insanın elinde kalmasını sağlar.

## Continuous Deployment (Sürekli Dağıtım) — Üçüncü halka

**Sürekli dağıtım (continuous deployment)** iş akışı, sürekli teslimle **neredeyse aynıdır** — tek fark şudur: **Manuel onay süreci yoktur.** Dağıtım sistemi, release candidate build'leri **otomatik olarak** üretim ortamına dağıtır.

> **Sınav tuzağı — Delivery vs Deployment:** Bu ikisi isim olarak neredeyse aynı olduğu için (İngilizcede "delivery" ve "deployment") sürekli karıştırılır. Ayrımı şöyle sabitle: **Continuous Delivery = otomatik olarak üretime dağıtılmaya HAZIR bir yapıt üretilir, ama üretime koyma kararı insana aittir (manuel onay).** **Continuous Deployment = testler geçtiği sürece, insan hiç araya girmeden, yapıt otomatik olarak üretime dağıtılır.** Soruda "release candidate'i onaylıyorum" ya da "manuel onay" geçiyorsa **Delivery**; "hiçbir insan müdahalesi olmadan otomatik üretim" geçiyorsa **Deployment**'tır.

| Aşama | Tetikleyici | Ne yapar | İnsan müdahalesi |
| --- | --- | --- | --- |
| Continuous Integration | Feature branch'e commit | Derler, container/executable üretir, Artifact Registry'ye kaydeder | Yok (otomatik derleme+test) |
| Continuous Delivery | Main branch'e push | Staging'de test eder, release candidate üretir, üretime **onayla** dağıtır (canary/blue-green) | **Var** — üretime dağıtım manuel onay gerektirir |
| Continuous Deployment | Main branch'e push | Aynı akış, ama testler geçerse üretime **otomatik** dağıtır | Yok |

## Basit bir başlangıç pipeline'ı — ve neden yeterli değil

Modül burada, yeni başlayan birinin kurabileceği türden basit bir CI/CD pipeline'ını tarif ediyor: commit → derleme → test → dağıtım. Bu tür bir pipeline, **verimli ve tutarlı bir derleme/dağıtım süreci sağlar** — otomasyonun temel vaadini yerine getirir.

Ama modül hemen bir uyarıyla devam ediyor: **"Güvenliği CI/CD sürecinin her adımında düşünmek çok önemlidir."** Neden? Çünkü otomatik bir pipeline, aynı zamanda **otomatik bir saldırı yüzeyi** de yaratır. Eğer bir saldırgan pipeline'ın herhangi bir noktasına (kaynak kod, bağımlılıklar, derleme makinesi, imaj deposu, dağıtım adımı) sızabilirse, bu kötü niyetli kod **otomatik olarak** üretime kadar taşınabilir — tam da otomasyonun hızından faydalanarak. İşte bu yüzden modül, pipeline'ın anatomisini anlattıktan hemen sonra güvenliğe geçiyor.

---

# PARÇA 2 — CI/CD Güvenliği: Software Delivery Shield

## Neden var?

Az önce gördüğün gibi, bir CI/CD pipeline'ı; kaynak kod, açık kaynak bağımlılıklar, derleme altyapısı, imaj deposu ve dağıtım adımları gibi **birçok bileşenden** oluşur. Her bileşen, ayrı bir saldırı noktasıdır: kötü niyetli biri açık kaynak bir bağımlılığa zararlı kod ekleyebilir, derleme sürecine müdahale edebilir, ya da imaj deposundaki bir imajı değiştirebilir. Bu risklerin her biri için ayrı ayrı çözüm aramak yerine, Google Cloud tüm bu riskleri **tek bir kapsayıcı çözüm** altında ele alıyor: **Software Delivery Shield.**

**Google Cloud'un Software Delivery Shield'ı**, CI/CD sürecinin **her adımını** koruyan, **tam yönetilen (fully managed), uçtan uca bir yazılım tedarik zinciri güvenlik çözümüdür.** Bunu bir şemsiye kavram olarak düşün — aşağıda göreceğin her bir servis (Assured OSS, Cloud Build'in metadata'sı, Artifact Analysis, Cloud Deploy, Binary Authorization, GKE/Cloud Run güvenlik panelleri), bu şemsiyenin altındaki birer parçadır.

> **Sezgi:** Software Delivery Shield'ı, bir fabrikadaki uçtan uca kalite kontrol zinciri gibi düşün: Hammadde girişinde kontrol (Assured OSS), üretim hattında kayıt tutma (Cloud Build metadata), bitmiş ürün deposunda tarama (Artifact Analysis), sevkiyat öncesi son onay (Cloud Deploy + Binary Authorization), ve mağazada/rafta sürekli izleme (GKE/Cloud Run güvenlik panelleri). Zincirin **her halkasında** bir kontrol noktası var — sadece girişte ya da sadece çıkışta değil.

Şimdi pipeline'ın başından sonuna, hangi servisin hangi riski kapattığını sırayla görelim.

## Assured Open Source Software (Assured OSS) — Kaynağında güven

Modern uygulamaların büyük çoğunluğu, açık kaynak paketler üzerine inşa edilir. Ama bu paketlerin **güvenliğini** kim garanti ediyor? İşte **Assured Open Source Software (Assured OSS)** servisi bu boşluğu dolduruyor: Açık **Java ve Python** kaynak paketlerini, **Google tarafından doğrulanmış ve test edilmiş** olarak uygulamana dahil etmeni sağlar.

Bu paketler:

- **Google'ın güvenli pipeline'ları** kullanılarak inşa edilir.
- **Düzenli olarak taranır, analiz edilir ve güvenlik açıkları için test edilir.**
- Google, bu paketlerdeki güvenlik açıklarını **aktif olarak bulur ve düzeltir.**

> **Neden bu önemli?** Çünkü açık kaynak ekosisteminin en büyük zafiyeti, kimsenin sorumluluğu üstlenmemesidir — bir paket topluluk tarafından bakımı yapılıyor olabilir ama kimse onu düzenli olarak güvenlik açığına karşı taramıyor olabilir. Assured OSS, bu sorumluluğu Google'a devretmeni sağlar: Sen kendi paketlerini tek tek taramak yerine, **zaten taranmış, zaten güvenli** bir kaynaktan besleniyorsun.

## Cloud Build — Doğrulanabilir bir üretim kaydı

Cloud Build, kaynak kodunu ve doğrulanmış açık kaynak paketlerini içe aktarabilir ve derlemeni **Google Cloud altyapısında** çalıştırabilir. Ama güvenlik açısından asıl önemli olan şu: **Cloud Build, derleme hakkında doğrulanabilir metadata (metaveri) tutar.**

Bu metadata neye yarar? **Üretilen bir yapıtın (artifact), güvenilir kaynaklardan, güvenilir bir derleme sistemi tarafından oluşturulduğunu doğrulamana yardımcı olur.** Başka bir deyişle, elindeki container imajının "gerçekten senin belirlediğin kaynak koddan, senin belirlediğin süreçten geçerek" üretildiğini kanıtlayabilirsin — imajın araya bir yerde değiştirilmediğinden (tampered) emin olursun.

> **Sezgi:** Bunu bir ürünün üzerindeki "menşe sertifikası" gibi düşün. Sadece "bu ürün var" demek yetmez; "bu ürün şu fabrikada, şu hammaddelerle, şu süreçten geçerek üretildi" diyebilmen gerekir. Cloud Build'in metadata'sı, tam olarak bu sertifikayı sağlıyor.

## Artifact Registry ve Artifact Analysis — Depoda sürekli tarama

**Artifact Registry**, build yapıtlarını **saklamanı, güvenceye almanı ve yönetmeni** sağlar. Ama sadece saklamak yeterli değildir — depodaki yapıtların **güvenli kaldığından** da emin olman gerekir. İşte bu noktada **Artifact Analysis** devreye girer: **Artifact Registry'deki yapıtlar için güvenlik açıklarını proaktif olarak tespit eder.**

Artifact Analysis şunları yapar:

- **Temel container imajları (base images)** ile **Maven ve Go paketleri** için **entegre, hem talep üzerine (on-demand) hem otomatik taramalar** sağlar.
- Örneğin bir Java projesini Artifact Registry'ye push ettiğinde, Artifact Analysis, Maven yapıtlarının kullandığı **açık kaynak bağımlılıklar içindeki** güvenlik açıklarını tarar.
- İlk taramadan **sonra da durmaz** — Artifact Registry'deki taranmış imajların metadata'sını, **yeni güvenlik açıkları için sürekli olarak izlemeye devam eder.**

> **Neden bu "sürekli izleme" kısmı kritik?** Çünkü bir imaj push edildiği anda güvenli olsa bile, **yarın** o imajın içindeki bir bağımlılıkta yeni bir güvenlik açığı (CVE) keşfedilebilir. Eğer tarama sadece push anında yapılsaydı, bu yeni açık asla fark edilmezdi — imaj depoda "güvenli" etiketiyle sonsuza kadar kalırdı. Sürekli izleme, geçmişte push ettiğin imajların da güncel tehdit bilgisiyle yeniden değerlendirilmesini sağlar.

## Cloud Deploy — Kontrollü, izlenebilir dağıtım

**Cloud Deploy**, uygulamalarının teslimini, **önceden tanımlanmış bir sırayla**, bir dizi hedef ortama (target environment) otomatikleştirir. Doğrudan **Cloud Run ve GKE'ye** sürekli teslimi destekler — **tek tıkla onay ve rollback** ile.

Cloud Deploy ayrıca **dağıtılan uygulamalar için güvenlik içgörüleri (security insights) gösterir.** Yani Cloud Deploy sadece "bu imajı şuraya koy" demiyor — dağıttığın imajın güvenlik durumu hakkında sana görünürlük de sağlıyor.

> **Bunu Parça 1'e bağla:** Cloud Deploy, Parça 1'de gördüğün "dağıtım sistemi" bileşeninin somut karşılığıdır — staging'e dağıtıp testleri çalıştıran, release candidate'i üretime canary/blue-green olarak taşıyan, tek tıkla onay ve rollback sağlayan araç budur.

## Binary Authorization — "Sadece güvendiğim imaj çalışsın"

Buraya kadar gördüğün araçlar (Assured OSS, Cloud Build metadata, Artifact Analysis, Cloud Deploy) hep **bilgi ve görünürlük** sağlıyordu: şu paket güvenli, şu imaj şu kaynaktan geldi, şu imajda şu açık var. Ama bu bilgiyi **zorunlu bir kurala** çevirip **"bu koşulları sağlamayan hiçbir imaj çalışmasın"** demek isteseydin?

İşte tam olarak bunu **Binary Authorization** yapıyor: Yazılım tedarik zincirin boyunca bir **güven zinciri (chain of trust)** oluşturmana, sürdürmene ve doğrulamana yardımcı olur.

Binary Authorization'ın çalışma mantığı şöyle:

1. Bir imajın **belirli, gerekli bir süreçten başarıyla geçerek** oluşturulduğunu tasdik eden dijital belgeler olan **attestation'ları (tasdikler)** toplar. Örneğin "bu imaj güvenlik taramasından geçti" ya da "bu imaj entegrasyon testlerinden geçti" gibi attestation'lar olabilir.
2. Bir imajın, ancak bu attestation'lar **senin kuruluşunun politikasını karşıladığında** dağıtılmasını sağlar.
3. Politika ihlalleri bulunduğunda **seni uyarır.**

> **Sezgi:** Binary Authorization'ı, bir binaya girmeden önce güvenlik görevlisinin kontrol ettiği bir kimlik/rozet sistemi gibi düşün. Rozetin (attestation) üzerinde "güvenlik eğitimini tamamladı", "arka plan kontrolünden geçti" gibi damgalar (stamp) olmalı. Görevli (Binary Authorization), bu damgaları senin belirlediğin politikaya göre kontrol eder; eksik damgalı biri **binaya (üretim ortamına) giremez.** Sadece "imaj var mı" diye bakmıyor — "bu imaj **doğru süreçlerden geçerek** üretildi mi" diye bakıyor.

> **Sınav tuzağı — Artifact Analysis vs Binary Authorization:** İkisi de "güvenlik" kelimesiyle anılır ama farklı işler yapar. **Artifact Analysis**, depodaki imajları **tarar ve bilgi verir** — "bu imajda şu açık var" der, ama imajı **engellemez.** **Binary Authorization**, bu bilgiyi (attestation biçiminde) alır ve **zorunlu kılar** — politikayı karşılamayan bir imajın **dağıtılmasını fiilen engeller.** Biri "gözlem" katmanı, diğeri "uygulama/zorunluluk (enforcement)" katmanıdır.

## GKE ve Cloud Run — Çalışma zamanında güvenlik

Pipeline sadece "derleme ve dağıtım" ile bitmiyor; imaj **çalışmaya başladıktan sonra** da güvenlik devam etmeli. Hem GKE hem Cloud Run, pipeline güvenliğine kendi katkılarını sunar:

- **GKE**, container güvenliğini değerlendirebilir ve **cluster ayarları, workload konfigürasyonu ve güvenlik açıkları** konusunda aktif rehberlik verir. GKE cluster'larını ve workload'larını değerlendirip, güvenliğini artırmak için **eyleme geçirilebilir (actionable) öneriler** sunar.
- **Cloud Run**, çalışan servislerdeki derlemeler ve güvenlik açıkları hakkında içgörüler gösteren bir **güvenlik paneli (security panel)** içerir.

> **Neden bu "çalışma zamanı" katmanı da gerekli?** Çünkü bir imaj, dağıtıldığı anda güvenli olsa bile, **çalışırken** yanlış yapılandırılmış olabilir (örneğin gereğinden fazla izinle çalışıyor olabilir) ya da zamanla yeni açıklar keşfedilebilir. Derleme-zamanı güvenliği (Assured OSS, Artifact Analysis, Binary Authorization) ile çalışma-zamanı güvenliği (GKE/Cloud Run panelleri) birlikte, pipeline'ın **baştan sona** korunmasını sağlar.

## Software Delivery Shield'ı tek tabloda toparlamak

| Pipeline aşaması | Risk | Koruyan servis | Ne yapar |
| --- | --- | --- | --- |
| Bağımlılık/kaynak seçimi | Güvensiz açık kaynak paket | Assured OSS | Google tarafından doğrulanmış, sürekli taranan Java/Python paketleri |
| Derleme (build) | İmajın kaynağı/sürecinin doğrulanamaması | Cloud Build (metadata) | Doğrulanabilir üretim kaydı — güvenilir kaynak + güvenilir süreç kanıtı |
| Depolama (storage) | Depodaki imajda bilinmeyen güvenlik açığı | Artifact Registry + Artifact Analysis | Otomatik/on-demand tarama + sürekli izleme |
| Dağıtım (deploy) | Kontrolsüz, izlenemeyen dağıtım | Cloud Deploy | Sıralı hedef ortamlara dağıtım, tek tık onay/rollback, güvenlik içgörüleri |
| Politika uygulama | Onaylanmamış/güvensiz imajın çalışması | Binary Authorization | Attestation'lara dayalı zorunlu politika, güven zinciri |
| Çalışma zamanı | Yanlış yapılandırma, yeni açıklar | GKE / Cloud Run güvenlik panelleri | Aktif rehberlik, actionable öneriler, çalışan servis içgörüleri |

---

# PARÇA 3 — Container'lar: Uygulamanı Paketlemenin Doğru Yolu

## Neden container? VM'lerin çözemediği problem

Container'lar, uygulamaları paketlemek ve dağıtmak için **tercih edilen yöntemdir.** Bunu anlamak için önce eski yöntemi — **sanal makineler (VM'ler)** aracılığıyla donanım sanallaştırmasını — hatırlamak gerekiyor.

VM'ler, birbirlerinden **kısmen her VM'nin kendi işletim sistemi kopyasına sahip olmasıyla** izole edilir. Bu yaklaşımın sorunu şu: **İşletim sistemleri açılışta (boot) yavaş olabilir ve kaynak açısından ağır (resource-heavy) olabilir.** Her VM, tam bir işletim sistemi kopyası taşıdığı için, hem disk/bellek açısından pahalıdır hem de her açılışta bu tam işletim sisteminin ayağa kalkmasını beklemen gerekir — genellikle **bir dakika veya daha fazla.**

**Container-based virtualization (container tabanlı sanallaştırma)**, donanım sanallaştırmasına bir **alternatiftir.** Bu problemleri, modern işletim sistemlerinin **yerleşik (built-in) yeteneklerini** kullanarak container ortamlarını birbirinden izole ederek çözer.

## Nasıl çalışır? Process izolasyonundan container'a

Container'ların nasıl çalıştığını anlamak için en temel birimden başlamak gerekiyor: **process (süreç)**. Bir process, **çalışan bir programdır.** Linux ve Windows'ta, çalışan process'lerin **bellek adres alanları (memory address spaces)** uzun zamandır birbirinden izole edilmiştir — bu, işletim sistemlerinin temel bir güvenlik/kararlılık özelliğidir.

Popüler container implementasyonları, **tam olarak bu izolasyon üzerine inşa edilir.** Container'lar, işletim sisteminin **ek özelliklerinden** yararlanır: process'lere **kendi namespace'lerine (isim uzayı) sahip olma** ve **diğer process'lerin kaynaklara erişimini sınırlama** yeteneği verir.

Sonuç olarak container'lar, VM'lerden **çok daha hızlı başlar ve çok daha az kaynak kullanır** — çünkü **her container'ın kendi işletim sistemi kopyası yoktur.** Bunun yerine, geliştiriciler her container'ı **işi yapmak için gereken minimal bir yazılım kütüphaneleri kümesiyle** yapılandırır. Hafif bir **container runtime (çalışma zamanı)**, container'ın başlatılıp çalıştırılması için gereken **"tesisatçılık" (plumbing) işlerini** yapar — gerektiğinde **kernel'e (çekirdek)** çağrı yaparak. Container runtime, aynı zamanda **imaj formatını** da belirler.

> **Sezgi:** VM'yi, her katılımcının kendi tam evini (mutfağı, banyosu, elektrik tesisatıyla) kurmak zorunda olduğu bir apartman kompleksi gibi düşün — her ev bağımsız ve tam izole, ama kurulumu yavaş ve pahalı. Container'ı ise, ortak bir bina altyapısını (elektrik, su, temel — yani işletim sisteminin kernel'i) paylaşan ama her dairenin kendi kilitli kapısı ve kendi eşyaları (namespace, kaynak sınırı) olan bir apartman gibi düşün. Ortak altyapı paylaşıldığı için taşınmak (başlatmak) çok daha hızlıdır, ama her daire yine de birbirinden **izole**dir.

Container'lar hem **Cloud Run**'a hem **GKE**'ye dağıtılabilir — bu, container'ların "nereye deploy edeceğim" sorusuna Google Cloud'un iki farklı cevabı olduğunu gösterir: tam yönetilen, serverless bir seçenek (Cloud Run) ve tam kontrollü, orkestre edilmiş bir seçenek (GKE).

## Container VM'ye kıyasla ne kazandırır? Üç somut fayda

Peki bir container, bir VM'nin sunmadığı neyi sunuyor? Modül burada üç somut faydayı sıralıyor.

**1. Sorumluluk ayrımı (separation of responsibility).** Geliştiriciler, uygulama mantığına (application logic) ve uygulamanın ihtiyaç duyduğu bağımlılıklara odaklanabilir. Uygulamayı dağıtacak ve yönetecek olan IT operasyon ekipleri, **yazılım sürümleri ve konfigürasyonlar** gibi uygulama detaylarıyla uğraşmak zorunda kalmaz. Container, bu iki dünyayı net bir sınırla ayırır: içeride ne olduğu geliştiricinin işi, dışarıda nasıl çalıştığı operasyonun işidir.

**2. İş yükü taşınabilirliği (workload portability).** Container'lar hafiftir ve **bir geliştiricinin dizüstü bilgisayarından, on-premises'teki bir VM'e ya da herhangi bir buluta kadar** hemen hemen her yerde çalışabilir. Bir geliştiricinin dizüstünde test ettiği ve bir entegrasyon ortamında test edilen **aynı uygulama**, üretim ortamında da çalışabilir. Bu taşınabilirlik, uygulamayı geliştirme yaşam döngüsü boyunca **terfi ettirmeyi (promotion)** basitleştirir ve iş yüklerini bulutlar ile veri merkezleri arasında **minimal efor** ile taşımana olanak tanır.

**3. Uygulama izolasyonu (application isolation).** Container'lar, **CPU, bellek, depolama ve ağ kaynaklarını işletim sistemi seviyesinde sanallaştırır.** Uygulamalar, fiilen **kendi ortamlarında** çalışır — bu da aynı donanım üzerinde çalışan container'lı uygulamaların, birbirini etkilemeden **farklı bağımlılık sürümlerini** kullanabilmesini sağlar. Sadece işletim sistemini soyutlayarak (tüm sanal bilgisayarı değil), bir container **saniyenin bir kısmında "açılabilir" (boot).** Bir sanal makine ise tipik olarak **bir dakika veya daha fazla** sürer.

| Özellik | Sanal Makine (VM) | Container |
| --- | --- | --- |
| İzolasyon birimi | Donanım (her VM kendi OS kopyasına sahip) | İşletim sistemi (process namespace + kaynak limiti) |
| Başlangıç süresi | ~1 dakika veya daha fazla | Saniyenin bir kısmı |
| Kaynak ağırlığı | Ağır (her VM tam bir OS taşır) | Hafif (minimal kütüphane kümesi) |
| Taşınabilirlik | Sınırlı (hipervizöre/altyapıya bağımlı) | Yüksek (laptop → on-prem → herhangi bir bulut) |
| Sorumluluk ayrımı | Daha bulanık | Net (uygulama mantığı vs operasyon) |

> **Sınav tuzağı — "container bir mini VM'dir" yanılgısı:** Container'ı küçük, hafifletilmiş bir VM gibi düşünmek yanlıştır. VM, **donanımı** sanallaştırır ve her biri **kendi işletim sistemi kopyasını** çalıştırır. Container ise **işletim sistemini** sanallaştırır — tek bir işletim sistemi çekirdeği üzerinde, process izolasyonu ve namespace'ler aracılığıyla birden fazla izole ortam çalıştırır. Soru "her ortamın kendi OS'u var mı" diye soruyorsa cevap VM'dir; "OS seviyesinde izole edilmiş, hafif, hızlı başlayan ortam" diye soruyorsa cevap container'dır.

## Container imajı — her yerde aynı davranışın sırrı

**Container imajı (container image)**, uygulaman için **eksiksiz bir pakettir** — uygulamanın binary'sini ve uygulamanın çalışması için gereken **tüm yazılımı** içerir. Aynı container imajını geliştirme, test ve üretim ortamlarında dağıttığında, **uygulaman her ortamda aynı şekilde davranır.**

> **Neden bu kadar önemli?** Çünkü geleneksel dağıtımların en büyük kâbusu "benim makinemde çalışıyordu" cümlesidir — bir ortamda çalışan kodun başka bir ortamda çalışmama sebebi genellikle **ortam farklarıdır** (farklı kütüphane sürümü, farklı işletim sistemi ayarı, eksik bir bağımlılık). Container imajı, uygulamanın çalışması için gereken **her şeyi** kendi içine kapattığı için, bu "ortam farkı" sorununu kökünden ortadan kaldırır. Test ettiğin şey, üretime giden şeyle **bit bit aynıdır.**

Bu ilke, Parça 1'de gördüğün pipeline'ın neden bu kadar güvenilir olabildiğinin de temelidir: staging'de test edilen imaj ile üretime dağıtılan imaj **aynı imajdır** — aralarında yeniden derleme, yeniden paketleme gibi bir fark yoktur.

---

# PARÇA 4 — Cloud Build ile Container İmajı Oluşturma

## Neden Cloud Build?

Şimdiye kadar "derleme sistemi container imajı üretir" dedik ama bu imajı **kim, nasıl** üretiyor? Cevap: **Cloud Build.**

**Cloud Build**, uygulaman için bir **Docker container imajı** oluşturan ve bu imajı **Artifact Registry**'ye gönderen derleme hatları (build pipelines) kurmanı sağlayan **tam yönetilen (fully managed) bir servistir.**

Buradaki "tam yönetilen" ifadesi önemli bir vaadi taşıyor: **Derleme araçlarını ve container imajlarını bir derleme makinesine indirmen ya da kendi derleme altyapını yönetmen gerekmez.** Kendi CI sunucunu kurup bakımını yapmak (sürüm güncellemeleri, kapasite planlama, güvenlik yamaları) yerine, bu sorumluluğu tamamen Google'a devredersin.

Artifact Registry ile Cloud Build'i birlikte kullanarak, kod deposuna commit yaptığında **otomatik olarak tetiklenen** derleme hatları kurabilirsin — bu da tam olarak Parça 1'de gördüğün "continuous integration" halkasının somut gerçekleştirimidir.

## Build trigger — Ne zaman derleme başlasın?

Cloud Build'de, bir **trigger type'a (tetikleyici tipi)** göre çalıştırılan bir **build trigger (derleme tetikleyicisi)** oluşturabilirsin. Trigger type, bir derlemenin şuna göre tetiklenip tetiklenmeyeceğini belirtir:

- Depodaki **belirli bir dala (branch)** yapılan commit'ler, ya da
- **Belirli bir etiket (tag)** içeren commit'ler.

> **Sezgi:** Bunu bir dedektör gibi düşün — sürekli depoyu dinliyor ve "şu koşul sağlandı mı" diye kontrol ediyor. Koşul sağlanınca (doğru dala commit geldi, ya da doğru etiketle bir commit geldi), otomatik olarak derlemeyi başlatıyor. Bu, Parça 1'deki "feature branch'e commit CI'ı tetikler, main branch'e push CD'yi tetikler" ayrımının Cloud Build'deki teknik karşılığıdır — farklı trigger'lar farklı dallara/etiketlere bağlanarak bu ayrımı somutlaştırır.

## Build configuration file — Pipeline'ın tarifi

Derleme hattındaki adımları belirten bir **build configuration file (derleme yapılandırma dosyası)** oluşturursun. Bu dosyadaki **step'ler (adımlar)**, uygulamanı derlemek için çalıştıracağın **komutlara ya da script'lere benzer.**

Burada kavramsal olarak çok önemli bir nokta var: **Her build step, Cloud Build tarafından derleme çalıştırıldığında invoke edilen (çağrılan) bir Docker container'dır.**

- **`name` (step adı)**, derleme adımı için invoke edilecek container'ı tanımlar.
- **`images` özniteliği**, bu derleme yapılandırması tarafından oluşturulacak container imajının adını içerir.

Cloud Build, sana **farklı kod depoları kullanma, container imajlarını arama yapılabilmesi için etiketleme (tag) ve veri indirme/işleme gibi işlemler yapan build step'leri oluşturma** esnekliği sağlar. Build configuration, **YAML veya JSON** formatında belirtilebilir.

> **Neden her step bir Docker container?** Bu tasarım kararı, tutarlılık ve izolasyon sağlar. Her adım kendi izole ortamında (kendi container'ında) çalışır, yani bir adımın kullandığı araçlar ya da kütüphaneler, başka bir adımı **kirletmez.** Örneğin "test çalıştır" adımı bir Python container'ı kullanırken, "imajı derle" adımı bir Docker-in-Docker container'ı kullanabilir — ikisi birbirinden tamamen bağımsızdır.

## `/workspace` — Adımlar arasında veri paylaşımı

Peki bir adımın ürettiği çıktıyı, bir sonraki adım nasıl kullanır? Cevap: **`/workspace` dizini.**

Cloud Build, kaynak kodunu, bir derleme adımıyla ilişkili Docker container'ının **`/workspace` dizinine mount eder (bağlar).** Her build step'in ürettiği yapıtlar **`/workspace` klasöründe kalıcı hale gelir (persist edilir)** ve **bir sonraki build step tarafından kullanılabilir.**

> **Sezgi:** `/workspace` dizinini, birbirini takip eden vardiyaların paylaştığı ortak bir tezgah gibi düşün. İlk vardiya (ilk build step) tezgaha bir yarı-mamul bırakır; ikinci vardiya (ikinci build step) geldiğinde, o yarı-mamulü tezgahtan alıp kaldığı yerden devam eder. Her vardiya kendi izole "odasında" (kendi container'ında) çalışıyor olsa da, tezgah (`/workspace`) ortak ve paylaşılan kalıyor.

Bu mekanizma sayesinde, çok adımlı bir pipeline kurabilirsin: örneğin bir adım bağımlılıkları indirir, bir sonraki adım kodu derler, bir sonraki adım testleri çalıştırır, son adım da Docker imajını paketler — ve her adım, önceki adımın `/workspace`'te bıraktığı çıktıyı kullanabilir.

## Artifact Registry'ye otomatik gönderim

Cloud Build, derlenen container imajını **otomatik olarak Artifact Registry'ye push eder.** Artifact Registry içinde, belirli bir container için derlemelerin **durumunu (status) ve geçmişini (history)** görüntüleyebilirsin.

Bu, Parça 1'deki "uygulama yapıtları bir depoda saklanır" cümlesinin ve Parça 2'deki "Artifact Registry + Artifact Analysis" güvenlik katmanının, Cloud Build tarafında nasıl **otomatik olarak** gerçekleştiğini gösteriyor: sen elle bir push komutu çalıştırmıyorsun, Cloud Build derleme tamamlanır tamamlanmaz bunu senin adına yapıyor.

## Pub/Sub bildirimleri — Derleme durumuna tepki verme

Son olarak, Cloud Build, **derleme durumu bildirimlerini Pub/Sub'a yayımlayabilir.** Bu bildirimlere **abone olarak (subscribe)**, derleme durumuna ya da diğer özniteliklere göre **eyleme geçebilirsin.**

> **Neden bu değerli?** Çünkü bu, pipeline'ı **kendi otomasyon sistemine entegre etmenin** kapısını açıyor. Örneğin bir derleme başarısız olduğunda otomatik olarak ekibe bir Slack bildirimi gönderen bir Cloud Function tetikleyebilirsin; ya da bir derleme başarıyla tamamlandığında otomatik olarak bir sonraki pipeline aşamasını (örneğin Cloud Deploy'u) tetikleyebilirsin. Pub/Sub, Cloud Build'i izole bir kutu olmaktan çıkarıp, daha geniş bir olay güdümlü (event-driven) mimarinin parçası haline getiriyor.

---

# Hangi Servisi Ne Zaman Kullanırım? (Karar Bölümü)

Modülün tüm parçalarını, bir geliştiricinin karşılaşacağı somut senaryolar üzerinden toparlayalım:

| Senaryo | Doğru servis / kavram |
| --- | --- |
| Feature branch'e commit yapıldığında otomatik derleme+test istiyorum | Continuous Integration (Cloud Build trigger) |
| Main branch'e push sonrası staging'de test edip, üretime **manuel onayla** dağıtmak istiyorum | Continuous Delivery |
| Testler geçtiği sürece üretime **hiç insan müdahalesi olmadan** dağıtmak istiyorum | Continuous Deployment |
| Yeni sürümü önce trafiğin küçük bir yüzdesine açıp kademeli artırmak istiyorum | Canary release |
| İki eş ortam arasında anlık trafik geçişi ve anlık rollback istiyorum | Blue-green release |
| Açık kaynak Java/Python paketlerimin Google tarafından doğrulanmış olmasını istiyorum | Assured OSS |
| Bir imajın güvenilir kaynaktan, güvenilir süreçle üretildiğini kanıtlamak istiyorum | Cloud Build build metadata |
| Artifact Registry'deki imajlardaki güvenlik açıklarını sürekli taramak istiyorum | Artifact Analysis |
| Sıralı hedef ortamlara, tek tık onay/rollback ile dağıtım yapmak istiyorum | Cloud Deploy |
| Politikayı karşılamayan (attestation eksik) imajların çalışmasını **engellemek** istiyorum | Binary Authorization |
| GKE cluster ayarları ve workload güvenliği için öneriler almak istiyorum | GKE güvenlik değerlendirmesi |
| Çalışan Cloud Run servislerindeki güvenlik açıklarını görmek istiyorum | Cloud Run güvenlik paneli |
| Uygulamamı laptop → test → üretim boyunca **aynı şekilde** çalıştırmak istiyorum | Container + container image |
| Uygulamamı hızlı başlatıp az kaynakla, izole biçimde çalıştırmak istiyorum (VM yerine) | Container |
| Docker imajı derleyip Artifact Registry'ye göndermek istiyorum, kendi build altyapımı kurmak istemiyorum | Cloud Build |
| Derleme adımları arasında ara çıktıları paylaşmak istiyorum | `/workspace` dizini |
| Derleme başarılı/başarısız olduğunda otomatik bir eylem tetiklemek istiyorum | Cloud Build → Pub/Sub bildirimleri |

---

# Toplu Özet (Hızlı Tekrar)

**Modülün üç katmanı:** (1) CI/CD pipeline'ın anatomisi — commit'ten üretime giden otomatik yol. (2) Bu yolun güvenliği — Software Delivery Shield şemsiyesi altındaki servisler. (3) Yolun somut yapı taşları — container'lar ve Cloud Build.

**CI/CD'nin nedeni:** Yüksek yayın hızı için derleme/test/yayın süreçlerinin otomatikleştirilmesi gerekir; pipeline stabil ve tekrarlanabilir bir süreç sağlar.

**Üç halka:** Continuous Integration (feature branch commit → otomatik derleme+test → Artifact Registry'ye yapıt), Continuous Delivery (main branch push → staging test → release candidate → **manuel onay** → canary/blue-green → Cloud Monitoring → switch-over ya da rollback), Continuous Deployment (Delivery ile aynı ama **manuel onay yok**, otomatik üretim dağıtımı).

**Canary vs blue-green:** Canary = trafiğin küçük bir yüzdesine kademeli açma. Blue-green = iki eş ortam arasında anlık trafik geçişi/rollback.

**Software Delivery Shield:** Fully managed, uçtan uca yazılım tedarik zinciri güvenlik çözümü — CI/CD'nin her adımını korur. Bileşenleri: Assured OSS (doğrulanmış Java/Python paketleri), Cloud Build metadata (güvenilir kaynak/süreç kanıtı), Artifact Registry + Artifact Analysis (otomatik/on-demand tarama + sürekli izleme), Cloud Deploy (sıralı dağıtım, tek tık onay/rollback, güvenlik içgörüleri), Binary Authorization (attestation tabanlı zorunlu politika, güven zinciri), GKE/Cloud Run güvenlik panelleri (çalışma zamanı güvenliği).

**Artifact Analysis vs Binary Authorization:** Analysis = gözlem/tarama (bilgi verir, engellemez). Binary Authorization = zorunluluk/enforcement (politikayı karşılamayan imajı fiilen engeller).

**Container vs VM:** VM donanımı sanallaştırır, her biri kendi OS kopyasına sahiptir (yavaş, ağır). Container işletim sistemini sanallaştırır — process izolasyonu + namespace'ler üzerine kurulu (hızlı, hafif). Container üç fayda sağlar: sorumluluk ayrımı, iş yükü taşınabilirliği, uygulama izolasyonu. Container saniyenin bir kısmında açılır; VM bir dakika veya fazla sürer.

**Container image:** Uygulama binary'si + gereken tüm yazılımı içeren eksiksiz paket; dev/test/prod'da aynı imaj = aynı davranış.

**Cloud Build:** Fully managed derleme servisi; Docker imajı oluşturur, Artifact Registry'ye push eder; kendi build altyapını yönetmene gerek yok. Trigger type = branch ya da tag bazlı otomatik tetikleme. Build configuration file (YAML/JSON) = step'lerin tarifi; her step bir Docker container'dır (`name` = hangi container, `images` = üretilecek imaj adı). `/workspace` = adımlar arası paylaşılan, kalıcı dizin. Derleme sonunda otomatik Artifact Registry push + Pub/Sub durum bildirimleri.

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Continuous Integration vs Delivery vs Deployment:** CI = feature branch commit → otomatik derle+test → yapıt Artifact Registry'de. Delivery = main branch push → staging test → release candidate → **manuel onay** → üretime canary/blue-green. Deployment = Delivery ile aynı ama **manuel onay yok**, tamamen otomatik.
- **Canary vs blue-green:** Canary = trafiğin küçük bir kesimine kademeli açma. Blue-green = iki eş ortam arasında anlık, tam geçiş/rollback.
- **Software Delivery Shield = şemsiye kavram**, kendisi ayrı bir "araç" değil — Assured OSS, Cloud Build metadata, Artifact Analysis, Cloud Deploy, Binary Authorization, GKE/Cloud Run güvenlik panellerinin **toplamıdır.**
- **Artifact Analysis vs Binary Authorization:** Analysis tarar ve **bilgilendirir** (gözlem). Binary Authorization, attestation'lara dayanarak politikayı **zorunlu kılar** ve uygunsuz imajı **engeller** (enforcement).
- **Assured OSS neyi kapsar:** Sadece **Java ve Python** açık kaynak paketleri — Google tarafından doğrulanmış, güvenli pipeline'larla inşa edilmiş, sürekli taranan.
- **Container vs VM:** VM = donanım sanallaştırma, her biri kendi OS kopyası, yavaş (~1 dk+) ve ağır. Container = OS seviyesinde sanallaştırma (process izolasyonu + namespace), hızlı (saniyenin bir kısmı) ve hafif. Container "mini VM" değildir.
- **Container'ın üç faydası:** Sorumluluk ayrımı (dev vs ops), iş yükü taşınabilirliği (laptop → on-prem → herhangi bulut), uygulama izolasyonu (aynı donanıkta farklı bağımlılık sürümleri çakışmadan çalışır).
- **Container image:** Binary + tüm gerekli yazılımı içeren eksiksiz paket; dev/test/prod'da **aynı imaj** kullanılır, bu da "benim makinemde çalışıyordu" sorununu ortadan kaldırır.
- **Cloud Build build step = bir Docker container:** `name` özniteliği hangi container'ın invoke edileceğini, `images` özniteliği üretilecek imajın adını belirtir. Build config YAML ya da JSON'dır.
- **`/workspace`:** Kaynak kodun mount edildiği ve build step'lerin çıktısının **kalıcı olarak paylaşıldığı** dizin — bir adımın ürettiği, bir sonraki adım tarafından kullanılabilir.
- **Trigger type:** Branch bazlı ya da tag bazlı — hangi commit'in derlemeyi tetikleyeceğini belirler.
- **Cloud Build → Artifact Registry → Pub/Sub:** Derleme biter bitmez imaj otomatik push edilir; durum bildirimleri Pub/Sub'a yayımlanır, buna abone olup eyleme geçebilirsin.

---

> **Kapanış:** Bu modül, bir satır kodun commit edilmesinden kullanıcının eline ulaşmasına kadar geçen yolu üç farklı açıdan gösterdi. Önce bu yolun **anatomisini** — CI'ın otomatik derleme/test halkasını, Delivery'nin manuel onaylı üretim geçişini, Deployment'ın tamamen otomatik versiyonunu, canary ve blue-green gibi güvenli yayın stratejilerini — öğrendin. Sonra bu yolun **her adımının** nasıl kötüye kullanılabileceğini ve Software Delivery Shield şemsiyesi altındaki her servisin (Assured OSS'ten Binary Authorization'a, Artifact Analysis'ten GKE/Cloud Run güvenlik panellerine) bu riskin hangi parçasını kapattığını gördün. Son olarak, bu yolun somut yapı taşlarına indin: container'ların VM'lere göre neden bu kadar hızlı, hafif ve taşınabilir olduğunu, ve Cloud Build'in bir Docker imajını nasıl derleyip `/workspace` üzerinden adımlar arası veri paylaşarak Artifact Registry'ye gönderdiğini öğrendin. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; özellikle Delivery/Deployment ayrımını, Artifact Analysis (gözlem) ile Binary Authorization (zorunluluk) arasındaki farkı, ve container'ın "mini VM" olmadığını unutma. Bir konuda takılırsan ilgili parçaya dön ve "bu servis, pipeline'ın hangi somut riskini ya da hangi somut adımını çözüyor" sorusunu yeniden kur.
