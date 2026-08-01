# Modül 4 — Kimlik Doğrulama ve Yetkilendirme: Uygulama Soruları

**Handling Authentication and Authorization** konusu için senaryo tabanlı uygulama soruları. Sorular, sınavın en çok test ettiği ayrımlara ağırlık verir: hangi principal'lar gerçekten kimlik doğrulayabilir, hangi auth yöntemi hangi dağıtım ortamına uyar, ADC'nin arama sırası, ve Workload Identity ile Workload Identity Federation arasındaki isim benzerliği tuzağı.

Cevap anahtarına bakmadan önce her soruyu yanıtlamayı dene. Kaynak metin: `deep-dive/04-authentication-and-authorization/authentication-and-authorization.md`.

---

## Sorular

**1.** Bir takım lideri, `data-analysts@company.com` adlı Google group'a `roles/storage.objectViewer` rolünü verir; böylece takımdaki 30 analist tek bir işlemle bir bucket'a okuma erişimi kazanır. Daha sonra bir analist, kendi girişini atlayıp bir backend script'inin bir API isteğini doğrudan grup "adına" doğrulamasını isteyip isteyemeyeceğini sorar (bir adımdan tasarruf etmek için). Bu mümkün müdür?

A. Evet — Google Groups kendi OAuth erişim token'larını üretebilir.
B. Hayır — Google Groups'un kendi login credential'ı yoktur; sadece toplu izin vermeyi kolaylaştırırlar, bu yüzden her analist yine kendi Google Account'uyla kimlik doğrulamalıdır.
C. Evet, ama yalnızca grup sahibi grupta MFA'yı etkinleştirirse.
D. Hayır — bir Google group'a yalnızca Service Account'lar eklenebilir, Google Account'lar eklenemez.

**2.** Bir startup Google Workspace'e abone değildir — 50 çalışanının şirket üzerinden Gmail, Docs ya da Drive erişimi yoktur. Yine de startup, tüm çalışanlarının Google Account'larını merkezi biçimde yönetmek ve IAM politikalarını organizasyon seviyesinde uygulamak istiyor. Bu ihtiyaca hangi principal türü uyar?

A. Google Workspace account
B. Cloud Identity domain
C. Google group
D. Tek, paylaşılan bir Service Account

**3.** Bir üretim projesinin güvenlik incelemesinde, mühendislerin çoğunun BigQuery veri kümelerini inceleyebilmek için proje seviyesinde temel **Viewer** rolüne sahip olduğu görülür. Bu rol aynı zamanda projedeki **her** kaynak türüne okuma erişimi verir. Güvenlik ekibi en az ayrıcalık ilkesine uymak için ne yapmalı?

A. Olduğu gibi bırak — Viewer salt okunur olduğu için üretimde gerçek bir risk taşımaz.
B. Temel Viewer rolünü, mühendislerin gerçekten ihtiyaç duyduğu BigQuery izinleriyle sınırlı bir predefined ya da custom rolle değiştir.
C. Herkesi Editor rolüne yükselt, böylece buldukları sorunları da düzeltebilirler.
D. Viewer'ı koru, ama VPC Service Controls etkinleştirerek telafi et.

**4.** Bir gece toplu işi (batch job), Compute Engine üzerinde gözetimsiz çalışır ve export dosyaları yazmak için Cloud Storage API'sini çağırır. Çalışma sırasında hiçbir insan müdahalesi yoktur. Bu API çağrılarını hangi mekanizma yetkilendirmeli?

A. İşin yapılandırma dosyasına gömülü bir API key.
B. VM üzerinde bir kez `gcloud auth login` çalıştırılarak elde edilen bir user account OAuth token'ı.
C. Yalnızca ihtiyaç duyduğu Storage izinleriyle sınırlı IAM rollerine sahip, toplu işi temsil eden bir Service Account.
D. Bir Firebase Authentication ID token'ı.

**5.** Bir şirket, indirilmiş bir Service Account özel anahtarının yanlışlıkla genel (public) bir depoya commit'lendiğini fark eder. Şirket bunu yakalamadan önce bir saldırgan bu anahtarı kullanarak Service Account'a (dolayısıyla kendine) ek, daha güçlü bir rol vermiştir. Şirket sızan anahtarı hemen siler. Şirket artık güvende midir?

A. Evet — anahtarı silmek, saldırganın yapabileceği her eylemi kalıcı olarak iptal eder.
B. Hayır — bu bir privilege escalation (ayrıcalık yükseltme) durumudur: saldırganın verdiği ek IAM rolü, sızan anahtar iptal edildikten sonra bile yürürlükte kalır; bu yüzden şirket saldırganın oluşturduğu bağlamaları (binding) da denetleyip elle kaldırmalıdır.
C. Evet, çünkü Service Account anahtarları bir sızıntı tespit edildiği anda otomatik olarak sona erer.
D. Hayır, ama sadece anahtarın saklanması için Secret Manager kullanılmadığı için.

**6.** Bir geliştirici, laptop'ında çalışan ve client library üzerinden doğrudan Cloud Vision API'sini çağıran bir Python script'i yazar; kodda hiçbir kimlik bilgisi belirtmez — Application Default Credentials'a güvenmektedir. Script'in kimlik doğrulayabilmesi için önce hangi komutu çalıştırmalıdır?

A. `gcloud auth login`
B. `gcloud auth application-default login`
C. `gcloud config set account`
D. `gcloud iam service-accounts keys create`

**7.** Bir Cloud Run servisine kullanıcı tanımlı (user-managed) bir Service Account eklenmiştir. Aynı zamanda, test sırasında yanlışlıkla container image'ına gömülmüş bir Service Account JSON dosyasını gösteren `GOOGLE_APPLICATION_CREDENTIALS` ortam değişkeni de ayarlanmış durumdadır. Application Default Credentials, çalışma zamanında gerçekte hangi kimlik bilgisini kullanır?

A. Eklenmiş Service Account'ı, çünkü eklenmiş Service Account'lar her zaman her şeyin önüne geçer.
B. `GOOGLE_APPLICATION_CREDENTIALS` tarafından işaret edilen Service Account anahtar dosyasını — çünkü ADC bu ortam değişkenini, gcloud kullanıcı kimlik bilgilerine ya da eklenmiş/varsayılan Service Account'a dönmeden **önce**, ilk sırada kontrol eder.
C. Hangisi daha yakın zamanda yapılandırıldıysa o kazanır.
D. Birden fazla kimlik bilgisi kaynağı olduğunda ADC her zaman hata fırlatır.

**8.** Üretimde çalışan bir uygulama, Compute Engine VM'i üzerinde çalışır ve Cloud SQL Admin API'sini çağırması gerekir. Anahtar dosyası yönetmeden kimlik bilgisi sağlamanın önerilen yolu nedir?

A. VM'nin varsayılan Service Account'ın JSON anahtarını indir ve `GOOGLE_APPLICATION_CREDENTIALS`'ı ona işaret edecek şekilde ayarla.
B. Uygulamanın ihtiyaç duyduğu izinlerle sınırlı, özel amaçlı bir Service Account'ı doğrudan VM'e ekle (attach).
C. VM üzerinde bir kez `gcloud auth application-default login` çalıştır ve ortaya çıkan dosyayı olduğu gibi bırak.
D. VM'nin başlangıç script'ine (startup script) bir Cloud SQL API key'i göm.

**9.** Aynı GKE cluster'ında birkaç mikroservis ayrı pod'lar olarak çalışıyor. Bugün hepsi, cluster'ın node Service Account'ın geniş izinlerini miras alıyor. Takım, bunun yerine her mikroservisin kendine ait, ince taneli bir IAM kimliğine sahip olmasını istiyor. Ne yapılandırmalılar?

A. Workload Identity Federation, çünkü herhangi bir harici workload için çalışır.
B. Workload Identity — her mikroservisin Kubernetes Service Account'ı kendine ait, özel bir IAM Service Account'ıyla eşleştirerek.
C. Her pod'a Kubernetes Secret olarak monte edilen tek bir Service Account anahtarı.
D. Her mikroservis için ayrı bir API key.

**10.** Bir şirketin CI/CD hattının bir kısmı AWS üzerinde (CodeBuild) çalışıyor ve bu AWS işlerinin Google Cloud'daki Artifact Registry'ye artifact yayınlaması gerekiyor. Şirket herhangi bir uzun ömürlü Service Account anahtarını yönetmekten kaçınmak istiyor ve AWS her iş için OIDC uyumlu bir kimlik token'ı üretebiliyor. Ne yapılandırmalılar?

A. Workload Identity, çünkü Google Cloud projesi dışındaki her workload için çalışır.
B. AWS Secrets Manager'da saklanan ve elle döndürülen indirilmiş bir Service Account anahtarı.
C. Workload Identity Federation — AWS tarafından verilen token'ı kısa ömürlü bir Google Cloud erişim token'ıyla değiştirerek, hiçbir Service Account anahtarı kullanmadan.
D. CodeBuild agent'ı içinde çalıştırılan `gcloud auth application-default login`.

**11.** Bir mimari inceleme dokümanı şöyle diyor: *"On-premises Jenkins sunucumuzun, bir anahtar dosyasına gerek kalmadan bir Google Cloud Service Account'ı impersonate edebilmesi için Workload Identity kullanacağız."* Bu, doğru yaklaşım ve terminoloji midir?

A. Yazıldığı gibi doğru.
B. Yanlış — Jenkins Google Cloud'un dışında (on-premises) çalışıyor, bu yüzden bu senaryo Workload Identity Federation gerektirir; Workload Identity özellikle GKE workload'larının bir IAM Service Account'ı impersonate etmesini ifade eder.
C. Yanlış — on-premises workload'ların hiçbir anahtarsız seçeneği yoktur, her zaman indirilmiş bir Service Account anahtarı kullanmalıdırlar.
D. Doğru, ama yalnızca Jenkins daha sonra bir GKE cluster'ı içinde çalışacak şekilde taşınırsa.

**12.** Eski (legacy) bir on-premises sistem OIDC tabanlı federation yapamıyor, bu yüzden takım son çare olarak ona bir Service Account anahtarı vermek zorunda. Önerilen en iyi uygulamaya göre, riski en aza indirmek için bu anahtarı nasıl oluşturmalılar?

A. Anahtar çiftini Google Cloud Console üzerinden normal şekilde oluştur ve ortaya çıkan özel anahtarı indir.
B. Kendi altyapılarında kendi açık/özel anahtar çiftini üret, yalnızca açık anahtarı Google Cloud'a yükle, özel anahtarı ise kendi güvenli teslim süreçleriyle çalışma ortamına ilet.
C. İndirilen özel anahtarı, tek bir artifact olarak dağıtılsın diye doğrudan uygulama binary'sine göm.
D. Özel anahtarı, yalnızca şirket içi çalışanların erişebildiği bir Git deposunda sakla.

**13.** Google Cloud üzerinde barındırılan dahili bir İK web uygulamasına internetten HTTPS ile erişilebilmesi gerekiyor, ama belirli sayfalara erişim yalnızca İK yöneticileriyle sınırlı olmalı. Takım bir VPN kurmaktan ve uygulama içine özel yetkilendirme kodu yazmaktan kaçınmak istiyor. Ne kullanmalılar?

A. Belirli kaynak IP aralıklarına izin veren VPC güvenlik duvarı kuralları.
B. Tüm İK personelinin bağlanması gereken geleneksel bir VPN.
C. Identity-Aware Proxy (IAP) — uygulama içi yetkilendirme koduna gerek kalmadan, uygulama seviyesinde kimlik ve politika tabanlı erişim kontrolü uygular.
D. Her İK yöneticisine verilen ayrı bir API key.

**14.** Bir şirket, mobil uygulamasının giriş ekranını başlangıçta Firebase Authentication ile kurmuştu. Yeni bir kurumsal müşteri artık kullanıcıları için SAML tabanlı tek oturum açma (SSO) ve çok faktörlü kimlik doğrulama (MFA) talep ediyor; şirket ayrıca dahili bir yönetici portalını Identity-Aware Proxy ile korumak istiyor. Ne yapmalılar?

A. Firebase Authentication'da kal ve sadece bir MFA eklentisi etkinleştir.
B. Identity Platform'a geçiş yap — Firebase Authentication ile aynı temel üzerine kurulu olan bu ürün, OIDC/SAML ile giriş, MFA ve IAP entegrasyonunu kurumsal ihtiyaçlar için ekler.
C. Herhangi bir kimlik doğrulama ürünü yerine bir Cloud Identity domain'e geç.
D. Yalnızca bir OAuth 2.0 onay ekranı (consent screen) yapılandır; başka değişikliğe gerek yok.

**15.** Bir uyumluluk (compliance) gereksinimi, sır olarak saklanan bir veritabanı parolasının (a) verisinin yalnızca AB bölgelerinde tutulmasını, (b) kuruluşun kontrol ettiği anahtarlarla şifrelenmesini ve (c) her okumanın denetim için loglanmasını şart koşuyor. Secret Manager'da hangi yapılandırma bunu karşılar?

A. `automatic` replication policy kullan, varsayılan IAM yapılandırmasına (yalnızca proje sahipleri) güven ve ek şifrelemeyi atla — Secret Manager varsayılan olarak her şeyi halleder.
B. Yalnızca AB bölgeleriyle sınırlı bir `user-managed` replication policy kullan, sır versiyonunu Cloud KMS ile şifrele ve Cloud Audit Logs'un etkin olduğundan emin ol.
C. Secret Manager'ı tamamen atla ve parolayı düz bir ortam değişkeni olarak sakla, çünkü bu şekilde düşünmek daha basittir.
D. `automatic` replication policy kullan ve kolaylık olsun diye tüm doğrulanmış (authenticated) kullanıcılara okuma izni ver.

---

## Cevap Anahtarı ve Açıklamalar

**1. Doğru cevap: B.** Google Groups (ve Google Workspace account'lar ile Cloud Identity domain'ler) kendi login credential'larına sahip değildir — bunlar yalnızca birçok principal'a tek seferde IAM bağlaması uygulamanı sağlamak için var olur. Cazip ama yanlış olan düşünce şudur: "bir grup bir rolü taşıyabiliyorsa, hareket de edebilmelidir" — oysa bir isteği gerçekten doğrulayabilen tek şeyler Google Account'lar ve Service Account'lardır.

**2. Doğru cevap: B.** Cloud Identity domain, Google Workspace müşterisi olmadan merkezi kimlik yönetimi isteyen kuruluşlar için sanal gruptur — kullanıcılarının özellikle Gmail, Docs, Drive gibi Workspace uygulamalarına erişimi yoktur. Google Workspace account cazip ama yanlış seçenektir çünkü aynı görünür ("tüm organizasyonun sanal grubu"), ama bu, startup'ın sahip olmadığı Workspace uygulama erişimini ima eder.

**3. Doğru cevap: B.** Viewer gibi temel roller kasıtlı olarak geniştir — takımın gerçekten ihtiyaç duyduğu tek kaynak türüne değil, projedeki **her** kaynak türüne okuma erişimi verirler, bu da en az ayrıcalık ilkesini ihlal eder. "Viewer salt okunur olduğu için güvenlidir" tuzağın ta kendisidir: salt okunur olmak sınırlı kapsamlı olmak anlamına gelmez, ve fazladan okuma erişimi yine de fazladan saldırı yüzeyi demektir (örneğin config'leri, sır metadata'sını ya da başka takımların verisini ifşa edebilir).

**4. Doğru cevap: C.** Service account tam olarak bu durum için tasarlanmıştır: gözetimsiz, makineden-makineye bir çağrı, bir kişinin kimliği yerine IAM rolleriyle yetkilendirilir. API key cazip ama yanlış seçenektir çünkü pratik görünür, ama sızan bir API key tam ve süresiz erişim verir ve çoğu Google API'si bu tür yazma işlemleri için API key'i zaten kabul etmez.

**5. Doğru cevap: B.** Bu, klasik bir privilege escalation (ayrıcalık yükseltme) riskidir: bir saldırgan sızan bir anahtarla ek IAM bağlamaları verdiğinde, bu bağlamalar anahtarın kendisinden bağımsız hale gelir, dolayısıyla anahtarı döndürmek ya da silmek bunları geri almaz. Cazip ama yanlış cevap "anahtarı silmek sorunu çözer"dir — bu, anahtarın gelecekteki kullanımını durdurur, ama saldırganın kendine zaten verdiği kalıcı erişimi geri almaz.

**6. Doğru cevap: B.** `gcloud auth application-default login`, özellikle kodun (CLI'ın değil) geliştirici olarak kimlik doğrulayabilmesi için Application Default Credentials'ı beslemek içindir. Tuzak `gcloud auth login`'dir — neredeyse aynı görünür ama `gcloud` CLI'ının kendisini doğrulamak içindir; ADC'yi hiç beslemez.

**7. Doğru cevap: B.** ADC'nin arama sırası önce `GOOGLE_APPLICATION_CREDENTIALS` ortam değişkenini kontrol eder; yalnızca bu ayarlı değilse ADC gcloud kullanıcı kimlik bilgilerine, ardından eklenmiş Service Account'a, ardından servisin varsayılan Service Account'a döner. Tuzak, "eklenmiş Service Account her zaman kazanır" varsayımıdır çünkü bu önerilen üretim kalıbıdır — ama bu senaryodaki gibi başıboş bir ortam değişkeni ayarlıysa (yanlışlıkla test dosyasının gömülmesi gibi), o sessizce öncelik kazanır; artık ortamda kalan ortam değişkenlerinin gerçek bir üretim hatası olmasının nedeni de tam olarak budur.

**8. Doğru cevap: B.** Özel amaçlı bir Service Account'ı doğrudan VM'e eklemek, üretim workload'larına kimlik bilgisi sağlamanın tercih edilen yoludur — Google kimlik bilgisi yaşam döngüsünü senin adına yönetir ve hiçbir anahtar dosyasının indirilmesi, kopyalanması ya da döndürülmesi gerekmez. Dikkat çeken yanlış seçenek (indirip `GOOGLE_APPLICATION_CREDENTIALS`'ı ayarlamak), eklenmiş Service Account'ların ortadan kaldırmak için var olduğu tam da o anahtar yönetimi riskini (sızıntı, elle döndürme) yeniden ortaya çıkarır.

**9. Doğru cevap: B.** Workload Identity, bir Kubernetes Service Account'ın bir IAM Service Account'ı impersonate etmesine izin veren GKE'ye özgü mekanizmadır; her workload'a, node'un geniş Service Account'ı paylaşmak yerine kendine ait ince taneli bir kimlik verir. Workload Identity Federation, neredeyse aynı isim yüzünden cazip ama yanlış seçenektir — o mekanizma Google Cloud dışında çalışan workload'lar için tasarlanmıştır, GKE cluster'ı içindeki pod'lar için değil.

**10. Doğru cevap: C.** Workload Identity Federation tam olarak bu durum için vardır: Google Cloud dışında çalışan ve OIDC uyumlu bir token üretebilen harici bir workload, bu token'ı kısa ömürlü bir Google Cloud erişim token'ıyla değiştirir, Service Account anahtarı ihtiyacını tamamen ortadan kaldırır. Tuzak, isim benzerliğinden dolayı "Workload Identity"yi seçmektir — o mekanizma yalnızca GKE cluster'ları içinde geçerlidir, AWS'de barındırılan workload'lar için değil.

**11. Doğru cevap: B.** Bu, ders kitabı düzeyinde bir Workload Identity - Workload Identity Federation karıştırmasıdır: Workload Identity GKE'ye özgüdür (Kubernetes Service Account'ın bir IAM Service Account'ı impersonate etmesi), Google Cloud dışında çalışan herhangi bir workload — on-premises dahil — ise bunun yerine Workload Identity Federation gerektirir. C seçeneği yanlıştır çünkü federation, OIDC destekleyen on-premises workload'lar için var olan tam olarak anahtarsız seçenektir.

**12. Doğru cevap: B.** Bir Service Account anahtarından gerçekten kaçınılamadığında önerilen en iyi uygulama, kendi anahtar çiftini üretmek, yalnızca açık anahtarı Google Cloud'a yüklemek ve özel anahtarı tamamen kendi güvenli teslim sürecin içinde tutmaktır — bu şekilde Google özel anahtarını hiçbir zaman eline bile almaz. "Anahtarı normal şekilde indir" seçeneği en varsayılan, en yaygın yoldur, ama en iyi uygulamaların tam olarak karşı uyardığı, daha yüksek riskli yol da odur.

**13. Doğru cevap: C.** IAP tam olarak bunun için tasarlanmıştır: VPN'ler, ağ güvenlik duvarı kuralları ya da uygulama içine özel yetkilendirme kodu olmadan, HTTPS üzerinden merkezi, kimlik-farkında, uygulama seviyesinde erişim kontrolü. VPN seçeneği cazip görünür çünkü dahili uygulamaları korumanın geleneksel yoludur, ama o ağ seviyesinde çalışır, kullanıcı/sayfa bazında değil — ve takım bundan açıkça kaçınmak istiyor.

**14. Doğru cevap: B.** Identity Platform, Firebase Authentication artı kurumsal katmandır — OpenID Connect/SAML ile giriş, çok faktörlü kimlik doğrulama ve IAP entegrasyonu — aynı temel altyapı üzerine kuruludur, bu yüzden geçiş yapmak mevcut yatırımı korur. "Sadece bir MFA eklentisi etkinleştir" seçeneği yanlıştır çünkü Firebase Authentication'ın kendisi SAML ile giriş ya da IAP entegrasyonu sunmaz; bunlar özellikle Identity Platform gerektirir.

**15. Doğru cevap: B.** `user-managed` replication policy, sır verisini belirli bölgelere sabitlemeni sağlar (veri konumu gereksinimini karşılar), Cloud KMS entegrasyonu sır versiyonlarını kuruluşun kontrol ettiği anahtarlarla şifrelemeni sağlar, ve Cloud Audit Logs her okuma ve yazmayı uyumluluk için kaydeder. `automatic` replication seçeneği cazip görünür çünkü daha basit varsayılandır, ama payload'ın herhangi bir bölgede saklanmasına açıkça izin verir, bu da yalnızca-AB gereksinimini karşılamaz.
