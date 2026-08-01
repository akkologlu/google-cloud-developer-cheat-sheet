# Kimlik Doğrulama ve Yetkilendirme (Authentication and Authorization) — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 4 — Handling Authentication and Authorization** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Amaç, IAM rollerini ya da `gcloud` komutlarını ezberletmek değil; **"kim olduğumu nasıl kanıtlarım" (authentication)** ile **"kanıtladıktan sonra neye izinliyim" (authorization)** sorularının birbirinden nasıl ayrıldığını, ve Google Cloud'un bu iki soruya kaç farklı — ama hepsi belli bir senaryoya göre en doğru olan — cevap sunduğunu sindirmendir. Acele etme; her yöntemi "bu hangi durumda doğar, hangi durumda yanlış tercih olur" gözüyle oku. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül neyi öğretiyor ve neden önemli?

Bir uygulama yazarken karşına er ya da geç şu korkutucu soru çıkar: **Kullanıcı verisini nasıl güvenceye alacağım?** Kimlik doğrulama ve yetkilendirmeyi sıfırdan, kendi elinle yazmak cazip görünebilir — bir giriş formu, bir parola tablosu, birkaç `if` bloğu... Ama bu yol, göründüğünden çok daha tehlikelidir. Parola hash'leme, oturum yönetimi, token süresi, brute-force koruması, çok faktörlü doğrulama gibi ince ayrıntıların her biri, atlandığında saldırıya açık bir kapı haline gelir. Kendi authentication/authorization kodunu yazmak, güvenlik uzmanlığı gerektiren bir işi amatörce üstlenmek demektir.

Google Cloud burada devreye giriyor: **IAM (Identity and Access Management)** ve **Identity Platform Authentication** ile, bu riski kendine almadan basit ve güvenli bir çözüme ulaşırsın. Bu modül, tam olarak bu ikilinin etrafında beş ana başlığı işliyor:

1. **IAM prensipleri** — kimin, hangi kaynak üzerinde, hangi işlemi yapabileceğini nasıl tanımlarsın.
2. **Servis hesapları ve diğer yöntemlerle** uygulamalarını Google Cloud API'lerini çağıracak şekilde doğrulama/yetkilendirme — ve **hangi yöntemi ne zaman kullanacağın.**
3. **Identity-Aware Proxy (IAP)** ile bulut uygulamalarına erişimi kontrol etme.
4. **Firebase SDK** ile federe kimlik yönetimini (federated identity management) kolayca uygulama.
5. **Secret Manager** ile uygulamanın ihtiyaç duyduğu kimlik bilgilerini güvenle saklama.

Bu beş başlık aslında tek bir hikâyenin parçalarıdır: **Önce "kim bu?" sorusunu çöz (authentication), sonra "bu kişi/uygulama neye izinli?" sorusunu çöz (authorization), sonra bu ikisini uygulamana koddan hiç dokunmadan, sızıntıya kapalı biçimde nasıl bağlayacağını öğren.** Şimdi baştan başlayalım.

> **Önceki modülle bağ:** İkinci modülde "kullanıcı yönetimi eforunu kimlik yönetimini devrederek en aza indir" demiştik ve Identity Platform ile Firebase Authentication'ı kısaca tanıtmıştık. Bu modül tam olarak o cümlenin altını dolduruyor. Yine ikinci modülde, config'i koddan ayırıp ortam değişkenlerinde tutmayı öğretmiştik — Secret Manager, bu ilkenin "hassas veri" versiyonudur: parola ve API key'leri de koda gömmezsin, güvenli bir servise koyarsın. Üçüncü ve ikinci modüllerde servis hesabı kavramına birkaç kez değinmiştik ("Cloud Client Libraries genelde bir servis hesabı kimliğiyle çalışır" demiştik); bu modül servis hesabını en ince ayrıntısına kadar açıyor.

---

# PARÇA 1 — IAM Yetkilendirme (Authorization)

## Yetkilendirmenin üçlüsü: Kim, Neye, Hangi Kaynak Üzerinde

IAM ile erişim kontrolünü şu üç sorunun cevabıyla tanımlarsın:

- **Kim (principal):** Erişimi talep eden taraf kimdir?
- **Ne erişimi (role):** Bu tarafa ne yapma izni verildi?
- **Hangi kaynak (resource):** Bu izin, hangi somut kaynak üzerinde geçerli?

Bu üçlü, IAM'in tüm mantığının omurgasıdır. Bir IAM politikasını okurken her zaman bu üç boşluğu doldurmaya çalış: "**Kim**, **neyi**, **nerede** yapabiliyor?"

Bunun üzerine tüm sistemi yöneten tek bir altın kural gelir: **En az ayrıcalık ilkesi (principle of least privilege).** Bu ilke, her principal'a **sadece işini yapmak için gerçekten gereken** kaynaklara erişim vermeni söyler — ne bir fazla, ne bir eksik. Neden bu kadar önemli? Çünkü her fazladan izin, saldırı yüzeyini (attack surface) büyütür. Bir hesap ele geçirilirse, o hesabın sahip olduğu her fazladan izin, saldırganın erişebileceği her fazladan kaynak demektir. Bu ilkeyi modül boyunca defalarca göreceksin — çünkü hem rol seçiminde hem servis hesabı tasarımında hem de Secret Manager'da tekrar tekrar karşına çıkıyor.

## "Kim" — Principal türleri

IAM politikasındaki "kim" kısmına **principal** denir. Google Cloud'da beş tür principal vardır, ve bunları ikiye ayırarak öğrenmek en sağlıklısı: **kimlik oluşturabilenler** ve **sadece izin yönetimini kolaylaştıranlar.**

### Kimlik oluşturabilen principal'lar

**Google Account.** Bir geliştiriciyi, bir yöneticiyi ya da Google Cloud ile etkileşen herhangi bir **gerçek kişiyi** temsil eder. Google Cloud konsoluna giriş yaptığında kullandığın hesap budur.

**Service account (servis hesabı).** Bireysel bir son kullanıcı yerine, bir **uygulamayı ya da compute workload'ını** temsil eden bir hesaptır. Google Cloud üzerinde barındırılan kodu çalıştırdığında, o kod **senin belirttiğin servis hesabı olarak** çalışır. Uygulamanın farklı mantıksal bileşenlerini (örneğin "faturalama servisi", "bildirim servisi") temsil etmek için istediğin kadar servis hesabı oluşturabilirsin — her bileşene kendi kimliğini ve kendi ince ayarlı iznini verirsin.

> **Sezgi:** Google Account "insan" demek, service account "makine/uygulama" demek. Sen konsola girerken Google Account kullanırsın; kodun Google Cloud API'lerini çağırırken service account kullanır.

### Kimlik OLUŞTURAMAYAN, sadece izin yönetimini kolaylaştıran principal'lar

Bu üç principal türü sık sık birbirine ve yukarıdaki ikisine karıştırılır, o yüzden özellikle dikkatli ol.

**Google group.** Google Account'ların ve servis hesaplarının **adlandırılmış bir koleksiyonudur**; benzersiz bir e-posta adresiyle tanımlanır. Ne işe yarar? Erişim kontrollerini **tek tek değil, topluca** uygulamana ve değiştirmene izin verir — bir gruba rol verdiğinde, o gruptaki herkes o rolü kazanır. **Ama kritik nokta şu: Google grubunun bir giriş kimlik bilgisi (login credential) yoktur.** Bir Google grubunu, bir kaynağa erişim talep etmek için **kimlik oluşturmakta kullanamazsın.** Grup, sadece "bu insanlara/hesaplara toplu olarak izin ver" demenin kolay yoludur; kendisi asla "ben buyum" diyerek bir isteği imzalayamaz.

**Google Workspace account.** İçerdiği tüm Google Account'ların **sanal bir grubunu** temsil eder ve kuruluşunun internet alan adıyla (örneğin `example.com`) ilişkilendirilir. Google grubu gibi, bu da kimlik oluşturmak için kullanılamaz ama **kolay izin yönetimi** sağlar — "example.com'daki herkese şu izni ver" demeni mümkün kılar.

**Cloud Identity domain.** Google Workspace account'a çok benzer: o da kuruluştaki tüm Google Account'ların sanal bir grubunu temsil eder. Ancak fark şu: **Cloud Identity domain kullanıcılarının Google Workspace uygulamalarına ve özelliklerine (Gmail, Docs, Drive gibi) erişimi yoktur.** Bir kuruluş Google Workspace müşterisi değilse ama yine de kullanıcılarını merkezi biçimde yönetmek istiyorsa Cloud Identity'yi kullanır. Google Workspace account gibi, bu da kimlik oluşturmak için kullanılamaz.

> **Sınav tuzağı — kimlik oluşturan vs oluşturmayan:** Bu, modülün en sık sorulan ayrımlarından biridir. **Google Account** ve **service account** kimlik oluşturabilir — yani bir API isteğini "ben buyum" diyerek imzalayabilirler. **Google group, Google Workspace account ve Cloud Identity domain ise KİMLİK OLUŞTURAMAZ** — bunların hiçbirinin login credential'ı yoktur; sadece bir kullanıcı koleksiyonuna **toplu izin uygulamanın kolay yoludurlar.** Soruda "bu principal'la bir API isteğini doğrulayabilir miyim" diye soruluyorsa, cevap grup/Workspace/Cloud Identity için her zaman **hayır**dır.

> **Sınav tuzağı — Google Workspace account vs Cloud Identity domain:** İkisi de "organizasyondaki herkesin sanal grubu" tanımına uyar ve bu yüzden karıştırılırlar. Ayrım şu: **Google Workspace account**, Workspace uygulamalarına (Gmail, Docs, Drive...) erişimi olan kullanıcıları kapsar. **Cloud Identity domain** ise bu Workspace uygulama erişimi **olmayan** kullanıcılar içindir — kuruluş Workspace müşterisi olmadan kimlik yönetimi istediğinde kullanılır.

## "Hangi kaynak" — Resource

Resource, IAM politikasının uygulandığı somut Google Cloud varlığıdır. Örnekler: **projeler, Compute Engine örnekleri (instance), Cloud Storage bucket'ları, Artifact Registry repository'leri.** Kaynak hiyerarşisini (organizasyon → klasör → proje → kaynak) ilk modülden hatırlarsın; IAM politikaları bu hiyerarşinin herhangi bir seviyesinde tanımlanabilir ve aşağı doğru miras alınır.

## "Ne erişimi" — Permission ve Role

**Permission (izin)**, bir kaynak üzerinde hangi işlemlerin yapılmasına izin verildiğini belirler. İzinler, IAM dünyasında sabit bir kalıpla ifade edilir:

```text
servis.kaynak.fiil
service.resource.verb
```

Örnek: `pubsub.subscriptions.consume` — Pub/Sub servisinde, subscription kaynağı üzerinde, "tüket (consume)" işlemini yapma izni.

Burada çok önemli bir kural var: **Bir izni bir kullanıcıya doğrudan atayamazsın.** Bunun yerine kullanıcıya bir **rol (role)** verirsin. Rol, izinlerin bir koleksiyonudur; bir principal'a rol verdiğinde, o rolün içerdiği **tüm** izinleri birden vermiş olursun.

Somut örnek: "staff" adlı Google grubundaki tüm kullanıcılara, `project_a` üzerinde **InstanceAdmin** rolü verilir. Bu tek işlemle, gruptaki her kullanıcı o rolün içerdiği tüm izinleri (örnek oluşturma, silme, başlatma, durdurma, değiştirme gibi) kazanır.

> **Neden izin değil de rol?** Çünkü gerçek dünyada bir işi yapmak neredeyse hiçbir zaman tek bir izin gerektirmez. "Sanal makineleri yönet" demek; oluşturabilme, silebilme, başlatabilme, durdurabilme ve değiştirebilme iznini **birlikte** gerektirir. Bu izinleri tek tek dağıtmak hem yorucu hem hataya açık olurdu. Rol, bunları anlamlı bir pakette gruplar.

### Üç rol türü

**1. Temel roller (basic roles).** Çok geniş kapsamlı, yüksek düzeyde izin veren rollerdir. Örneğin **Viewer** rolü, bir projedeki **tüm** kaynaklara salt-okunur erişim verir. Bu genişlik yüzünden temel roller genellikle **üretim ortamları için önerilmez** — çok fazla izin verirler, en az ayrıcalık ilkesini ihlal ederler.

**2. Önceden tanımlı roller (predefined roles).** Belirli Google Cloud kaynaklarına **granular (ince taneli)** erişim sağlar. Google tarafından oluşturulur ve bakımı yapılır. Örnek: **`run.invoker`** rolü, kullanıcının bir Cloud Run servisini çağırmasına izin verir. Predefined roller, temel rollerden çok daha isabetlidir çünkü belirli bir iş için tasarlanmıştır.

**3. Özel roller (custom roles).** Kullanıcı tarafından tanımlanır ve tipik olarak belirli bir ihtiyaca göre bakımı yapılır. Mevcut predefined roller senin kullanım senaryon için **fazla izin verici** olduğunda custom rol oluşturmalısın. İzinler üzerinde tam kontrolün olduğu için, custom roller **en az ayrıcalık ilkesini uygulamanın** en kesin yoludur.

**Aynı kullanıcıya birden fazla rol verilebilir** — roller birbirini dışlamaz, birikimlidir.

| Rol türü | Kapsam | Kim yönetir | Ne zaman kullanılır |
| --- | --- | --- | --- |
| Basic | Çok geniş (tüm proje) | Google (sabit) | Küçük ekip, hızlı başlangıç; üretimde genelde önerilmez |
| Predefined | Servise/işe özel, granular | Google oluşturur/bakımını yapar | Çoğu gerçek senaryo — ilk tercih |
| Custom | Tam senin tanımladığın izin seti | Sen yönetirsin | Predefined roller çok geniş kaldığında; en az ayrıcalık şart olduğunda |

> **Önceki modülle bağ:** Bu üçlü ayrım (basic/predefined/custom) ve "kim-ne-nerede" üçlüsü, ilk modülde IAM'i tanıttığımızda da işlenmişti. Burada aynı temel tekrar ediliyor çünkü authentication'ı anlamadan önce authorization'ı sağlam kavramış olman gerekiyor — modülün kendisi de "First, we review IAM authorization" diyerek bunu bir **hatırlatma (review)** olarak sunuyor. Yeni olan kısım, principal türlerinin kimlik oluşturma yeteneği ayrımı (grup/Workspace/Cloud Identity'nin kimlik oluşturamaması) ve bundan sonraki authentication derinliğidir.

---

# PARÇA 2 — Authentication (Kimlik Doğrulama)

## Authorization'dan Authentication'a geçiş

Şimdiye kadar gördüğümüz her şey **authorization**'dı — yani IAM sana **ne yapmaya izinli olduğunu** söylüyordu. Ama bir adım geriye gidelim: IAM'in "kim" dediği o principal, **kendisinin gerçekten o principal olduğunu nasıl kanıtlıyor?** İşte bu soru **authentication**'ın alanıdır.

> **Authentication vs Authorization — temel tanım:** **Authentication**, senin **kim olduğunu kanıtlamandır** — bir kimlik bilgisi (credential) sunarak. **Authorization** ise, kimliğin doğrulandıktan **sonra**, **neye izinli olduğunu** belirler. Sırayla düşün: önce kim olduğunu kanıtlarsın (authn), sonra sistem sana ne yapabileceğini söyler (authz). Biri olmadan diğeri anlamsızdır — kimliğini kanıtlamadan izin kontrolü yapılamaz, izin tanımlanmadan da kimlik doğrulamanın bir anlamı kalmaz.

Bir Google Cloud geliştiricisi olarak, farklı senaryolarda farklı authentication türlerine ihtiyaç duyarsın:

- Uygulaman **Google'a kimlik doğrulayıp** Google servislerine ve kaynaklarına erişmeli.
- Google Cloud üzerinde barındırılan uygulamaları (örneğin Cloud Run servislerini) **çağırman** gerekebilir — genellikle sadece **doğrulanmış çağıranların** erişmesini istersin.
- Uygulamanın **son kullanıcılarını** doğrulaman gerekebilir.

## API çağrılarını yetkilendirmenin üç yolu

Bir Google Cloud servisini çağırdığında, tipik olarak bir **API çağrısı** yapıyorsundur. Bu çağrıyı yetkilendirmenin üç yolu vardır:

**1. API key.** Uygulamayı tanımlayan bir karakter dizisidir. Bir API key kullanmak, isteği fatura ve kota amaçlı bir Google Cloud projesiyle ilişkilendirir. Ancak **ele geçirilmiş bir API key, API'ye tam ve uzun süreli erişim sağlar** — süresi dolmaz, kısıtlaması azdır. Bu yüzden API key'ler tipik olarak sadece **düşük güvenlikli, salt-okunur API'ler** için uygundur. Nitekim **çoğu Google API'si API key'leri kabul bile etmez.**

**2. User account (kullanıcı hesabı — OAuth).** Bir kişiyi temsil eder ve o kişinin e-posta adresiyle tanımlanır. Giriş işlemi (login), e-posta adresini ve ayrı bir kimlik bilgisini (genellikle bir parola) kullanarak bir **OAuth token** üretir. Bu token, kullanıcının izinlerine göre **sınırlı erişim** sağlar ve **belirli bir süre sonra sona erer (expire).** OAuth token'lar, API key'lerden **daha güvenlidir.**

**3. Service account (servis hesabı).** Bir workload'ı veya uygulamayı temsil eder ve benzersiz e-posta adresiyle tanımlanır. Bir servis hesabı için üretilen OAuth token, o servis hesabına **eklenmiş rollere göre** API'ye erişim sağlar.

| Yöntem | Neyi temsil eder | Güvenlik düzeyi | Süre | Ne zaman kullanılır |
| --- | --- | --- | --- | --- |
| API key | Uygulama (kimliksiz) | Düşük — ele geçince tam/kalıcı erişim | Süresiz | Sadece düşük-güvenlikli, salt-okunur API'ler |
| User account (OAuth) | Gerçek kişi | Orta-yüksek — kullanıcı izniyle sınırlı | Süreli (expire olur) | Son kullanıcı adına işlem yapmak |
| Service account | Uygulama/workload | Role bağlı, granular | Rol ve token politikasına bağlı | Uygulamadan-uygulamaya, sunucu tarafı erişim |

> **Sınav tuzağı — üç yöntemi karıştırma:** API key "ben bu projeyim" der ama "ben buyum" demez — kimlik doğrulamaz, sadece fatura/kota ilişkilendirir. User account bir **insanı**, service account bir **uygulamayı/workload'ı** temsil eder. Soruda "kullanıcı adına" geçiyorsa user account/OAuth; "arka planda çalışan bir servis" geçiyorsa service account; "düşük güvenlikli, herkese açık, salt okunur" geçiyorsa (nadiren) API key.

## Servis hesabı kimlik doğrulaması derinlemesine

Servis hesabı, bir uygulamanın ya da compute workload'ının **kimliği** olarak davranır. Uygulaman, bir Google API'sini veya servisini çağırırken servis hesabını kullanır — böylece **kullanıcılar doğrulama sürecine doğrudan dahil olmazlar.** Her servis hesabı, kendine özgü benzersiz bir e-posta adresiyle tanımlanır.

Servis hesapları **authorization'ı mümkün kılar** çünkü bir servis hesabına belirli IAM rolleri atayabilirsin — uygulamanın ihtiyaç duyduğu erişim düzeyini böyle sağlarsın.

Peki servis hesapları **nasıl** doğrulanır? Kullanıcı hesaplarından tamamen farklı bir mekanizmayla: **RSA açık-özel anahtar çifti (public-private key pair)** ile.

- Servis hesabına bağlı bir **parola yoktur** — bu yüzden bir servis hesabıyla tarayıcı üzerinden giriş yapamazsın.
- Servis hesabının **özel anahtarı (private key)**, bir **servis hesabı JSON dosyası** olarak indirilebilir.
- Servis hesaplarının erken döneminde, bir uygulama olarak kimlik doğrulaman gerektiğinde bu indirilen anahtarlar rutin biçimde kullanılırdı.
- Eğer bir servis hesabının özel anahtarını biliyorsan, bu anahtarla bir **erişim token'ı (access token)** talep edebilirsin. Ortaya çıkan token, o servis hesabı **adına** Google Cloud API'leriyle etkileşmeni sağlar.

> **Kritik benzetme:** Bir servis hesabının özel anahtarına sahip olmak, **bir kullanıcının parolasını bilmekle aynı şeydir.** O anahtar elindeyse, o hesap sensin. Bu yüzden bu anahtarı yönetmek son derece hassas bir sorumluluktur.

## Servis hesabı anahtarlarının üç riski

Servis hesabı anahtarları dikkatli yönetilmezse ciddi bir güvenlik riski haline gelir. Üç somut risk şöyle:

**1. Credential leakage (kimlik bilgisi sızıntısı).** Bir geliştirici, özel anahtarı yanlışlıkla **genel (public) bir kod deposuna commit'lerse** ne olur düşün: Kötü niyetli biri bu anahtarı bulup ortamındaki kaynaklara erişebilir. Bu, en sık karşılaşılan ve en önlenebilir risktir.

**2. Privilege escalation (ayrıcalık yükseltme).** Kötü bir aktör bir servis hesabı anahtarına erişirse, bu anahtarı **kendi ayrıcalığını yükseltmek** için kullanabilir. Örneğin, veritabanı üzerinde izni olan bir servis hesabını kullanarak **kendine** veritabanı erişimi verebilir. En tehlikeli kısmı şu: **Tehlikeyi fark edip servis hesabı anahtarını değiştirsen bile, yükseltilmiş ayrıcalıklar kalıcı olarak yerinde kalır** — çünkü saldırgan zaten kendine kalıcı bir izin vermiştir, sadece anahtarı iptal etmek bunu geri almaz.

**3. Identity masking (kimlik gizleme).** Bir servis hesabı olarak kimlik doğrulayarak, kötü aktör **kendi gerçek kimliğini ve eylemlerini gizleyebilir.** Loglarda "siz" değil, servis hesabı görünür — bu da adli analizi (forensics) zorlaştırır.

Bu risklerin en iyi azaltma yolu nedir? **İndirilmiş servis hesabı anahtarlarından kaçınmak** ve mümkün olduğunda servis hesaplarını doğrulamak için **başka yöntemler** kullanmaktır. Bir sonraki parçada tam olarak bu alternatif yöntemleri göreceğiz.

---

# PARÇA 3 — Hangi Auth Yöntemini Ne Zaman Kullanmalıyım? Karar Ağacı ve ADC

## Karar ağacı

Google Cloud API'lerine bir uygulamadan kimlik doğrulamanın birden fazla yolu vardır. Doğru yöntemi seçmek için kendine sırayla şu soruları sor:

```text
Uygulaman Google Cloud üzerinde mi çalışıyor?
│
├── EVET, ve GKE'de DEĞİL
│   ├── Yerel geliştirme ortamı mı?
│   │     → `gcloud auth application-default login`
│   │       (uygulama SENİN kullanıcı kimlik bilgilerinle çalışır)
│   │
│   └── Yerel olmayan (üretim) ortam mı?
│         → Compute/serverless örneğine DOĞRUDAN bir
│           servis hesabı ekle (attached service account)
│
├── EVET, ve GKE'de ÇALIŞIYOR
│   → Workload Identity kullan
│     (Kubernetes servis hesabı, IAM servis hesabını impersonate eder)
│
└── HAYIR, Google Cloud dışında çalışıyor
    │
    ├── Federation mümkün mü? (bulut sağlayıcı/on-prem
    │    bir ID token üretebiliyor mu?)
    │      → EVET: Workload Identity Federation
    │        (harici token → Google Cloud access token,
    │         servis hesabı anahtarı GEREKMEZ)
    │
    └── Federation mümkün değilse
          → SON ÇARE: Servis hesabı anahtarı
            (büyük özenle güvenceye al)
```

Bu ağacı ezberlemek yerine mantığını kavra: **Google Cloud, kimlik doğrulamayı senin yerine yapmayı her zaman tercih eder.** Uygulaman ne kadar "Google Cloud'un içinde" ise, o kadar az manuel kimlik bilgisi yönetimine ihtiyacın olur. Uygulaman Google Cloud'dan ne kadar uzaklaşırsa (dışarıda, on-premises, başka bulutta), federation gibi mekanizmalarla köprü kurman gerekir; hiçbir köprü mümkün değilse en son çare olarak anahtar dosyasına düşersin.

## ADC — Application Default Credentials

Peki senin kodun, hangi kimlik bilgisini kullanacağını **nasıl bilir**? Cevap: **Application Default Credentials (ADC)**. Cloud Client Library'ler, uygulamanın kimlik bilgilerini bulmak ve Google Cloud API'lerine erişmene izin vermek için ADC'yi kullanır.

ADC'nin en büyük değeri şudur: **Kodunu, yerel geliştirme ortamından Cloud Run veya GKE gibi bir Google Cloud servisine taşırken değiştirmen gerekmez.** Google Cloud dışında çalışan uygulamalar da **aynı kodu** kullanabilir. Kod hep aynıdır — arka planda hangi kimlik bilgisinin kullanılacağına ADC karar verir.

ADC, kimlik bilgisi konumlarını şu **sırayla** kontrol eder:

1. **`GOOGLE_APPLICATION_CREDENTIALS` ortam değişkeni** var mı diye önce buna bakılır. Varsa, ADC bu değişkenin gösterdiği yoldaki **servis hesabı dosyasını** kullanır.
2. Ortam değişkeni ayarlı değilse, ADC bir sonraki adımda **gcloud CLI ile ayarlanmış kullanıcı kimlik bilgileri** için bilinen (well-known) bir konuma bakar.
3. Son olarak, ADC **eklenmiş (attached) bir servis hesabı** kullanır. Bazı servisler, belirli bir kaynağa servis hesabı eklemene izin verir — örneğin Cloud Run, belirli bir servise veya fonksiyona servis hesabı eklenmesine izin verir; ADC o eklenmiş servis hesabını kullanır. Belirli kaynağa eklenmiş bir servis hesabı yoksa, ADC kullanılan servisin **varsayılan servis hesabını** kullanır (Compute Engine, GKE veya Cloud Run gibi).

Bu üç adımın hiçbirinde kimlik bilgisi bulunamazsa, **hata fırlatılır.**

> **Somut örnek:** Python'da yazılmış bir fonksiyon, hiçbir kimlik bilgisi belirtmeden doğrudan bir authenticated API isteği yapar. Kod kimlik bilgisi göndermez; ADC devreye girer ve yukarıdaki sırayla kimlik bilgisini arar, bulur ve kullanır.

## `gcloud auth login` vs `gcloud auth application-default login`

Bu iki komut isim olarak çok benzer ama **tamamen farklı amaçlara** hizmet eder — sınavın klasik tuzaklarından biridir.

- **`gcloud auth login`** — senin kimlik bilgilerini, **gcloud CLI komutlarını çalıştırırken** kullanmak içindir. Terminalde `gcloud compute instances list` gibi bir komut çalıştırdığında kullanılan kimlik budur.
- **`gcloud auth application-default login`** — senin kimlik bilgilerini, **kodundan çağrı yaparken** kullanmak içindir. Bu komut, kullanıcı erişim kimlik bilgilerini içeren bir JSON dosyası üretir ve bunu ADC'nin bilinen konumuna yerleştirir. Böylece uygulaman, **senin kullanıcı kimliğinle** API çağrısı yapabilir. Bu JSON dosyası güvenlidir, parola içermez ve **zaman sınırlıdır (time-bound)**.

> **Sınav tuzağı:** "gcloud komutlarını çalıştırmak" → `gcloud auth login`. "Kodumdan API çağırmak, ADC'yi beslemek" → `gcloud auth application-default login`. İkisini birbirinin yerine kullanmak sınavda klasik bir tuzaktır.

## Attached service accounts — üretim için tercih edilen yöntem

Google Cloud serverless ürünlerinde (Cloud Run gibi) veya Compute Engine üzerinde çalışan uygulamalardan API çağırmanın **tercih edilen yolu**, servis hesabını doğrudan kaynağa **eklemektir (attach)**.

- Bir Compute Engine VM'inde çalışırken, VM'nin **varsayılan servis hesabını**, uygulaman için özel olarak oluşturulmuş bir servis hesabıyla değiştir. Bu servis hesabına yalnızca uygulamanın **gerçekten ihtiyaç duyduğu** ayrıcalıkları ver.
- Benzer şekilde, Cloud Run'a bir servis dağıttığında veya Cloud Run functions'da bir fonksiyon oluşturduğunda, o servise veya fonksiyona bir servis hesabı ekle.

**User-managed (kullanıcı tanımlı) bir servis hesabı eklemek, Google Cloud üzerinde çalışan üretim kodu için ADC'ye kimlik bilgisi sağlamanın tercih edilen yoludur.** Bu yöntemde hiçbir anahtar dosyası indirmene, taşımana ya da güvenceye almana gerek kalmaz — Google, kimlik bilgisi yaşam döngüsünü senin adına yönetir.

## Workload Identity (GKE için)

Uygulaman GKE'de çalışıyorsa, Google Cloud API'lerine güvenli ve yönetilebilir biçimde erişmenin önerilen yolu **Workload Identity**'dir.

Workload Identity, GKE cluster'ındaki bir **Kubernetes servis hesabının**, uygulaman Google Cloud API'lerini çağırırken bir **IAM servis hesabı gibi davranmasını** sağlar. Bunun getirisi büyük: Cluster'ındaki **her uygulamaya ayrı, ince taneli (fine-grained) bir kimlik ve yetkilendirme** atayabilirsin — tüm cluster tek bir geniş yetkili kimlik paylaşmak zorunda kalmaz.

Workload Identity, Google Cloud API'lerini çağırırken **Kubernetes servis hesabı token'larını otomatik olarak IAM token'larıyla değiştirir (exchange).** Kurulumu iki adımdan oluşur:

1. Cluster'ında Workload Identity'yi **etkinleştir.**
2. Kubernetes servis hesabının, oluşturduğun IAM servis hesabını **impersonate etmesine (taklit etmesine) izin ver.**

Gerisini ADC halleder — kodun hiçbir şekilde değişmez.

## Workload Identity Federation (Google Cloud dışı için)

Bazen uygulamalarını Google Cloud dışında — başka bir bulutta ya da kendi veri merkezinde (on-premises) — çalıştırman gerekir. İşte burada **Workload Identity Federation** devreye girer: **Servis hesabı anahtarı gerekmeden** bu harici workload'lara Google Cloud API erişimi verir.

Geleneksel olarak, Google Cloud dışında çalışan uygulamalar erişim için **servis hesabı anahtarları** kullanırdı — ve gördüğümüz gibi, servis hesabı anahtarları doğru yönetilmezse ciddi bir güvenlik riskidir. Workload Identity Federation, **OpenID Connect (OIDC)** destekleyen bir kimlik sağlayıcısı kullanan harici uygulamalar için bu anahtar ihtiyacını **tamamen ortadan kaldırır.**

Mekanizma şöyle işler: Bir **OpenID Connect ID token'ı**, kısa ömürlü bir **Google Cloud erişim token'ıyla** değiştirilir (exchange edilir); bu token bir IAM servis hesabını impersonate etmene izin verir. Sonuç: Harici uygulama, bir servis hesabı anahtarını güvenceye almak ya da düzenli olarak döndürmek (rotate etmek) **zorunda kalmadan** Google Cloud servislerini çağırabilir.

> **Sınav tuzağı — Workload Identity vs Workload Identity Federation:** İsimleri neredeyse aynı olduğu için karıştırılırlar ama farklı senaryolar içindir. **Workload Identity**, **GKE cluster'ının içindeki** workload'lar içindir (Kubernetes SA → IAM SA). **Workload Identity Federation**, **Google Cloud'un dışındaki** workload'lar içindir — başka bir bulut sağlayıcısı ya da on-premises, ve OIDC destekleyen bir kimlik sağlayıcısı gerektirir. "GKE'de çalışıyorum" → Workload Identity. "AWS/Azure'da ya da kendi veri merkezimde çalışıyorum, federation kurmak istiyorum" → Workload Identity Federation.

## Servis hesabı anahtarı — son çare, ama gerekirse en iyi uygulamalar

Federation mümkün değilse, harici uygulamalara Google Cloud API erişimi vermek için servis hesabı anahtarı kullanman gerekebilir. Ama unutma: **Servis hesabı anahtarı kullanmak gerçekten son çaredir.** Kullanmak zorunda kalırsan, şu en iyi güvenlik uygulamalarına mutlaka uy:

1. **Google'ın oluşturduğu özel anahtarı indirmek yerine, kendi açık anahtarını (public key) yükle.** Kendi altyapını kullanarak bir açık-özel anahtar çifti üret, açık anahtarı Google'a yükle, özel anahtarı ise çalışma ortamına (runtime) **güvenle kendin teslim et.** Bu yaklaşım, özel anahtarı işlerken hata yapma olasılığını azaltır — çünkü Google'a hiçbir zaman özel anahtarını göndermemiş olursun.
2. **Anahtarları asla program kaynak koduna ya da binary'lere gömme.** Anahtarlar binary'lerden çıkarılabilir (extract edilebilir); kaynak kod ise depolarda (repository) barınıyor olabilir — bu da anahtarların ele geçirilme (compromise) olasılığını artırır.
3. **Servis hesaplarının en az ayrıcalık ilkesine uyduğundan emin ol.** İlişkili uygulama için gereken **minimum izin setini** ver. Servis hesabı anahtarı bir şekilde ele geçirilse bile, kötü aktörün erişimini bu şekilde sınırlamış olursun.

---

# PARÇA 4 — Diğer Authentication ve Authorization Yöntemleri

## Kullanıcı adına kaynak erişimi: OAuth 2.0

Bazen uygulaman, **kullanıcı adına** kaynaklara erişmesi gerekebilir. Örnek senaryolar:

- Uygulaman, uygulama kullanıcılarına ait **BigQuery veri kümelerine (dataset)** erişmesi gerekiyor.
- Uygulaman, kullanıcı adına **proje veya kaynak oluşturmak** için kullanıcı olarak kimlik doğrulaması gerekiyor.

Bu tür senaryolarda **OAuth 2.0 protokolünü** kullanarak kullanıcı adına kaynaklara erişebilirsin. Akış şöyle işler: Uygulaman kaynaklara erişim talep ettiğinde, kullanıcıdan **onay (consent)** istenir. Kullanıcı onay verirse, uygulama bir **yetkilendirme sunucusundan (authorization server)** kimlik bilgisi talep edebilir. Uygulama bu kimlik bilgisini, kullanıcı adına kaynaklara erişmek için kullanır.

> **Sezgi:** Bunu "Şu uygulama, Google hesabınızdaki takvim bilgilerine erişmek istiyor. İzin veriyor musunuz?" diyen tanıdık ekranlardan hatırlarsın. Kullanıcı "izin ver" dediği an, uygulama o kullanıcının izinleri **çerçevesinde** (daha fazlası değil) kaynaklara erişebilir.

## Identity-Aware Proxy (IAP)

Kullanıcılara erişim sağlamanın bir başka yolu **Identity-Aware Proxy (IAP)**'dir. IAP, Google Cloud projendeki bulut uygulamalarına erişimi kontrol eder.

IAP, bir kullanıcının kimliğini **doğrular** ve yapılandırmana göre o kullanıcının uygulamaya erişip erişemeyeceğine **karar verir.** Bunun en büyük avantajı şu: **Geliştiricinin erişim kontrolü için hiç kod yazmasına gerek kalmaz.**

IAP, **HTTPS ile erişilen uygulamalar için merkezi bir yetkilendirme katmanı** kurmanı sağlar. Bu sayede şunlardan kaçınabilirsin:

- VPN'lere güvenmek,
- Ağ seviyesinde (network-level) güvenlik duvarı kurallarına güvenmek,
- Uygulamalarının içine karmaşık authorization kodu yazmak.

Bunun yerine IAP, sana **uygulama seviyesinde bir erişim kontrol modeli (application-level access control)** benimseme imkânı verir — erişim kararı, ağın neresinden geldiğine değil, **kimin kim olduğuna ve neye izinli olduğuna** göre verilir.

> **Sezgi:** VPN, "bu binaya girebilirsin" der; IAP, "bu binaya girebilirsin **ve** şu odaya girmene izin var, şu odaya yok" der — kişiye özel, uygulama seviyesinde, koda hiç dokunmadan.

## Firebase Authentication

**Firebase**, mobil uygulama geliştirmeye yardımcı olan bir platformdur. **Firebase Authentication**, mobil uygulamalarına kimlik doğrulama ve kimlik yönetimi eklemeni sağlayan özellikler sunar.

Firebase Auth şu yöntemlerle kimlik doğrulamayı destekler:

- **Parolalar (passwords)**
- **Telefon numaraları**
- Popüler **federe kimlik sağlayıcıları (federated identity providers)** — Google, Apple, GitHub gibi.

Firebase Auth, kayıt olma (sign-up) ve giriş yapma (sign-in) **UI akışlarını** ve **hesap kurtarma gibi uç durumları (edge cases)** yöneten **hazır (drop-in) auth bileşenleri** sunar. SDK'lar ve hazır UI kütüphaneleri, geliştirmeyi kolaylaştırır — sıfırdan bir giriş ekranı tasarlayıp test etmen gerekmez.

Başarılı bir girişten sonra, kullanıcının profiline erişebilir ve sağlanan token'ı **backend servisleri için OAuth 2.0 ve OpenID Connect akışlarında** kullanabilirsin.

## Identity Platform vs Firebase Authentication

Bu ikisi çok benzer işlevler sunar ve bu yüzden sık karıştırılır. İkisi de sana kullanıcıları uygulamalarına kolayca giriş yaptırmayı sağlar; **backend servisleri, SDK'lar ve UI kütüphaneleri** sunarak.

Ancak **Identity Platform**, **kurumsal (enterprise) müşteriler** için tasarlanmış ek yetenekler sunar:

- **OpenID Connect ve SAML** ile giriş desteği.
- **Çok faktörlü kimlik doğrulama (MFA — multi-factor authentication).**
- **Identity-Aware Proxy (IAP) ile entegrasyon.**

| Özellik | Firebase Authentication | Identity Platform |
| --- | --- | --- |
| Backend servis, SDK, UI kütüphaneleri | Var | Var |
| Parola, telefon, federe sağlayıcılar (Google/Apple/GitHub) | Var | Var |
| OpenID Connect ve SAML ile giriş | — | Var |
| Çok faktörlü kimlik doğrulama (MFA) | — | Var |
| IAP entegrasyonu | — | Var |
| Hedef kitle | Mobil/web uygulama geliştiricileri | Kurumsal müşteriler |

> **Sınav tuzağı — Firebase Authentication vs Identity Platform:** "MFA istiyorum", "SAML ile giriş istiyorum", "IAP ile entegre olsun" gibi ifadeler görürsen cevap **Identity Platform**'dur. Sade bir mobil/web uygulaması için hızlı, hazır bir giriş deneyimi istiyorsan **Firebase Authentication** yeterlidir. İkisi aynı temel üzerine kurulu olduğu için — Identity Platform'u "Firebase Authentication + kurumsal özellikler" olarak düşünebilirsin.

> **Önceki modülle bağ:** İkinci modülde "kullanıcı yönetimi eforunu kimlik yönetimini devrederek en aza indirebilirsin" demiş ve Identity Platform ile Firebase Authentication'ı **federe kimlik yönetimi** başlığı altında kısaca tanıtmıştık. O zaman "kendi tescilli çözümünü uygulamak, güvenceye almak ve ölçeklemek zorunda kalmazsın" demiştik — işte bu modülde bu vaadin **nasıl** gerçekleştiğini gördün: parola/telefon/federe sağlayıcı desteği, hazır UI bileşenleri, ve kurumsal ihtiyaçlar için Identity Platform'un ek katmanı.

---

# PARÇA 5 — Secret Manager

## Neden var?

Uygulamalar, diğer servislerle ve uygulamalarla kimlik doğrulamak için genellikle **kimlik bilgilerine** ihtiyaç duyar: API key'ler, parolalar, sertifikalar. Bu kimlik bilgilerinin **güvenli biçimde** saklanması gerekir.

Geliştiriciler sıklıkla bu kimlik bilgilerini **düz dosyalarda (flat files)** saklamayı düşünür. Bunun getirisi şudur: **Erişim inanılmaz derecede kolaylaşır, ama güvenliği sağlamak zorlaşır.** Herkesin bu bilgiyi görmesi istenmediği için, dosya sistemi izinleriyle (file operating system permissions) erişimi kısıtlaman gerekir. Daha da kötüsü, düz dosya yaklaşımı sırların **kuruluşunun bulut ve on-premises altyapısına dağılmasına** yol açabilir — her uygulamanın kendi dosyasında kendi kopyası, senkronize edilmesi ve güvenceye alınması gereken ayrı ayrı parçalar haline gelir.

**Secret Manager**, hassas bilgiyi saklamanın **güvenli ve rahat** bir yolunu sunar.

## Ne yapar, nasıl çalışır?

Secret Manager ile sırları **ikili blob (binary blob) veya metin dizesi (text string)** olarak saklayabilir, yönetebilir ve erişebilirsin. Yalnızca **uygun izinlere sahip kullanıcılar ve uygulamalar** bir sırra erişebilir. Tüm sırlarını **tek bir yerde** tutmak ve **kimin erişebileceğini IAM ile belirlemek**, sırlarının güvenli yönetimini büyük ölçüde sadeleştirir.

### Özellikler

- **Ad global, veri opsiyonel olarak bölgesel.** Secret Manager'da saklanan bir sırrın **adı küreseldir (global)**, ama sırrın verisi **isteğe bağlı olarak belirli bir bölgede (region)** saklanabilir. Bu, hem küresel erişilebilirlik hem de veri konumu (data residency) gereksinimlerini karşılamanı sağlar.

- **Versiyonlama.** Sırlar **versiyonlanabilir.** Her versiyon **farklı sır verisi** taşıyabilir ve saklanabilecek versiyon sayısında **limit yoktur.** Kritik nokta: **Versiyonlar değiştirilemez (immutable)**, ama **silinebilir.** Yani bir versiyonu güncellemek istediğinde eskisini "düzenlemezsin" — yeni bir versiyon oluşturursun; eski versiyon olduğu gibi kalır ya da silinir.

- **En az ayrıcalık ilkesi.** Sırlar **proje seviyesinde** oluşturulur ve başlangıçta yalnızca **proje sahipleri (project owners)**, projedeki sırları oluşturma ve erişme iznine sahiptir. Diğer roller, sırlara erişmek için **açıkça IAM izni** verilmesini gerektirir. Bu, tıpkı Parça 1'de gördüğümüz en az ayrıcalık ilkesinin somut bir uygulamasıdır: varsayılan olarak kimse erişemez, sadece açıkça yetkilendirilenler erişir.

- **Denetim günlükleri (audit logs).** **Cloud Audit Logs** etkinleştirildiğinde, Secret Manager ile yapılan **her etkileşim** — okumalar ve güncellemeler dahil — bir **denetim girdisi (audit entry)** oluşturur. Bu denetim, yalnızca **uygun kullanıcı ve uygulamaların** sırlarına eriştiğini doğrulamana olanak tanır.

- **Şifreleme.** Secret Manager, senin adına **sunucu tarafı şifreleme anahtarlarını**, Google'ın kendi şifrelenmiş verisi için kullandığı **aynı hardened (sertleştirilmiş) anahtar yönetim sistemleriyle** yönetir. Ayrıca Secret Manager, **Cloud Key Management Service (Cloud KMS)** ile entegre olur: bir sırrın versiyonunu saklamadan önce **Cloud KMS ile şifreletebilirsin**; versiyonu geri aldığında ise **çözmek için yine Cloud KMS gerekir.**

### Secret Manager nasıl kullanılır

**Sır oluşturma.** Google Cloud konsolu, `gcloud` CLI veya kod ile bir sır oluşturabilirsin. Örneğin bir `gcloud` komutuyla `my-secret` adında bir sır oluşturursun; `--data-file` parametresi, sırrı **standart girdiden (stdin)**, boru hattıyla (piped) bir `echo` komutundan alır. Burada önemli bir seçim yapman gerekir: **replication policy (çoğaltma politikası).**

- **`automatic`** — sır payload'ının **herhangi bir bölgede** saklanmasına izin verir.
- **`user-managed`** — sırrın saklanabileceği **bölgeleri sen belirlersin.**

**Sır alma (retrieve).** Aynı şekilde konsol, `gcloud` CLI veya kod ile alabilirsin. Kod tarafında (örneğin Python), önce `secretmanager` kütüphanesini içe aktarır ve bir istemci (client) oluşturursun. Sırlara **resource name (kaynak adı)** ile erişilir; bu ad **proje kimliğini (project ID)**, **sırrın kimliğini/adını** ve **erişmek istediğin versiyonu** içerir. Versiyonlar **sıra sayılarıdır** (1, 2, 3, ...); en güncel versiyona erişmek için **`latest`** anahtar kelimesini kullanabilirsin. Sırrı almak, adı fonksiyona geçirmek ve dönen **payload'ı decode etmek** kadar basittir.

Bu süreç, hassas veriyi uygulamaların içinde kullanmanın **güvenli bir yöntemini** sağlar.

> **Önceki modülle bağ:** İkinci modülde Cloud Code'un IDE entegrasyonlarından bahsederken "Secret Manager entegrasyonu — hassas verini IDE içinden yönetirsin" demiştik ve config'i koddan ayırıp ortam değişkenlerinde tutma ilkesini işlemiştik. Şimdi görüyorsun ki bu ilkenin **hassas veri** (parola, API key, sertifika) versiyonu Secret Manager'dır: config genel ayarlar için ortam değişkenine gider, ama **sır** niteliğindeki veri için Secret Manager'a gider — çünkü ortam değişkenleri versiyonlama, denetim günlüğü ya da KMS şifrelemesi sunmaz.

---

# Hangi Yöntemi Ne Zaman Seçerim? (Karar Bölümü)

Modülün tüm parçalarını, bir geliştiricinin karşılaşacağı somut senaryolar üzerinden toparlayalım:

| Senaryo | Doğru yöntem |
| --- | --- |
| Uygulamam Google Cloud'da (GKE değil) çalışıyor, üretimde | Attached service account |
| Yerel makinemde geliştirme yapıyorum, kodumu test ediyorum | `gcloud auth application-default login` |
| Uygulamam GKE'de çalışıyor | Workload Identity |
| Uygulamam başka bir bulutta / on-premises, OIDC destekleniyor | Workload Identity Federation |
| Uygulamam Google Cloud dışında, federation imkânsız | Servis hesabı anahtarı (son çare, en iyi uygulamalarla) |
| Uygulamam kullanıcı adına BigQuery/başka kaynağa erişecek | OAuth 2.0 (kullanıcı onayı ile) |
| HTTPS ile erişilen uygulamama merkezi erişim kontrolü lazım, kod yazmak istemiyorum | Identity-Aware Proxy (IAP) |
| Mobil/web uygulamama hızlı, hazır bir giriş ekranı lazım | Firebase Authentication |
| Kurumsal müşterilerim var, SAML/OIDC/MFA/IAP entegrasyonu gerekiyor | Identity Platform |
| Uygulamam API key, parola, sertifika gibi sırlar saklayacak | Secret Manager |
| gcloud komutlarını terminalden çalıştırıyorum | `gcloud auth login` |

---

# Toplu Özet (Hızlı Tekrar)

**Temel çerçeve:** Authorization (IAM) = neye izinlisin; Authentication = kim olduğunu kanıtlama. Önce kimliğini kanıtlarsın, sonra sistem sana ne yapabileceğini söyler.

**IAM üçlüsü:** principal (kim) — role (ne erişim) — resource (hangi kaynak). Permission doğrudan atanmaz, role üzerinden verilir; format `service.resource.verb`. En az ayrıcalık ilkesi tüm modülün omurgasıdır.

**Principal türleri:** Kimlik oluşturabilenler — Google Account (insan), service account (uygulama/workload). Kimlik OLUŞTURAMAYANLAR, sadece izin yönetimini kolaylaştıranlar — Google group (login credential yok), Google Workspace account (Workspace uygulama erişimi olanlar), Cloud Identity domain (Workspace uygulama erişimi OLMAYANLAR).

**Üç rol türü:** Basic (geniş, üretimde önerilmez), Predefined (Google yönetir, servise özel, granular), Custom (sen yönetirsin, en az ayrıcalık için).

**Üç API çağrı yetkilendirme yolu:** API key (zayıf, çoğu API kabul etmez), User account/OAuth (kişi, süreli token), Service account (uygulama, role bağlı token).

**Servis hesabı:** E-posta ile tanımlı, parola yok, RSA anahtar çifti ile doğrulanır; JSON dosyası olarak indirilebilir private key. Private key = parola gibi hassas. Üç risk: credential leakage, privilege escalation (anahtar değişse bile ayrıcalık kalır), identity masking.

**Karar ağacı:** GC'de + non-GKE → local dev'de `gcloud auth application-default login`, üretimde attached SA. GKE'de → Workload Identity. GC dışı + federation mümkün → Workload Identity Federation (OIDC). Federation imkânsız → SA key (son çare).

**ADC arama sırası:** `GOOGLE_APPLICATION_CREDENTIALS` env var → gcloud kullanıcı kimlik bilgileri (well-known location) → attached service account (yoksa servisin varsayılan SA'sı) → hata.

**`gcloud auth login`** (CLI komutları için) **vs `gcloud auth application-default login`** (kod/ADC için).

**Workload Identity** (GKE içi, Kubernetes SA → IAM SA impersonation) **vs Workload Identity Federation** (GC dışı, OIDC token → GC access token, SA key gerekmez).

**SA key son çare en iyi uygulamaları:** İndirilen private key yerine kendi public key'ini yükle; anahtarı asla kod/binary'ye gömme; en az ayrıcalık uygula.

**Diğer yöntemler:** OAuth 2.0 (kullanıcı adına erişim, consent akışı); IAP (kod yazmadan, HTTPS uygulamalar için merkezi/uygulama-seviyesi erişim kontrolü, VPN/firewall/karmaşık authz koduna alternatif); Firebase Authentication (mobil/web, parola/telefon/federe sağlayıcı, hazır UI); Identity Platform (Firebase Auth + kurumsal: OIDC+SAML, MFA, IAP entegrasyonu).

**Secret Manager:** Sır adı global, veri opsiyonel bölgesel; versiyonlar immutable ama silinebilir, limitsiz; proje seviyesinde, varsayılan sadece project owners; Cloud Audit Logs her etkileşimi kaydeder; hardened key management + Cloud KMS entegrasyonu. Oluşturma: console/gcloud/kod, replication policy automatic vs user-managed. Alma: resource name (project ID + secret ID + version), `latest` anahtar kelimesi.

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Authentication vs Authorization:** Authentication = kimlik kanıtlama (credential sunma). Authorization = kanıtlanan kimliğin neye izinli olduğu (IAM).
- **Kimlik oluşturabilen vs oluşturamayan principal'lar:** Google Account ve service account kimlik oluşturabilir. Google group, Google Workspace account, Cloud Identity domain **oluşturamaz** — sadece toplu izin yönetimi kolaylığı sağlarlar, login credential'ları yoktur.
- **Google Workspace account vs Cloud Identity domain:** İkisi de organizasyondaki tüm hesapların sanal grubu ama Cloud Identity domain kullanıcılarının Google Workspace uygulama/özelliklerine erişimi **yoktur**.
- **Basic vs Predefined vs Custom role:** Basic = geniş/üretimde riskli. Predefined = Google yönetir, servise özel. Custom = sen yönetirsin, en az ayrıcalık için tam kontrol.
- **API key vs User account vs Service account:** API key zayıf ve çoğu API kabul etmez; user account = kişi + süreli OAuth token; service account = uygulama + role bağlı token.
- **Servis hesabı anahtarının 3 riski:** credential leakage (repo'ya sızma), privilege escalation (anahtar değişse bile ayrıcalık kalıcı), identity masking (kimlik gizleme). Mitigasyon: indirilen anahtarlardan kaçın.
- **`gcloud auth login` vs `gcloud auth application-default login`:** Birincisi CLI komutları için, ikincisi koddan/ADC için.
- **ADC arama sırası:** env var → gcloud kullanıcı kimlik bilgisi → attached SA (yoksa servis varsayılan SA'sı) → hata.
- **Karar ağacı özeti:** local dev → ADC login; GC'de non-GKE üretim → attached SA; GKE → Workload Identity; GC dışı + federation mümkün → Workload Identity Federation; federation yok → SA key (son çare).
- **Workload Identity vs Workload Identity Federation:** Birincisi GKE içi (Kubernetes SA → IAM SA), ikincisi GC dışı/multi-cloud/on-prem (OIDC token → GC access token), ikisi de SA key gerektirmez.
- **SA key en iyi uygulamalar (son çare):** kendi public key'ini yükle (Google'ın private key'ini indirme), koda/binary'ye gömme, en az ayrıcalık.
- **IAP:** Kod yazmadan, HTTPS uygulamalar için merkezi + uygulama-seviyesi erişim kontrolü; VPN/network firewall/karmaşık authz koduna alternatif.
- **Identity Platform vs Firebase Authentication:** İkisi de backend+SDK+UI ile kolay giriş sağlar; Identity Platform ek olarak OIDC+SAML, MFA, IAP entegrasyonu sunar (kurumsal).
- **Secret Manager:** Ad global, veri opsiyonel bölgesel; versiyonlar immutable-ama-silinebilir, limitsiz; varsayılan erişim sadece project owners; her etkileşim audit log'a düşer; Cloud KMS ile şifreleme entegrasyonu; replication policy automatic (herhangi bölge) vs user-managed (belirttiğin bölgeler).

---

> **Kapanış:** Bu modül, "kimlik ve erişim" sorusunu tek bir cevaba değil, **duruma göre değişen bir karar ağacına** dönüştürdü. IAM ile "kim-ne-nerede" üçlüsünü tanımlamayı, servis hesaplarının hem güçlü hem riskli doğasını, ADC'nin arka planda sessizce doğru kimlik bilgisini bulma mekanizmasını, GKE içi ve GKE dışı senaryolar için Workload Identity ailesinin iki farklı üyesini, ve son çare olarak servis hesabı anahtarını nasıl güvenle kullanacağını öğrendin. Bunun üstüne IAP ile kod yazmadan erişim kontrolü kurmayı, Firebase Authentication ve Identity Platform ile kullanıcı kimlik yönetimini devretmeyi, ve Secret Manager ile hassas verini tek, denetlenebilir bir yerde saklamayı gördün. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; özellikle isim benzerliği taşıyan (Workload Identity / Workload Identity Federation) ve amaç benzerliği taşıyan (Firebase Authentication / Identity Platform) çiftleri karıştırmadığından emin ol. Bir konuda takılırsan ilgili parçaya dön ve "bu yöntem hangi somut sorunu çözmek için var" sorusunu yeniden kur. Sıradaki modüle geçmeye hazırsın.
