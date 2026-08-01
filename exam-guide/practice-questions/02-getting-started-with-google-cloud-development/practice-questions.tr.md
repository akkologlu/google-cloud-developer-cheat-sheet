# Pratik Sorular — Modül 02: Google Cloud Geliştirmeye Başlamak

[`deep-dive/02-getting-started-with-google-cloud-development`](../../../deep-dive/02-getting-started-with-google-cloud-development/getting-started-with-google-cloud-development.md) modülünde işlenen **Developing Applications with Google Cloud: Foundations** içeriğine dayalı senaryo tabanlı pratik sorular.

Bu sorular, o modüldeki sınav tuzaklarına ve karar tablolarına ağırlık verir — gerçek Professional Cloud Developer sınavında insanların gerçekten yanlış yaptığı ayrımlar. [Cevap Anahtarı ve Açıklamalar](#cevap-anahtarı-ve-açıklamalar) bölümüne bakmadan önce her soruyu cevaplamayı dene.

---

## Sorular

**1.** Ekibin aynı uygulama derlemesini (build) geliştirme, staging ve üretim ortamlarına dağıtıyorsunuz. QA sürekli "testten geçen kod, üretimde çalışan kod değil" diye işaretliyor; çünkü bir geliştirici son sürümden önce staging veritabanı adresini sabit (constant) bir değer olarak koda gömmüş, sonra üretim derlemesi için bunu elle değiştirmiş. Ekip bu tür hataları gelecekte önlemek için ne yapmalı?

A. Veritabanı adresini kaynak kodunda sabit olarak sakla ve hangi ortama ait olduğunu açıklayan bir yorum ekle
B. Veritabanı adresini bir ortam değişkeni (environment variable) olarak sakla ve doğru değeri her ortam için ayrıca ver; kod tüm ortamlarda aynı kalsın
C. Kod tabanının iki ayrı dalını (branch) tut — biri staging değerleri için, biri üretim değerleri için
D. Veritabanı adresini, kodla birlikte commit edilen, ortam başına ayrı bir yapılandırma dosyası olarak sürüm kontrol deposunda sakla

**2.** Bir ekip, beş yıllık monolitik sipariş işleme uygulamasını mikroservislere bölüp bölmemeyi tartışıyor. Bir mühendis "mikroservisler modern standart, o yüzden her zaman onlara geçmeliyiz" diyor. Bir teknik lider buna karşı çıkıyor. Sınavla uyumlu doğru mantık nedir?

A. Mikroservisler bağımsız ölçeklendiği için monolitlere her zaman tercih edilmelidir
B. Monolitler dağıtımı daha basit olduğu için her zaman tercih edilmelidir
C. Karar, yeniden yapılandırmanın maliyetini (zaman, efor, operasyonel karmaşıklık) faydasına (bağımsız ölçekleme ve dağıtım) karşı tartmalıdır — bu otomatik bir seçim değildir
D. Karar yalnızca mühendislik ekibinin hangi mimaride daha deneyimli olduğuna dayanmalıdır

**3.** Bir sipariş servisi ile bir envanter servisinin çalışma zamanında gevşek bağlı (loosely coupled) olması gerekiyor; böylece siparişlerdeki bir ani artış, envanter yetişene kadar sipariş servisini durdurmasın ve her iki servis de bağımsız olarak güncellenebilsin. Bunu, sırasıyla bir "olay kuyruğu (event queue)" ve bir "mesaj kuyruğu (message queue)" rolünü üstlenen Google Cloud yapı taşlarıyla gerçekleştirmek istiyorsunuz. Hangi eşleştirme doğru?

A. Olay kuyruğu olarak bir Eventarc tetikleyicisi, mesaj kuyruğu olarak bir Pub/Sub konusu (topic)
B. Olay kuyruğu olarak bir Pub/Sub konusu (topic), mesaj kuyruğu olarak bir Eventarc tetikleyicisi
C. Olay kuyruğu olarak Cloud Tasks, mesaj kuyruğu olarak Cloud Scheduler
D. Olay kuyruğu olarak Cloud CDN, mesaj kuyruğu olarak Memorystore

**4.** Bir müşteri servisinin API'si `name`, `age` ve `email` alanları içeren bir yük (payload) döndürüyor. Bir e-posta gönderme servisi bu yükü tüketiyor ama yalnızca `name` ve `email` alanlarına ihtiyaç duyuyor. Altı ay sonra müşteri ekibi, yeni bir özelliği desteklemek için yüke bir `phone_number` alanı eklemek istiyor. E-posta servisindeki hangi tasarım kararı, bu alan eklendiğinde servisin bozulmasını önler?

A. E-posta servisi, yükü bugün mevcut olan her alanın bulunmasını zorunlu kılan katı bir şemaya göre ayrıştırır ve bilinmeyen alanları reddeder
B. Müşteri servisi, e-posta servisi onu tüketmeye başladıktan sonra yükünü asla değiştirmemelidir
C. E-posta servisi müşteri servisini iki kez çağırmalıdır — biri isim/e-posta için, biri toplam alan sayısının eşleştiğini kontrol etmek için
D. E-posta servisi yalnızca ihtiyaç duyduğu `name` ve `email` alanlarını okur, yükteki diğer alanları yok sayar

**5.** Bir Cloud Run servisi, oldukça değişken trafik altında ödeme (checkout) isteklerini işliyor. Kod incelemesinde biri, kullanıcının alışveriş sepetini istekler arasında hızlandırmak için servis örneğinin yerel belleğinde önbelleğe almayı öneriyor. Bu, bu modüldeki en iyi uygulamalara neden aykırıdır ve bunun yerine ne yapılmalıdır?

A. Sorun değil — Cloud Run örnekleri asla ölçek küçültmez, o yüzden bellek içi durum (state) her zaman güvenlidir
B. Bellekteki durum, bir kullanıcıyı belirli bir örneğe bağlar; bu da ölçeklendirmeyi veya örnekleri güvenle sonlandırmayı zorlaştırır. Sepet durumu bunun yerine Firestore gibi ayrı bir veritabanında kalıcılaştırılmalıdır
C. Örnek daha büyük bir makine türü kullandığı sürece yerel bellek kabul edilebilir
D. Sepet durumu bellek yerine konteynerin yerel diskine yazılmalıdır

**6.** Uygulamanız aşağı akış (downstream) bir ödeme API'sini çağırıyor. Üretimde iki farklı hata deseni ortaya çıkıyor: (1) çağrının başarısız olduğu ama API'nin bir iki saniye içinde tekrar sağlıklı hale geldiği ara sıra ağ sorunları, ve (2) ödeme API'sinin 20 dakika boyunca tamamen çöktüğü uzun süreli bir kesinti. Bu iki durum için sırasıyla doğru dayanıklılık stratejisi nedir?

A. Durum 1: devre kesiciyi (circuit breaker) aç ve çağırmayı durdur. Durum 2: başarılı olana kadar üstel geri çekilmeyle (exponential backoff) yeniden dene
B. Her iki durumda da: yeniden denemeler ucuz olduğu için hiç geri çekilme olmadan hemen ve tekrar tekrar dene
C. Durum 1: üstel geri çekilmeyle yeniden dene. Durum 2: devre kesiciyi aç ve servis toparlanana kadar trafik göndermeyi durdur
D. Her iki durumda da: hemen devre kesiciyi aç ve asla yeniden deneme

**7.** Uygulamanızın iki farklı önbellekleme ihtiyacı var: (1) her istekte arka uç mantığı tarafından okunan, yeniden hesaplaması pahalı kişiselleştirilmiş fiyatlandırma verisi, ve (2) dünyanın dört bir yanındaki kullanıcılar için hızlı yüklenmesi gereken statik JavaScript, CSS ve görsel varlıklar. Hangi Google Cloud servisleri bu iki ihtiyacı doğru eşleştirir?

A. Kişiselleştirilmiş fiyatlandırma verisi için Memorystore, statik varlıklar için Cloud CDN
B. Kişiselleştirilmiş fiyatlandırma verisi için Cloud CDN, statik varlıklar için Memorystore
C. Her iki ihtiyaç için de, ayrı bir önbellekleme katmanı olmadan Cloud Storage
D. Her iki ihtiyaç için de Firestore, çünkü yerleşik bellek içi önbelleklemesi vardır

**8.** Bir şirketin, yakın vadede yeniden yazılamayan ya da buluta taşınamayan 15 yıllık eski (legacy) bir hasar işleme sistemi var. Mobil uygulamaların ve iş ortağı entegrasyonlarının, her tüketicinin eski sistemin protokolünü doğrudan uygulamak zorunda kalmadan, hız sınırlama (rate limiting) ve merkezi güvenlikle birlikte temiz, modern bir REST API üzerinden bu sistemin verisine erişmesini istiyorlar. Eski sistemin önüne ne koymalılar?

A. Eski sistemin yanıtlarını Edge'de önbelleğe almak için bir Cloud CDN uç noktası
B. Her tüketicinin eski protokolü kendisinin uygulayacağı doğrudan bir ağ yolu
C. Proxy katmanı olmayan bir Compute Engine load balancer
D. Eski sistemi arkada tutup modern API'ler sunan bir API ağ geçidi/cephesi olarak Apigee

**9.** Bir startup, kullanıcıların e-posta/parola, SAML ve çok faktörlü kimlik doğrulama (MFA) ile kayıt olup giriş yapabilmesini istiyor ve bunun kendi kimlik bilgisi deposunu inşa edip güvenceye almadan, aylar değil günler içinde çalışır hale gelmesini istiyor. Bu modülden önerilen yaklaşım nedir?

A. Kendi yönettiği bir kullanıcı veritabanı ve parola özetleme (hashing) ile özel bir kimlik doğrulama servisi inşa etmek
B. Kimlik yönetimini Identity Platform / Firebase Authentication'a devretmek
C. Her kullanıcının paylaşılan bir IAM Service Account ile kimlik doğrulaması yapmasını sağlamak
D. Kullanıcı parolalarını şifrelenmiş olarak bir Cloud Storage bucket'ında saklamak ve uygulama kodunda doğrulamak

**10.** Ekibiniz Cloud Run üzerinde bir servis geliştiriyor ve konteyner içinde log dosyalarını ya da log döndürmeyi (rotation) yönetmeden, log tabanlı metrikler kurmak ve istekleri servisler arasında izlemek istiyor. Uygulama ne yapmalı?

A. Konteynerin yerel diskinde bir log dosyası açmak ve belirli bir programa göre döndürmek
B. Log girişlerini uygulama kodundan doğrudan bir Cloud Storage bucket'ına yazmak
C. Loglarını standart çıktıya (stdout) yazmak ve platformun bunları toplayıp merkezileştirmesine izin vermek
D. Tüm log satırlarını bellekte tamponlamak ve yalnızca kapanışta BigQuery'ye aktarmak

**11.** Bir ekip, paylaşılan bir dala yapılan her commit'in otomatik olarak bir derleme (build) tetiklemesini ve birim/entegrasyon testlerini çalıştırmasını istiyor; doğrulanan derlemenin otomatik olarak saklanıp yayına hazır olarak işaretlenmesini istiyor — ama üretime hiçbir şeyin ulaşmasından önce bir kişinin "dağıt" butonuna basmasını hâlâ istiyorlar. Bu, hangi uygulamalar kombinasyonunu tanımlar?

A. Continuous Integration + Continuous Delivery
B. Continuous Integration + Continuous Deployment
C. Otomatik test olmadan yalnızca Continuous Delivery
D. Manuel test ile yalnızca Continuous Deployment

**12.** Bir ekip, 15 yıllık monolitik bir hasar işleme uygulamasını mikroservislere göç ettiriyor. "Big bang" (her şeyi bir anda değiştirme) yeniden yazımı çok riskli bulunarak reddedildi. Bunun yerine, eski uygulamanın küçük parçalarını kademeli olarak değiştirmek istiyorlar; her isteği, o ana kadar göç ettirilmiş olana bağlı olarak ya eski uygulamaya ya da yeni servise yönlendiren bir yönlendirme katmanıyla. Hangi deseni tarif ediyorlar?

A. Blue/green dağıtımı
B. Canary sürümü
C. Devre kesici (circuit breaker) deseni
D. Strangler (boğucu) deseni

**13.** Bir ekip, bir servisin yeni sürümünü önce üretim trafiğinin yalnızca %5'ine yaymak, hata oranlarını ve gecikmeyi izlemek ve yeni sürüm sağlıklı görünüyorsa ancak %100'e kadar kademeli olarak artırmak istiyor. Bu hangi dağıtım stratejisi?

A. Blue/green dağıtımı
B. Canary sürümü
C. Strangler deseni
D. Continuous Delivery

**14.** Bir geliştirici 2026 yılında, bir dağıtım hattının parçası olarak derleme yapıtlarını (build artifacts) bir Cloud Storage bucket'ına kopyalayan yeni bir betik (script) yazıyor ve en iyi performansı, diğer `gcloud` betikleriyle tutarlı bir komut stiliyle birlikte istiyor. Hangi komutu kullanmalı?

A. `gsutil cp`
B. `bq load`
C. `gcloud storage cp`
D. `gcloud compute scp`

**15.** Bir platform ekibi, 200 kişilik bir mühendislik ekibindeki her mühendise, şirketin VPC'si içinde yaşayan, tarayıcıdan, SSH'tan ya da yerel bir IDE'den erişilebilen ve her mühendisin araçları yerel olarak kurmasını gerektirmeyen tutarlı, güvenli, kalıcı ve yeniden üretilebilir bir bulut geliştirme ortamı sağlamak istiyor. Hangi ürün bu işe uyar ve en bariz alternatif neden bu iş için yanlıştır?

A. Cloud Workstations — yönetilen, yeniden üretilebilir, kalıcı, VPC içinde kalan geliştirme ortamları sağlar; Cloud Shell ise bir saatlik hareketsizlikten sonra sonlanan, geçici, oturum başına bir yönetim kabuğudur ve bir ekibin birincil geliştirme ortamı olması amaçlanmamıştır
B. Cloud Shell — ücretsiz ve tarayıcı tabanlı olduğu için bir ekibin günlük geliştirme ortamı olarak yeterlidir
C. Cloud Code — yalnızca bir IDE eklentisidir, barındırılan bir ortam değildir
D. Tüm ekibe SSH kimlik bilgileri dağıtılan paylaşılan bir Compute Engine VM'i

---

## Cevap Anahtarı ve Açıklamalar

**1. Doğru cevap: B**
Yapılandırma değerleri kaynak kodunda sabit olarak değil, ortam değişkeni olarak yaşamalıdır; böylece tamamen aynı derleme yalnızca ortam değişkenleri değişerek dev, staging ve üretim arasında hareket edebilir. D seçeneği cazip bir çeldiricidir — değeri koddan çıkarır ama ortam başına yapılandırma dosyalarını depoya commit etmek, yapılandırma değişikliklerini yine kod değişiklikleriyle birleştirir ve "aynı derleme, farklı ortam değişkenleri" garantisini bozar.

**2. Doğru cevap: C**
Modül açıkça belirtir ki mikroservisler otomatik olarak "daha iyi" değildir — bir monoliti yeniden yapılandırmak maliyetlidir, bu yüzden karar bu maliyeti bağımsız ölçekleme ve dağıtımın faydasına karşı tartmalıdır. A seçeneği klasik sınav tuzağıdır: bağlamdan bağımsız olarak mikroservislerin her zaman doğru modern seçim olduğunu varsaymak.

**3. Doğru cevap: A**
Modül, Eventarc tetikleyicisini "olay kuyruğu" rolüne, Pub/Sub konusunu (topic) ise "mesaj kuyruğu" rolüne eşler. B seçeneği cazip bir çeldiricidir çünkü ikisini birbiriyle değiştirir — Pub/Sub sezgisel olarak daha genel bir "kuyruk" gibi hissettirir, ama modülün özel eşlemesi olaylar için Eventarc, mesajlar için Pub/Sub'dır.

**4. Doğru cevap: D**
Bir HTTP yükünde (payload) gevşek bağlılık, tüketicinin yalnızca ihtiyacı olan alanları okuması anlamına gelir; bu da yayıncının kimseyi bozmadan ileride yeni alanlar eklemesine izin verir. A seçeneği cazip bir çeldiricidir çünkü katı şema doğrulaması iyi bir mühendislik disiplini gibi görünür, ama tüketiciyi yükün tüm şekline sıkı sıkıya bağlar ve yeni bir alan eklendiği anda bozulur.

**5. Doğru cevap: B**
Durumsuz (stateless) bileşenler durumu içeride saklamamalı ya da paylaşmamalıdır; paylaşılan durum yaygın bir ölçeklenebilirlik darboğazıdır ve örnekler birbirinin yerine geçebilir olmalıdır ki herhangi biri herhangi bir isteği işleyebilsin. Durum, Firestore gibi ayrı bir kalıcı depoda yaşamalıdır. A seçeneği cazip bir çeldiricidir çünkü Cloud Run'ın örnekleri kısa süre canlı tutabilmesi doğrudur, ama bu bir garanti değildir ve buna güvenmek, esnek ve birbirinin yerine geçebilir örnekler tasarlamanın tüm amacını baltalar.

**6. Doğru cevap: C**
Geçici (transient) hatalar üstel geri çekilmeyle (exponential backoff) yeniden denemeyi gerektirir (zaten zorlanan bir arka ucu fazladan yüklememek için), uzun süreli/kalıcı hatalar ise servis toparlanana kadar trafiği tamamen durduran bir devre kesici (circuit breaker) gerektirir. A seçeneği cazip bir çeldiricidir çünkü tam olarak doğru teknikleri uygular ama hangi hata türünün hangi tekniği aldığını değiştirir — modülün açıkça uyardığı bir hata.

**7. Doğru cevap: A**
Memorystore (Redis/Memcached, tam yönetilen, bellek içi), yeniden hesaplaması pahalı uygulama verisi içindir; Cloud CDN (Google'ın küresel Edge ağı) ise kullanıcılara yakın sunulan web/statik içerik içindir. B seçeneği cazip bir çeldiricidir çünkü iki katmanı birbiriyle değiştirir — ikisi de "önbellekleme" olsa da yığının farklı katmanlarında çalışırlar.

**8. Doğru cevap: D**
Apigee, yeniden yazılamayan ya da taşınamayan eski sistemlerin önüne modern bir API cephesi koymak için modülün adlandırdığı çözümdür; bunu yaparken güvenlik, hız sınırlama, kota ve analitik de ekler. A seçeneği makul görünen bir çeldiricidir çünkü Cloud CDN de bir şeyin "önünde" durur, ama yalnızca statik içeriği önbelleğe alır — eski bir arka uç için API cephesi, güvenlik ya da hız sınırlama sağlamaz.

**9. Doğru cevap: B**
Kimlik yönetimini Identity Platform ya da Firebase Authentication'a devretmek, özel bir kimlik bilgisi deposu inşa edip güvenceye alma ihtiyacını ortadan kaldırır; her ikisi de yerel olarak e-posta/parola, SAML, OpenID Connect ve MFA'yı destekler. C seçeneği cazip bir çeldiricidir çünkü IAM Service Account'lar gerçek bir Google Cloud kimlik mekanizmasıdır, ama bunlar iş yükü/uygulama kimliği içindir, son kullanıcı kaydı ve girişi için değil.

**10. Doğru cevap: C**
Uygulamalar loglarını bir olay akışı gibi ele almalı ve stdout'a yazmalı; altta yatan platformun (Google Cloud Observability) bunları toplayıp merkezileştirmesine ve metrik/izleme oluşturmasına izin vermelidir — bu, yönetilecek kalıcı bir diski olmayan Cloud Run gibi serverless işlem için özellikle doğaldır. A seçeneği cazip bir çeldiricidir çünkü yerel bir log dosyası yönetmek "geleneksel" sunucu yaklaşımı gibi hissettirir, ama modülün belirttiği en iyi uygulamayla doğrudan çelişir ve serverless ortamlara uymaz.

**11. Doğru cevap: A**
Continuous Integration, commit üzerine otomatik derleme ve test adımını kapsar; Continuous Delivery ise doğrulanmış derlemeyi otomatik olarak yayına hazır şekilde saklamayı kapsar, ama son üretim adımını bir kişiye bırakır. B seçeneği cazip bir çeldiricidir çünkü Continuous Deployment "daha eksiksiz" ya da "daha otomatik" bir cevap gibi görünür, ama bu senaryonun ekibin korumak istediğini söylediği insan onayı adımını tam olarak kaldırır.

**12. Doğru cevap: D**
Strangler deseni tam olarak budur: eski bir uygulamanın parçalarını, her isteği eski ya da yeni implementasyona yönlendiren bir cephe (facade) arkasında kademeli olarak değiştirmek, ta ki eski sistem tamamen "boğulana" kadar. A seçeneği cazip bir çeldiricidir çünkü blue/green de iki şeyin yan yana var olmasını içerir, ama blue/green iki tam ortam arasında anlık, tam bir trafik geçişidir; kademeli, parça parça bir değiştirme değildir.

**13. Doğru cevap: B**
Canary sürümü, yeni bir sürümü önce trafiğin küçük bir dilimine yaymak, izlemek ve ardından kademeli olarak artırmak anlamına gelir — tehlikeyi yayılmadan önce fark eden bir kanarya kuşu gibi. A seçeneği cazip bir çeldiricidir çünkü blue/green de "güvenli bir yayın" stratejisidir, ama önce küçük bir yüzdeyle başlamak yerine tüm trafiği eski ortamdan yeniye bir anda çevirir.

**14. Doğru cevap: C**
`gcloud storage`, artık Cloud Storage'ı yönetmek için tercih edilen komut satırı aracıdır — `gsutil`'den daha iyi performans gösterir ve diğer `gcloud` komutlarının stiliyle eşleşir. A seçeneği cazip bir çeldiricidir çünkü `gsutil` hâlâ çalışır ve birçok eski öğreticide gösterilir, ama modül `gcloud storage`'ın artık tercih edilen araç olduğunu açıkça belirtir.

**15. Doğru cevap: A**
Cloud Workstations tam olarak bunun için tasarlanmıştır: müşterinin VPC'si içinde çalışan, yönetilen, yeniden üretilebilir, güvenli, kalıcı ekip geliştirme ortamları. B seçeneği cazip bir çeldiricidir çünkü Cloud Shell gerçek, ücretsiz ve tarayıcı tabanlıdır, ama hızlı yönetim görevleri için tasarlanmış, geçici, oturum başına bir yönetim kabuğudur (5 GB Persistent Disk, bir saatlik hareketsizlikten sonra sonlanır) — tüm bir mühendislik ekibinin günlük geliştirme çalışması için standartlaştırılmış bir ortam değildir.
