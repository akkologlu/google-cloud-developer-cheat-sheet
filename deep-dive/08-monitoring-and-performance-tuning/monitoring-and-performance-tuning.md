# İzleme ve Performans Ayarı (Monitoring and Performance Tuning) — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 8 — Monitoring and Performance Tuning** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Modülün merkezinde tek bir fikir var: **Bir uygulamayı üretime almak işin yarısıdır; onun neden çalıştığını, ne zaman bozulacağını ve neden yavaşladığını bilmek diğer yarısıdır.** Google Cloud bu ikinci yarı için beş aracı bir şemsiye altında topluyor — **Google Cloud Observability.** Bu deep dive, bu beş aracın (Cloud Monitoring, Cloud Logging, Error Reporting, Cloud Trace, Cloud Profiler) her birinin **neden var olduğunu**, **nasıl çalıştığını** ve **ne zaman hangisine başvuracağını** öğretiyor; ayrıca Prometheus ve Google Cloud Managed Service for Prometheus'un bu resme nasıl oturduğunu da kapsıyor. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül tam olarak neyi öğretiyor ve neden önemli?

Bir düşünce deneyiyle başlayalım. Uygulamanı Cloud Run'a, GKE'ye ya da Compute Engine'e dağıttın (Modül 6 ve 7'de gördüğün gibi). Uygulama artık **çalışıyor.** Peki şu sorulara cevap verebilir misin?

- Uygulaman şu anda **sağlıklı** mı, yoksa sessizce mi bozuluyor?
- Kullanıcılarından biri "yavaş" dediğinde, bu **nerede** yavaşlıyor — veritabanında mı, ağda mı, kodun bir bölümünde mi?
- Dün gece bir hata patlaması oldu; **neden** oldu, hangi kod satırı suçlu?
- Üretimde **hangi fonksiyon** CPU'nun çoğunu yiyor ve test ortamında bunu nasıl fark edemedin?

Bu sorulara cevap veremiyorsan, elinde çalışan bir uygulama var ama **görünürlüğün (visibility) yok.** İşte bu modül tam olarak bu görünürlüğü nasıl kazanacağını öğretiyor. Modülün açılış cümlesi bunu netleştiriyor: **Google Cloud Observability, metrikleri, logları ve metadata'yı birleştirir** — ister Google Cloud'da, ister AWS'de, ister on-premises, ister hibrit bir ortamda çalışıyor olsun, servis davranışlarını ve sorunları **tek bir kapsamlı görünümden** hızlıca anlayıp gerekirse harekete geçmeni sağlar.

Dikkat et: Bu araç seti **Google Cloud'a özel değil.** Google, "sadece bizim bulutumuzu izleyebilirsin" demiyor; "nerede çalıştırırsan çalıştır, tek bir yerden bakabilirsin" diyor. Bu, gerçek dünyada çoğu şirketin hibrit ya da çoklu-bulut ortamlarda çalıştığı gerçeğine verilen doğrudan bir cevap.

Modül, beş bileşeni sırayla ele alıyor:

1. **Cloud Monitoring** — performans, kullanılabilirlik ve genel sağlık görünürlüğü; dashboard'lar ve uyarılar.
2. **Cloud Logging** — log toplama, arama, analiz; yapılandırılmış (structured) log kavramı; Ops Agent; Prometheus ile ilişkisi.
3. **Error Reporting** — hataları otomatik yakalama, gruplama, bildirim.
4. **Cloud Trace** — dağıtık izleme (distributed tracing); isteklerin uygulamanda ne kadar sürede işlendiğini görme.
5. **Cloud Profiler** — üretimdeki uygulamanın CPU ve bellek kullanımını, düşük ek yükle, kaynak koduna kadar izleme.

Bu beşini birbirinden bağımsız araçlar gibi değil, **birbirini tamamlayan bir zincir** gibi düşün: Monitoring sana "bir şey ters gidiyor" der; Logging "ne olduğunu" gösterir; Error Reporting "hangi hatanın, kaç kez" olduğunu özetler; Trace "nerede yavaşladığını" gösterir; Profiler "hangi kod satırının kaynağı tükettiğini" gösterir. Şimdi bunları sırayla, derinlemesine görelim.

---

# BÖLÜM 1 — Cloud Monitoring: Güvenilirliğin Temeli

## Neden var?

Modülün kendi cümlesiyle: **"Monitoring, uygulama güvenilirliğinin temelidir."** Bu güçlü bir iddia ve nedeni basit: Güvenilirlik dediğin şey, aslında **"sorunu kullanıcı fark etmeden önce senin fark etmen"** demektir. Eğer bir sorunu ancak müşteri şikâyet ettiğinde öğreniyorsan, zaten güvenilirlik kaybetmişsindir. Cloud Monitoring, bu erken uyarı sistemini kurmanı sağlar.

Cloud Monitoring; Google Cloud servislerinden ve uygulamalarından **metrikleri, olayları (events) ve metadata'yı toplar**, ve sorunlardan zamanında haberdar olman için **uyarı politikaları (alerting policies)** oluşturmanı sağlar. Bu, tam olarak "performans, kullanılabilirlik ve genel sağlık görünürlüğü" cümlesinin somut karşılığıdır.

> **Analoji:** Cloud Monitoring'i bir hastanenin yoğun bakım ünitesindeki monitörlere benzet. Hasta (uygulaman) her an nabız, tansiyon, oksijen seviyesi (metrikler) ile izlenir. Bir değer normal aralığın dışına çıktığında, doktor hastanın yatağına koşmadan **önce** alarm çalar. Monitoring de aynı işi yapar: sorunu, kullanıcı fark etmeden **önce** sana bildirir.

## Neden geliştiriciler bunu umursamalı?

Modül, geliştiricilerin monitoring'e neden önem vermesi gerektiğini üç ayrı kullanım senaryosuyla açıklıyor. Bunları ayrı ayrı görelim çünkü her biri **farklı bir soruyu** cevaplıyor.

### 1. Dashboard'larla temel soruları cevaplamak

Cloud Monitoring ile hem **özel (custom) dashboard'lar** kurabilir hem de **hazır (out-of-the-box) dashboard'ları** kullanabilirsin. Uygulamalarını ve altyapını — Google Cloud'da, on-premises'te ya da başka bulutlarda çalışsın — izleyebilirsin. Bu dashboard'lar altyapının ve uygulamaların sağlığını izlemeni ve **anormallikleri kolayca fark etmeni** sağlar.

Monitoring, zaman içinde bakıldığında, uygulama kullanım desenlerindeki **trendleri** de gösterir. Modülün verdiği somut örnekler:

- Veritabanım ne kadar büyük ve ne kadar hızlı büyüyor?
- Günlük aktif kullanıcı sayım ne kadar hızlı artıyor?
- Uygulamamın hangi özellikleri en çok kullanılıyor?

Bu sorular **acil** değil — bugün cevaplamazsan uygulaman çökmez. Ama ürün ve kapasite kararların için kritik. Örneğin veritabanı büyüme hızını bilmiyorsan, ne zaman ölçek değiştirmen gerektiğini de bilemezsin.

### 2. Acil dikkat gerektiren durumları yakalamak

Monitoring aynı zamanda **"neyin acil dikkat gerektirdiğini"** söyler:

- Uygulama şu anda **bozuk mu**, yoksa **yakında mı bozulacak**?
- Metrik trendlerine ya da limitlerine **uyarı (alert)** koyarak, felaket bir arızaya yol açmadan önce sorunları fark edip düzeltebilirsin.

Bu, ilk maddeden farklı bir kullanım biçimidir: birincisi **pasif gözlem** (trend takibi), ikincisi **aktif tetikleme** (bir eşik aşıldığında bildirim). İkisi de aynı verilerden (metriklerden) besleniyor ama amaçları farklı.

### 3. Retrospektif (geriye dönük) analiz

Üçüncü kullanım biçimi, bir olay **olduktan sonra** ne olduğunu anlamaktır:

- Uygulamamızın gecikmesi (latency) bir gecede dramatik biçimde arttı — **aynı zamanda başka ne oldu?**
- Kullanıcıları etkilemeden önce bu sorunu düzeltebilmem için **alarm kurabileceğim bir koşul var mı?**

Bu senaryo, monitoring'in sadece "anlık durum" göstermediğini, **geçmişe dönük korelasyon kurmak için de** kullanıldığını gösteriyor. Bir gecede gecikme artmışsa, aynı zamanda bir deploy mi yapıldı, bir trafik artışı mı oldu, bir bağımlı servis mi yavaşladı — dashboard'lardaki zaman serisi verisi bu soruların cevabını bulmanı sağlar.

> **Neden bu üç kullanım biçimi birlikte önemli?** Çünkü tek başına hiçbiri yeterli değil. Sadece dashboard'a bakıp trend izlemek, aktif bir sorunu kaçırabilir (kimse sürekli ekrana bakmıyor). Sadece alarm kurmak, "neden" sorusuna cevap vermez. Sadece retrospektif analiz yapmak, sorunu **önceden** engellemez. Cloud Monitoring'in gücü, üçünü de **aynı veri kümesi** üzerinden sunmasıdır.

## Cloud Monitoring nasıl güvenilirliği artırır?

Modül, Cloud Monitoring'in güvenilirliği artırma biçimini şöyle özetliyor: Kullanıcıların Google Cloud ve çoklu-bulut ortamlarını izleyerek **trendleri tanımlamasına ve sorunları önlemesine** olanak tanır. Cloud Monitoring ile **izleme yükünü (overhead) azaltabilir** ve **sinyal-gürültü oranını (signal-to-noise ratio) iyileştirebilirsin** — bu da sorunları daha hızlı tespit edip düzeltmeni sağlar.

Bu son nokta pratikte çok önemlidir. "Sinyal-gürültü oranı" derken kastedilen şudur: Eğer her küçük dalgalanmada uyarı alıyorsan (gürültü çok), gerçek bir sorunu (sinyal) fark etmen zorlaşır — çünkü sürekli gelen sahte alarmlara karşı **duyarsızlaşırsın.** İyi kurgulanmış bir monitoring sistemi, sadece gerçekten önemli olan sapmalarda seni uyarır.

## Dört Altın Sinyal (Four Golden Signals)

Modül burada çok önemli, sınavda da sık sorulan bir çerçeve veriyor: **En azından, uygulamaların için yakalaman gereken bazı temel metrikler vardır.** Uygulama dashboard'ların, dört altın sinyali içermelidir: **gecikme (latency), trafik (traffic), hatalar (errors), doygunluk (saturation).**

Bu dört sinyal, herhangi bir servisin sağlığını anlamak için **evrensel bir başlangıç noktasıdır.** Yeni bir servis kurduğunda "hangi metrikleri izlemeliyim?" diye düşünüyorsan, cevap her zaman bu dörtle başlar.

### Latency (Gecikme)

**Gecikme, bir isteği karşılamak için geçen süredir.** Burada modülün verdiği kritik uyarıya dikkat et: **Başarılı ve başarısız isteklerin gecikmesini ayırt etmelisin.**

Neden? Modülün örneği şöyle: Bir veritabanına ya da başka bir arka uç servise **bağlantı kaybı** yüzünden oluşan bir HTTP hatası, **çok hızlı** çözülmüş olabilir (bağlantı hemen reddedilir, istek hemen başarısız olur). Ama bu bir **HTTP 500 hatası** olduğu için başarısız bir isteği gösterir. Eğer 500 hatalarını genel gecikme ölçümüne dahil edersen, **yanıltıcı metrikler** elde edebilirsin — çünkü hızlı başarısız olan istekler, ortalama gecikmeyi yapay olarak **düşürür**, uygulamanın aslında ne kadar "sağlıklı yavaş" olduğunu gizler.

> **Sezgi:** Bir sınavda tüm soruları çok hızlı ama yanlış cevaplayan bir öğrenciyi düşün. "Ortalama cevap süresi" metriğine bakarsan bu öğrenci çok hızlı görünür — ama aslında hiçbir şey **başaramamıştır.** Latency metriğini hatalarla karıştırmak, tam olarak bu yanılgıya düşmektir.

### Traffic (Trafik)

**Trafik, sistemine ne kadar talep bindiğinin ölçüsüdür.** Sistem-spesifik bir metrikle ölçülür — yani "trafik" her sistemde aynı şekilde tanımlanmaz:

- Bir web sunucusu için trafik, **saniyedeki HTTP/HTTPS istek sayısıdır.**
- Bir NoSQL veritabanına giden trafik, **saniyedeki okuma veya yazma işlemi sayısıdır.**

Bu ayrım önemli çünkü sınavda "trafiği nasıl ölçersin" diye sorulduğunda, doğru cevap **sistemin doğasına** bağlıdır — tek bir evrensel birim yoktur.

### Errors (Hatalar)

**Hatalar, başarısız isteklerin sayısını gösterir.** Modül, "başarısızlık" kriterinin ne kadar geniş olabileceğini üç örnekle gösteriyor:

1. **Açık (explicit) bir hata** — örneğin bir HTTP 500 yanıtı.
2. **Yanlış içerikli başarılı yanıt** — HTTP 200 döner ama içerik **yanlıştır.** Bu ince bir noktadır: sunucu "başarılı" dese bile, aslında istemciye hatalı veri vermiştir.
3. **Politika hatası (policy error)** — uygulaman bir saniyelik yanıt süresi **vaat ediyor** ama bazı istekler bir saniyeden **uzun sürüyor.** Burada hiçbir HTTP hata kodu yok, ama SLA/SLO açısından bu bir hatadır.

> **Neden bu üçüncü tür (politika hatası) önemli?** Çünkü çoğu kişi "hata" derken sadece HTTP 5xx kodlarını düşünür. Ama gerçek dünyada bir servis teknik olarak "çalışıyor" olsa bile, **taahhüt ettiği performans veya doğruluk düzeyini** tutturamıyorsa bu da bir hatadır. Bu, ileride SLO (Service Level Objective) kavramıyla doğrudan bağlantılıdır.

### Saturation (Doygunluk)

**Doygunluk, uygulamanın ne kadar "dolu" olduğunu**, yani hangi kaynakların gerilip hedef kapasiteye yaklaştığını gösterir.

Modülün verdiği kritik uyarı: **Sistemler, %100 kullanıma ulaşmadan önce performans olarak bozulmaya başlayabilir.** Bu yüzden **kullanım hedeflerini (utilization targets) dikkatli belirlemelisin.**

> **Neden bu sezgiye aykırı geliyor?** Çünkü "kapasitenin %100'ü doluysa sorun var" diye düşünmek doğaldır ama gerçekte çoğu sistem (özellikle CPU ve bellek) **%100'e ulaşmadan çok önce** kuyruklar oluşmaya, gecikme artmaya başlar. Örneğin CPU kullanımı %70'i geçtiğinde bile bazı sistemlerde kuyruklama gecikmesi ciddi biçimde artabilir. Bu yüzden "doygunluk = %100 kullanım" diye basitleştirmek yanlıştır; doygunluk hedefini sistemin **gerçek davranışına göre** kalibre etmen gerekir.

**Dört altın sinyal — özet tablo:**

| Sinyal | Ne ölçer? | Örnek metrik | Dikkat edilecek tuzak |
| --- | --- | --- | --- |
| Latency | İsteği karşılama süresi | ms cinsinden yanıt süresi | Başarılı/başarısız istekleri **ayrı** ölç |
| Traffic | Sisteme binen talep | HTTP req/sn, DB read-write/sn | Metrik sistem-spesifiktir |
| Errors | Başarısız istek sayısı | HTTP 5xx, yanlış içerikli 200, SLA ihlali | "Başarılı" HTTP kodu bile hata olabilir |
| Saturation | Kaynakların ne kadar dolu olduğu | CPU/bellek/disk kullanımı | %100'den **önce** bozulma başlayabilir |

> **Sınav tuzağı:** "Dört altın sinyal nedir?" sorusunda dördünü de sayabilmen yetmez — her birinin **tanımındaki inceliği** bilmen gerekir: latency'de başarılı/başarısız ayrımı, errors'da "200 ama yanlış içerik" ihtimali, saturation'da "%100'den önce bozulma" uyarısı. Sınav soruları genelde tam bu incelik noktalarını test eder.

---

# BÖLÜM 2 — Cloud Logging: Geliştiricinin En Değerli Aracı

## Neden var?

Modülde Mike Dunker'ın vurgusuyla başlıyoruz: **Cloud Logging, uygulama geliştiricileri için en değerli olabilecek araçlardan biridir.** Loglar ve metrikler, uygulamanın **nasıl çalıştığını** anlamana yardımcı olur. **Sağlam bir log sistemi, geliştirici verimliliği için ve uygulamanın durumunu anlamak için kritiktir.**

Bunu neden bu kadar vurguluyoruz? Çünkü metrikler sana **"ne kadar"** sorusunu cevaplar (kaç istek, ne kadar gecikme), ama **"neden"** sorusunu cevaplamaz. Bir isteğin neden başarısız olduğunu, hangi kod yolundan geçtiğini, hangi parametrelerle çağrıldığını anlamak için **loglara** ihtiyacın vardır.

Cloud Logging, **depolama, arama, analiz ve izleme desteğine sahip, gerçek zamanlı bir log yönetim sistemidir.** Google Cloud kaynaklarından logları **otomatik olarak toplar**; ayrıca kendi uygulamalarından da log toplayabilirsin.

## Logs Explorer — Tekil log kayıtlarını görmek

**Logs Explorer**'ı kullanarak **tekil log kayıtlarını görüntüleyebilir** ve **ilişkili log kayıtlarını bulabilirsin.** Uygulaman içindeki çağrı akışını (flow of calls) loglamak, uygulamanın **nasıl çalıştığını ya da neden beklendiği gibi çalışmadığını** anlamana yardımcı olur.

> **Analoji:** Logs Explorer'ı bir uçağın kara kutusuna benzet. Bir kaza (hata) olduğunda, kara kutu her saniyenin kaydını tutmuştur; sen olay anına yakın kayıtları çekip **ne olduğunu adım adım** yeniden kurabilirsin.

## Log Analytics — Loglar üzerinde SQL ile toplu analiz

Loglar sadece hata ayıklama için değil, **uygulama performansını analiz etmek için de** kullanılabilir. Modülün örneği: **Log Analytics** arayüzünü kullanarak loglarında **toplu (aggregate) işlemler** yapabilirsin. Bu arayüzle, log verini sorgulamak ve performansı anlamana yardımcı olmak için **SQL kullanırsın.**

Bu önemli bir ayrım: Logs Explorer tekil kayıtlara odaklanırken, Log Analytics loglarını **bir veri kümesi** gibi ele alıp SQL sorgularıyla toplama, gruplama, sayma gibi işlemler yapmanı sağlar. Örneğin "son bir saatte kaç tane 500 hatası oldu, hangi endpoint'lerden geldi" gibi bir soruyu SQL ile cevaplayabilirsin.

## Log-based alert ve log-based metric

Cloud Logging'i, loglarında **belirli türde olaylar** gerçekleştiğinde seni bilgilendirecek şekilde yapılandırabilirsin. İki farklı mekanizma var ve bunları **doğru senaryoda** kullanmak önemli:

**Log-based alert (log tabanlı uyarı):** Bir log girdisinde **belirli bir desen (pattern)** göründüğünde bildirim göndermek için oluşturulur. Örneğin belirli bir kritik hata mesajı loglara düştüğü **anda** haber almak istiyorsan bu senindir.

**Log-based metric (log tabanlı metrik):** Alternatif olarak, zaman içinde **trendleri** ya da **olayların oluşumunu** izlemek isteyebilirsin. Bu durumlarda log tabanlı metrik oluşturursun. Log tabanlı metrikler şu durumlarda uygundur: **bir mesajın oluşum sayısını saymak** ve **oluşum sayısı bir eşiği aştığında bildirim almak** istediğinde. Metrikler ayrıca verindeki **trendleri gözlemlemek** ve değerler **kabul edilemez bir şekilde** değiştiğinde bildirim almak için de kullanılabilir.

> **Sınav ayrımı:** *"Belirli bir olay olduğu anda haber ver"* → **log-based alert.** *"Bir olayın kaç kez olduğunu say, eşiği aşınca haber ver ya da trendi gözlemle"* → **log-based metric.** Birincisi anlık/olaysal, ikincisi sayımsal/trendsel.

## Ops Agent — VM'lerden log ve metrik toplama

Compute Engine örneklerinde çalışan üçüncü taraf uygulamalardan log ve metrikleri Cloud Logging'e akıtmak için **Ops Agent**'ı kurabilirsin.

**Ops Agent'in mimarisi iki ayrı bileşenden oluşur:**

- **Fluent Bit** — yüksek verimli (high-throughput) log toplama için kullanılır.
- **OpenTelemetry Collector** — metrik toplama için kullanılır.

Bu ayrım önemli: Ops Agent tek bir monolitik araç değil, **loglama için özelleşmiş bir bileşen ve metrik için özelleşmiş bir bileşenin** bir araya gelmesiyle oluşuyor — her biri kendi işinde en iyi olan açık kaynak projeler kullanılarak.

**Ops Agent'in log toplama yetenekleri:**

- **Standart sistem log konumlarından** (`/var`, `/log`, `/syslog`) logları **otomatik olarak toplar.**
- Dosya tabanlı loglar **özelleştirilebilir yollar (paths)** ve **yenileme aralığı (refresh interval)** ile de yapılandırılabilir.
- **Esnek işleme (flexible processing)** desteği sunar:
  - Metin loglarını **yapılandırılmış (structured) loglara ayrıştırma (parse)**.
  - Log girdilerini **alan (field) ekleyip/kaldırıp/yeniden adlandırarak** değiştirme.
  - Etiketlere (labels) ve düzenli ifadelere (regular expressions) dayanarak logları **hariç tutma (exclude).**

**Ops Agent'in metrik toplama yetenekleri:**

- Hiçbir yapılandırma olmadan **standart sistem metriklerini** toplar: **CPU, disk, bellek, ağ, süreçler (processes).**
- **Küratörlenmiş (curated) üçüncü taraf uygulama metrikleri** de toplanabilir. Modülün verdiği örnekler: **Apache Tomcat, Apache web server, NGINX.**

## Diğer compute ortamlarında logging — önceden yapılandırılmış

Cloud Logging, bazı ortamlarda **önceden yapılandırılmış (preconfigured)** gelir — ayrıca kurulum yapmana gerek kalmaz:

- **Cloud Run servisleri ve fonksiyonları**, **yerleşik (built-in) logging desteğine** sahiptir. Uygulamalar tarafından yazılan loglar **otomatik olarak** Cloud Logging'e gönderilir.
- **Google Kubernetes Engine**'de, cluster'ın için **"observability for GKE" entegrasyonunu** etkinleştirerek logging'i açabilirsin.

### GKE'de kalıcılık sorunu: Neden Cloud Logging şart?

Burada modül çok pratik ve sınav-dostu bir nokta veriyor: **Kubernetes logları GKE içinde kalıcı olarak saklanmaz.**

- **Container logları**, host pod'u kaldırıldığında **silinir.**
- **Sistem logları**, yeni loglara yer açmak için **periyodik olarak temizlenir.**
- **Cluster olayları (events)**, **bir saat sonra** kaldırılır.

Logları Cloud Logging'e göndermek, log kayıtlarının **ihtiyacın olduğu kadar uzun süre** saklanmasını sağlar.

> **Neden bu kadar önemli?** Çünkü bir pod çöktükten ve GKE onu otomatik olarak yeniden oluşturduktan bir saat sonra, sorunun ne olduğunu anlamak için o pod'un loglarına bakmak istersen ve loglar Cloud Logging'e gönderilmediyse, **o kanıt artık yoktur.** Bu, "üretim sorununu anlamak için loglara ihtiyacım var ama loglar zaten silinmiş" senaryosunun tam nedenidir.

## Text log vs Structured log

Cloud Run servisleri ve fonksiyonları için, sadece **standart çıktıya (stdout) ve standart hataya (stderr) yazabilirsin**, loglar **otomatik olarak** Cloud Logging'e teslim edilir. Metin dizeleri, log girdisinin **`textPayload`** alanına konur. Log alındığı zaman, logu üreten kaynak, log adı gibi başka alanlar da metin mesajıyla birlikte loglanır.

Ama modül burada net bir tavsiye veriyor: **Metin logları yerine yapılandırılmış (structured) log kullanmak önerilir.**

**Neden metin logları yetersiz kalır?**

- Metin loglarının bir **log seviyesi (log level)** yoktur, bu yüzden metin loglarının içinde **gerçekten önemli olan içeriği bulmak zor olabilir.**
- Yapılandırılmış log verisi içindeki **alanlar sorgularla aranabilir**, bu da log analizini **çok daha kolay** hale getirir.

**Yapılandırılmış log nasıl çalışır?**

Yapılandırılmış bir log girdisi için, **tek satırlık serileştirilmiş JSON** loglarsın; bu JSON, log girdisinin **`jsonPayload`** alanına yerleştirilir. JSON verisi kullandığında, bazı **özel alanlar** JSON payload'undan **ayrıştırılır (stripped)** ve üretilen log girdisindeki karşılık gelen alana yazılır. Örneğin:

- **`severity`** alanını belirterek **log seviyesini** ayarlayabilirsin.
- **`message`** özelliği sağlandığında, logun **ana görüntü metni (main display text)** olarak kullanılır.

Modülün örneği şöyle işliyor: İlk satır bir **metin log girdisi** oluşturur. Kodun geri kalanı, bir **yapılandırılmış log girdisi** oluşturmanın örneğidir. Yapılandırılmış girdi, log girdisi içinde etiketler (labels), log severity'si ve bir `component` alanı oluşturmak için özel alanlar kullanır.

> **Sınav tuzağı — "Text log da yeterlidir, neden uğraşayım?" yanılgısı:** Metin logu hızlı ve kolay yazılır, ama **arama, filtreleme ve seviye bazlı ayrıştırma** yapamazsın. Sorgu ile "sadece ERROR seviyesindeki logları göster" gibi bir filtre uygulamak istiyorsan, bu ancak **structured log**'un `severity` alanı sayesinde mümkündür. Sınavda "üretim ortamında log analizini kolaylaştırmak için ne önerilir" diye sorulursa cevap **structured (JSON) logging**'dir.

**Text log vs Structured log karşılaştırması:**

| Özellik | Text log | Structured (JSON) log |
| --- | --- | --- |
| Depolandığı alan | `textPayload` | `jsonPayload` |
| Log seviyesi | Yok | `severity` alanı ile var |
| Aranabilirlik | Zor (düz metin arama) | Kolay (alan bazlı sorgu) |
| Ana görüntü metni | Tüm metin | `message` alanı |
| Önerilen mi? | Basit/hızlı senaryolar için | **Evet, genel olarak önerilir** |

---

# BÖLÜM 3 — Prometheus ve Google Cloud Managed Service for Prometheus

## Prometheus nedir ve neden önemlidir?

Loglama ve uyarı için düşünebileceğin bir başka araç **Prometheus**'tur. Prometheus, **açık kaynaklı bir sistem izleme ve uyarı araç setidir (systems monitoring and alerting toolkit).** VM'lerde ve Kubernetes'te çalışan servisleri izleyebilir. **Kubernetes iş yüklerini ve cluster'larını izlemek, ve iş yükleri/cluster'lar sağlıksız olduğunda uyarı vermek için çok popüler bir çözümdür.**

Prometheus, metrikleri **zaman serisi verisi (time series data)** olarak toplar ve saklar. Bu zaman serisi verisi, uygulama metriklerindeki **trendleri görmene** yardımcı olabilir. Ayrıca Prometheus, metriklerinden **dashboard'lar ve uyarılar oluşturmak için kullanılabilecek güçlü bir sorgu dili olan PromQL**'i sağlar.

Prometheus birçok fayda sağlar, ama **Prometheus altyapısını yönetmek ve ölçeklendirmek zor olabilir.** İşte tam bu noktada Google Cloud devreye giriyor.

## Google Cloud Managed Service for Prometheus

Bu soruna bir çözüm, **Google Cloud Managed Service for Prometheus**'u kullanmaktır. Bu, **tamamen yönetilen (fully-managed) bir Prometheus çözümüdür** ve Prometheus'u ölçekte manuel olarak yönetme ve işletme yükünü ortadan kaldırır.

Çözümün önemli özellikleri:

- **Çoklu-bulut (multi-cloud) ve projeler-arası (cross-project)** çalışır. Google Cloud'da, başka bulutlarda ya da on-premises çalışan tüm uygulamaların, **Cloud Monitoring kullanılarak tek bir bakış açısından (single pane of glass)** yönetilebilir.
- Çok sayıda **veri toplayıcı (data collector)** mevcuttur:
  - **Managed collectors (yönetilen toplayıcılar)**, tüm Kubernetes ortamları için (GKE dahil) **önerilir.** Yönetilen toplayıcılarda, Prometheus'un işletilmesi **tamamen Kubernetes operatörü tarafından** halledilir.
  - **Self-deployed collectors (kendi kendine dağıtılan toplayıcılar)**, normal Prometheus binary'sinin **doğrudan yerine geçen (drop-in replacement)** çözümlerdir.
  - **OpenTelemetry collectors** ve **Ops Agent** de, Prometheus ile kullanılabilecek metrikleri toplayabilir.
- Managed Service for Prometheus, **PromQL sorgu API'sini** çağırabilen herhangi bir sorgu arayüzünü destekler — **Cloud Monitoring ve Grafana dahil.**
- Cloud Monitoring'de mevcut olan **1.500'den fazla ücretsiz metrik**, Managed Service for Prometheus'a veri göndermeden bile **sorgulanabilir.**
- Managed Service for Prometheus ayrıca, **Cloud Monitoring ve Prometheus metrikleri için tamamen bulut-tabanlı bir uyarı boru hattı (alerting pipeline)** sağlar.

> **Neden bu önemli?** Çünkü birçok ekip Kubernetes dünyasına zaten Prometheus bilgisiyle geliyor — PromQL öğrenmiş, dashboard'lar kurmuş. Google Cloud, bu bilgiyi **çöpe atmanı istemiyor.** Managed Service for Prometheus, senin zaten bildiğin PromQL'i ve Prometheus ekosistemini korurken, altyapı yönetimi yükünü (ölçekleme, yüksek kullanılabilirlik, depolama) Google'a devretmeni sağlıyor — tıpkı GKE'nin Kubernetes'i yönetilen hale getirmesi gibi, ama bu sefer izleme katmanı için.

> **Sınav tuzağı — "Kubernetes'te managed collector mu, self-deployed mı kullanmalıyım?" sorusu:** Tüm Kubernetes ortamları (GKE dahil) için **managed collectors önerilir** — çünkü işletimi Kubernetes operatörü halleder. Self-deployed collectors, normal Prometheus binary'sinin yerine geçen bir seçenektir ve daha çok **Kubernetes dışı** ya da özel kontrol gerektiren senaryolar için düşünülür.

---

# BÖLÜM 4 — Error Reporting: Hataları Otomatik Yakalamak ve Gruplamak

## Neden var?

**Error Reporting**, uygulamandaki hatalar konusunda seni bilgilendirir ve **nedenini araştırmana yardımcı olur.** Çalışan bulut servislerindeki çökmeleri (crashes) **sayar, analiz eder ve toplar (aggregates).**

Bu, Cloud Logging'in genel arama/analiz yeteneğinden **farklı, özelleşmiş bir araçtır.** Cloud Logging'de teorik olarak tüm hataları bulabilirsin ama binlerce log satırı arasında "hangi hata en sık oluyor, hangisi yeni" sorusunu manuel olarak cevaplamak zahmetlidir. Error Reporting tam olarak bu işi otomatikleştirir.

## Merkezi hata yönetim arayüzü

**Merkezi bir hata yönetim arayüzü**, sıralama (sorting) ve filtreleme (filtering) yetenekleri sağlar; **zamanlama, oluşum sayısı, ilk ve son görülme tarihleri, etkilenen kullanıcı sayısı** gibi hata detaylarını gösterir.

**Yeni hatalar bulunduğunda e-posta ve mobil uyarılar** almayı tercih edebilirsin (opt-in). Uygulama hata olayları, arayüzde **saniyeler içinde** işlenir ve görüntülenir.

## Hatalar nasıl tespit edilir?

Hatalar iki yoldan biriyle tespit edilir:

1. **Error Reporting API** tarafından **açıkça raporlanır** — yani uygulaman kodda bir hatayı yakalayıp aktif olarak API'ye bildirir.
2. **Log girdilerini inceleyerek çıkarım (inference) yapılır** — Error Reporting, log kayıtlarını **yaygın metin desenleri (common text patterns)**, örneğin **stack trace'ler**, için inceler ve bunları hata olarak tanır.

Bu ikinci yol çok pratiktir: Uygulamanı Error Reporting kullanacak şekilde özel olarak değiştirmesen bile, eğer stack trace'lerini normal şekilde loglarsan, Error Reporting bunları **otomatik olarak** yakalayabilir.

## Gruplama ve deduplikasyon

Hata olayları, **stack trace'leri analiz ederek akıllıca hata gruplarına (error groups) gruplanır ve deduplike edilir** — bu, oluşum sayısından bağımsız olarak, karşılaştığın **farklı hataları** anlamana yardımcı olur.

Uygulamanın en çok görülen ya da yeni hatalarını **net bir dashboard'da** görebilirsin. Stack trace'ler, **önemli olan kısma odaklanmana yardımcı olacak** şekilde ayrıştırılır ve gösterilir. Bir hata olayının stack trace'i, o hata grubu içindeki **oluşumların listesiyle birlikte** gösterilir — böylece benzer hata olaylarını **aynı anda** incelemek kolaylaşır.

> **Analoji:** Error Reporting'i bir müşteri şikâyet merkezine benzet. Yüzlerce farklı şikâyet gelse bile, iyi bir sistem bunları "aynı sorundan kaynaklanan" gruplara ayırır — böylece "500 kişi farklı şikâyette bulundu" yerine "aslında tek bir kök nedenden kaynaklanan 3 farklı sorun var, biri 480 kişiyi etkiliyor" görürsün. Bu, hangi sorunu **önce** çözmen gerektiğine karar vermeni kolaylaştırır.

## Ortama göre etkinleştirme

Servisinin nerede çalıştığına bağlı olarak Error Reporting'i etkinleştirmen gerekebilir:

- **Cloud Run servisleri ve fonksiyonları için otomatik olarak etkindir.**
- **Google Kubernetes Engine'de**, cluster'ı oluştururken **`cloud-platform` erişim kapsamını (access scope)** ekleyerek etkinleştirebilirsin.
- **Compute Engine üzerinde çalışan uygulamalar**, VM servis hesabına **Error Reporting Writer rolü** verilerek hata olaylarını raporlayabilir.

> **Örüntüyü fark ettin mi?** Bu, Cloud Logging bölümünde gördüğün "önceden yapılandırılmış ortamlar" örüntüsünün **aynısı**: Cloud Run en az efor gerektirir (otomatik), GKE orta düzey yapılandırma gerektirir (access scope), Compute Engine en fazla manuel adım gerektirir (IAM rolü atama). Bu, Modül 7'de gördüğün "kontrol arttıkça operasyonel efor artar" eksenine paralel bir örüntüdür — burada da geçerli.

## Diller ve entegrasyon yolları

Error Reporting, birçok popüler dilde kullanılabilir: **Go, Java, Node.js, PHP, Python, Ruby, .NET.** Entegrasyon için **client library'ler, REST API'yi**, ya da **Cloud Logging üzerinden hata gönderme** yöntemlerini kullanabilirsin.

**Node.js örneği** şu akışı izler: Error Reporting kütüphanesini dahil ettikten sonra, bir **client** oluşturursun, yeni bir **error event** yaratırsın, olaya detaylar eklersin, ve hatayı **raporlarsın (report)**. Hata olayı **asenkron olarak** raporlanır — böylece kodun, hata olayının teslimini beklemeden **işlemeye devam edebilir.**

> **Neden asenkron raporlama önemli?** Çünkü bir hata olduğunda, kullanıcının isteğine yanıt vermeyi hata raporlama işlemi yüzünden **geciktirmek istemezsin.** Asenkron raporlama, "hatayı kaydet" işiyle "kullanıcıya yanıt ver" işini birbirinden ayırır — biri diğerini bloklamaz.

## Error Reporting konsolunu kullanmak

Hatalarını görmek için Google Cloud konsolunda **Error Reporting** sayfasını açarsın. Varsayılan olarak, Error Reporting sana **sıklık sırasına göre**, son zamanlarda oluşan, açık (open) ve onaylanmış (acknowledged) hataların bir listesini gösterir.

Hatalar, **stack trace'leri analiz edilerek** gruplanır ve deduplike edilir. Error Reporting, dilin için kullanılan **yaygın çerçeveleri (frameworks) tanır** ve hataları buna göre gruplar.

- Hataları **oluşum sayısına** ya da **ilk ve son görüldüğü zamana** göre sıralayabilirsin.
- Bir hata grubuna bir **issue tracker linki** de bağlayabilirsin.

Bir hata kaydını seçmek bir **Error Details** sayfası açar. Bu sayfada, hata grubuyla ilgili bilgileri inceleyebilirsin: **zaman içindeki oluşum sayısı, spesifik hata olayları ve stack trace'ler.**

Örnek bir hatayla ilişkili log girdisini görmek için, son örnekler panelindeki (recent samples panel) herhangi bir kayıttan **"View Logs"**'a tıklarsın — bu seni **Logs Explorer**'a götürür.

> **Bu geçiş neden önemli?** Çünkü burada Error Reporting ile Cloud Logging'in **birbirine bağlandığını** görüyorsun. Error Reporting sana "hangi hata, ne sıklıkta" özetini verir; ama o hatanın etrafındaki **tam bağlamı** (o anda başka ne loglanmıştı, hangi istek parametreleriyle geldi) görmek istediğinde, Logs Explorer'a geçersin. Bu iki araç birbirinin **rakibi değil, tamamlayıcısıdır.**

---

# BÖLÜM 5 — Cloud Trace: Gecikmeyi Görselleştirmek

## Neden var?

Google Cloud, uygulamanın performansını ve kararlılığını yönetmene yardımcı olacak başka araçlar da sunar. **Cloud Trace**, uygulamalarından **gecikme verisi (latency data) toplayan ve Google Cloud konsolunda görüntüleyen dağıtık bir izleme (distributed tracing) sistemidir.**

Cloud Trace, isteklerin uygulaman içinde **nasıl yayıldığını (propagate)** takip etmeni ve **ayrıntılı, neredeyse gerçek zamanlı (near-real-time) performans içgörüleri** almanı sağlar.

Bunu neden yapmak istersin? Modülün Cloud Profiler bölümünde de vurgulanan bir gerçek var: **Üretim sistemlerinin performansını anlamak ünlü şekilde zordur.** Bir istek senin uygulamanda tek bir kod bloğunda işlenmiyor; birden fazla servise (mikroservise), veritabanına, dış API'ye gidip geliyor olabilir. "Neresi yavaş?" sorusunu cevaplamak için tüm bu adımları **tek bir zaman çizelgesinde** görmen gerekiyor — işte Cloud Trace bunu sağlıyor.

## Nasıl çalışır?

Cloud Trace, uygulamalarındaki **gecikmeyi toplar ve analiz eder.** Uygulamanın gelen istekleri işlemesinin ne kadar sürdüğünü ve isteği işlerken **RPC çağrıları gibi işlemleri tamamlamasının** ne kadar sürdüğünü anlamana yardımcı olur.

Gecikme verisi **URL bazında (per-URL)** raporlanır, bu da en fazla gecikme gösteren işlemlere odaklanmanı sağlar.

### Performans değişikliklerini otomatik olarak fark etmek

Cloud Trace, performansta değişiklikleri tanımlamana yardımcı olabilir. Cloud Trace, **en üst (top) uç noktalar (endpoints) için**, önceki günün performansını **bir önceki haftanın aynı günündeki performansla karşılaştıran** bir **günlük rapor (daily report)** otomatik olarak oluşturur.

Ayrıca, hangi trace'lerin dahil edileceğini seçerek **özel bir analiz raporu (custom analysis report)** da oluşturabilirsin.

> **Neden "aynı gün, bir önceki hafta" karşılaştırması?** Çünkü çoğu uygulamanın trafiği **haftalık desenler** izler (örneğin Pazartesi her zaman yoğun, hafta sonu sakin). "Dün ile bugünü" karşılaştırmak yanıltıcı olabilir (dün Pazar, bugün Pazartesi olabilir); ama "geçen Pazartesi ile bu Pazartesi"yi karşılaştırmak, gerçek bir performans **regresyonunu** trafik desenindeki doğal dalgalanmadan ayırt etmeni sağlar.

## İki veri gönderme yolu

Trace verisini Cloud Trace'e göndermenin **iki yolu** vardır:

**1. Otomatik izleme (automatic tracing).** Bazı yapılandırmalar otomatik izlemeyi destekler. **Cloud Run servisleri ve fonksiyonlarından gelen ve giden HTTP isteklerinin gecikme verisi otomatik olarak toplanır.** Ancak dikkat: **Servislerin İÇİNDEKİ gecikme verisi toplanmaz.** Yani Cloud Run'a bir istek geldiğinde ve gittiğinde ölçülür, ama o isteğin servis içinde hangi fonksiyonda ne kadar zaman geçirdiği **görünmez.**

**2. Uygulamayı enstrümante etmek (instrument the application).** Otomatik izlemenin topladığından **daha ayrıntılı bilgi** sağlar. Cloud Run üzerinde çalışan uygulamaları enstrümante etmeyi **tercih edebilirsin.** Dilin için mevcutsa, **OpenTelemetry ve ilişkili Cloud Trace Exporter önerilen çözümdür.** Alternatif olarak, **Cloud Trace API'yi kullanarak özel yöntemler** yazabilir ya da **Cloud Trace client library'lerini** kullanabilirsin.

> **Sınav tuzağı — "Cloud Run kullanıyorum, o zaman trace'leme otomatik ve tam kapsamlıdır" yanılgısı:** Otomatik izleme sadece **giren/çıkan HTTP isteklerini** kapsar. Servisin **içindeki** çağrıları (örneğin bir veritabanı sorgusu ya da başka bir mikroservise yapılan RPC çağrısı) görmek istiyorsan, **enstrümantasyon (instrumentation) yapman şart.** "Servis içi gecikmeyi görmek istiyorum" sorusu her zaman enstrümantasyona işaret eder, sadece otomatik izlemeye değil.

Bir **tracing client**, zamanlama verisini toplar ve Cloud Trace'e gönderir. Ardından veriyi görüntülemek ve analiz etmek için **Google Cloud konsolunu** kullanırsın.

## Trace ve span kavramları

Bu iki terim, Cloud Trace'in temel birimleridir ve sınavda karışabilir:

- **Trace (iz)**, **tek bir işlemin (operation)** tamamlanma süresini tanımlar.
- **Span**, trace içindeki **bir alt-işlemin (suboperation)** tamamlanma süresini tanımlar.
- **Bir trace, bir ya da daha fazla span'dan oluşur.**

> **Analoji:** Bir trace'i bir yemek siparişinin **baştan sona tüm sürecine** benzet (sipariş verildi → mutfağa iletildi → hazırlandı → servis edildi). Her bir adım (mutfağa iletim, hazırlama, servis) ayrı bir **span**'dır. Trace, tüm yolculuğun toplam süresidir; span'lar, o yolculuğun içindeki her bir durağın süresidir.

Trace'lerin ve span'ların **görselleştirilmesi**, uygulamalarındaki performans sorunlarını **takip etmene** yardımcı olabilir.

## Trace Explorer

**Trace Explorer** sayfası, bireysel trace'leri bulmanı ve **ayrıntılı olarak incelemeni** sağlar.

- **Scatter plot (dağılım grafiği)**, seçili zaman aralığındaki **her istek için bir nokta** gösterir.
- Bir isteğe ait noktanın **xy-koordinatları**, isteğin **zamanına ve gecikmesine** karşılık gelir.
- Gösterilen istekleri, **method ya da status code gibi istek özniteliklerine (attributes)** göre filtreleyebilirsin.

Bir trace'i incelemek için, scatter plot'taki bir **noktaya tıklarsın.** **Trace Details** paneli, bu trace ve trace içindeki span'lar hakkındaki detayları görüntüler. Span üzerindeki **noktalar**, o span'ın alt-işleminin tamamlanması sırasında **not düşülen (annotated) olayları** gösterir.

Gecikmenin **görsel göstergesi**, uygulamanın **hangi bölümlerinin performans sorunlarına yol açtığını** belirlemene yardımcı olabilir.

> **Pratik senaryo:** Bir kullanıcı "sayfa yükleme çok yavaş" diyor. Trace Explorer'a gidip zaman aralığını daraltıyorsun, scatter plot'ta **yüksek gecikmeli** (grafiğin üst kısmındaki) noktaları görüyorsun, birine tıklıyorsun ve Trace Details panelinde hangi span'ın (belki bir veritabanı sorgusu, belki dış bir API çağrısı) toplam sürenin **büyük kısmını** yediğini görüyorsun. İşte "nerede yavaş?" sorusuna Cloud Trace tam olarak bu şekilde cevap veriyor.

---

# BÖLÜM 6 — Cloud Profiler: Üretimde Performans Profili Çıkarmak

## Neden var?

Modül burada çok gerçekçi bir zorlukla başlıyor: **Üretim sistemlerinin performansını anlamak ünlü şekilde zordur.** Performansı bir **test ortamında** ölçmeye çalışmak, çoğu zaman **üretim ortamının özelliklerini yeniden yaratmakta başarısız olur.**

Bu neden böyle? Çünkü test ortamları genelde üretimdeki **gerçek trafik hacmini, gerçek veri boyutunu, gerçek eşzamanlılığı (concurrency)** ve gerçek altyapı yükünü taklit etmez. Bir fonksiyon test ortamında hızlı çalışabilir ama üretimde binlerce eşzamanlı istekle karşılaştığında tamamen farklı davranabilir.

**Cloud Profiler**, **üretim uygulamalarından CPU kullanımı ve bellek tahsisi (memory allocation) bilgisini sürekli olarak toplayan, istatistiksel (statistical), düşük ek yüklü (low-overhead) bir profildir.** Cloud Profiler bu bilgiyi, **onu üreten kaynak koduna atfeder (attributes)** — bu da uygulamanın **hangi bölümlerinin en fazla kaynak tükettiğini** tanımlamana yardımcı olur.

## Nasıl çalışır?

Modülün diğer tanımı, tekniği biraz daha netleştiriyor: Cloud Profiler, **istatistiksel teknikler ve düşük etkili enstrümantasyon (low-impact instrumentation)** kullanır ve bu, **tüm üretim uygulama örnekleri boyunca (across all production application instances)** çalışır. Bu sayede, uygulamanın performansı hakkında **uygulamayı yavaşlatmadan tam bir resim** sağlar.

Buradaki iki kelimeye dikkat et: **"istatistiksel"** ve **"düşük ek yüklü".** Bu, Cloud Profiler'ın **her tek işlemi** izlemediği anlamına gelir (bu, çok fazla ek yük yaratırdı ve uygulamayı yavaşlatırdı). Bunun yerine, **örnekleme (sampling)** tekniği kullanır — periyodik olarak "şu anda hangi kod çalışıyor" diye örnekler alır ve bu örneklerden istatistiksel olarak **doğru bir kaynak tüketim resmi** çıkarır. Bu yaklaşım, Cloud Profiler'ın **üretimde sürekli açık bırakılabilmesini** sağlar — çünkü uygulamanın performansını gözle görülür biçimde etkilemez.

> **Analoji:** Cloud Profiler'ı, bir fabrikadaki her makineyi her saniye tek tek izlemek yerine, fabrikanın içinde **rastgele anlarda anlık fotoğraflar** çeken bir gözlemciye benzet. Yeterince fotoğraf biriktiğinde, "hangi makine en çok zaman harcıyor" sorusuna **istatistiksel olarak güvenilir** bir cevap verebilirsin — üstelik hiçbir makineyi yavaşlatmadan.

Cloud Profiler, **performans sorunlarını tanımlamana ve ortadan kaldırmana** yardımcı olur.

> **Neden Cloud Profiler ile Cloud Trace'i karıştırmamalısın?** İkisi de "performans" kelimesini içeriyor ama **farklı sorulara** cevap veriyorlar:
> - **Cloud Trace**, bir **isteğin** uygulaman (ve servisler) içinde nasıl **yayıldığını**, hangi adımın ne kadar sürdüğünü gösterir — **istek bazlı, zaman ekseninde** bir görünüm.
> - **Cloud Profiler**, uygulamanın **kod düzeyinde** hangi fonksiyonların CPU ve bellek tükettiğini gösterir — **kaynak kullanımı bazlı, kod satırı ekseninde** bir görünüm.
>
> Soru "hangi istek adımı yavaş?" diyorsa → **Cloud Trace.** Soru "hangi kod fonksiyonu CPU'yu tüketiyor?" diyorsa → **Cloud Profiler.**

---

# Beş Aracı Bir Arada Görmek: Karşılaştırmalı Tablo

Şimdi beş aracı yan yana koyup neyin ne işe yaradığını netleştirelim.

| Araç | Neyi toplar/gösterir? | Temel soru | Ne zaman başvurursun? |
| --- | --- | --- | --- |
| **Cloud Monitoring** | Metrikler, olaylar, metadata; dashboard'lar, uyarı politikaları | Sistem genel olarak sağlıklı mı? | Sürekli — genel sağlık, trend takibi, eşik alarmları |
| **Cloud Logging** | Ham log kayıtları (text/structured); Log Analytics ile SQL analizi | Ne oldu, hangi detaylarla? | Bir olayın tam bağlamını incelerken |
| **Error Reporting** | Gruplanmış, deduplike edilmiş hatalar; stack trace'ler | Hangi hata, kaç kez, ne zamandır? | Üretimdeki hataları önceliklendirirken |
| **Cloud Trace** | İstek bazlı gecikme, trace/span zaman çizelgesi | İsteğin hangi adımı yavaş? | "Bu istek neden bu kadar sürdü?" sorusuna cevap ararken |
| **Cloud Profiler** | CPU/bellek tüketimi, kaynak koduna atfedilmiş | Hangi kod satırı kaynağı tüketiyor? | "Uygulamam neden bu kadar CPU/bellek harcıyor?" sorusuna cevap ararken |

> **Zihinde tutulacak sıralama mantığı:** Monitoring önce fark eder ("bir şey ters gidiyor") → Error Reporting neyin hata olduğunu özetler ("bu hata 200 kez oldu") → Logging tam bağlamı verir ("bu isteğin tüm detayları buydu") → Trace nerede yavaşladığını gösterir ("bu adım 3 saniye sürdü") → Profiler neden yavaşladığını kod seviyesinde gösterir ("bu fonksiyon CPU'nun %40'ını yiyor"). Gerçek bir üretim sorununu çözerken genelde bu beşini **sırayla** kullanırsın.

---

# Karar Tablosu: Hangi Durumda Hangi Araca Başvurmalıyım?

| Senaryo | Doğru araç |
| --- | --- |
| Uygulamamın genel sağlığını gösteren bir dashboard istiyorum | Cloud Monitoring (custom veya out-of-the-box dashboard) |
| Bir metrik eşiği aşılınca haber almak istiyorum | Cloud Monitoring (alerting policy) |
| Belirli bir log deseni oluştuğu anda bildirim istiyorum | Cloud Logging (log-based alert) |
| Bir olayın kaç kez tekrarlandığını sayıp eşik aşılınca bildirim istiyorum | Cloud Logging (log-based metric) |
| Tekil bir isteğin tüm log detaylarını incelemek istiyorum | Cloud Logging (Logs Explorer) |
| Loglarım üzerinde SQL ile toplu analiz yapmak istiyorum | Cloud Logging (Log Analytics) |
| Compute Engine VM'imdeki üçüncü taraf uygulama (NGINX, Tomcat) loglarını/metriklerini toplamak istiyorum | Ops Agent |
| GKE'deki pod logları pod silinince kaybolmasın istiyorum | GKE observability entegrasyonu + Cloud Logging |
| Kubernetes'te PromQL ile dashboard/uyarı kurmak istiyorum ama Prometheus'u kendim yönetmek istemiyorum | Managed Service for Prometheus (managed collector) |
| Hangi hatanın en sık ve en yeni olduğunu, kaç kullanıcıyı etkilediğini görmek istiyorum | Error Reporting |
| Uygulamam çöktüğünde otomatik e-posta/mobil bildirim istiyorum | Error Reporting (opt-in alerts) |
| Bir isteğin uygulamamın hangi adımında ne kadar sürdüğünü görmek istiyorum | Cloud Trace (Trace Explorer, span detayları) |
| Cloud Run servisimin içindeki (dahili) çağrıların gecikmesini de görmek istiyorum | Cloud Trace + enstrümantasyon (OpenTelemetry) |
| Dünkü performansın geçen haftanın aynı günüyle otomatik karşılaştırmasını istiyorum | Cloud Trace (otomatik günlük rapor) |
| Üretimde hangi fonksiyonun CPU/bellek tükettiğini, uygulamayı yavaşlatmadan görmek istiyorum | Cloud Profiler |
| Test ortamında performans testi yaptım ama üretimdeki gerçek davranışı bilmiyorum | Cloud Profiler (üretimde sürekli çalışır) |

---

# Toplu Özet (Hızlı Tekrar)

**Modülün ana fikri:** Google Cloud Observability, metrikleri, logları ve metadata'yı **tek bir görünümde** birleştiren, Google Cloud/AWS/on-premises/hibrit ortamlarda çalışan bir araç şemsiyesidir. Beş bileşen — Cloud Monitoring, Cloud Logging, Error Reporting, Cloud Trace, Cloud Profiler — birbirini tamamlayan bir zincir oluşturur: Monitoring fark eder, Logging bağlam verir, Error Reporting hataları özetler, Trace nerede yavaşladığını gösterir, Profiler neden yavaşladığını kod seviyesinde gösterir.

**Cloud Monitoring:** Metrikleri, olayları, metadata'yı toplar; alerting policy'ler kurdurur. Üç kullanım biçimi: (1) dashboard'larla trend takibi (DB büyümesi, DAU artışı, özellik kullanımı), (2) acil durum tespiti (metrik eşiklerinde alarm), (3) retrospektif analiz (bir olaydan sonra "aynı anda başka ne oldu"). Her uygulama dashboard'unda bulunması gereken **dört altın sinyal**: latency (başarılı/başarısız ayrı ölçülmeli), traffic (sistem-spesifik: HTTP req/sn veya read-write/sn), errors (açık hata + yanlış içerikli 200 + politika ihlali), saturation (%100'den önce bozulma başlayabilir, hedefleri dikkatli belirle).

**Cloud Logging:** Gerçek zamanlı log yönetimi; depolama, arama, analiz, izleme desteği. **Logs Explorer** tekil kayıtları gösterir; **Log Analytics** SQL ile toplu analiz yapar. **Log-based alert** (anlık desen bildirimi) ile **log-based metric** (sayım/trend/eşik) farklı amaçlara hizmet eder. **Ops Agent**, Compute Engine VM'lerinde Fluent Bit (log) + OpenTelemetry Collector (metrik) ile çalışır; standart konumlardan otomatik log toplar, esnek işleme (parse/modify/exclude) sunar, sıfır konfigürasyonla sistem metrikleri (CPU/disk/bellek/ağ/process) ve küratörlenmiş üçüncü taraf metrikleri (Tomcat, Apache, NGINX) toplar. Cloud Run/functions'ta logging yerleşiktir (stdout/stderr otomatik gider); GKE'de "observability for GKE" ile açılır — **Kubernetes logları kalıcı değildir** (pod silinince container logu gider, sistem logu periyodik temizlenir, cluster event'leri 1 saatte silinir), bu yüzden Cloud Logging'e göndermek şart. **Structured (JSON) logging** önerilir: `textPayload` yerine `jsonPayload`, `severity` alanı log seviyesi verir, `message` ana metni belirler — aranabilirlik ve seviye bazlı filtreleme sağlar.

**Prometheus / Managed Service for Prometheus:** Prometheus, açık kaynak izleme/uyarı araç setidir; zaman serisi verisi toplar, PromQL ile sorgulanır; Kubernetes izlemede popülerdir ama ölçeklemesi zordur. **Google Cloud Managed Service for Prometheus**, bu yükü ortadan kaldırır: çoklu-bulut/cross-project, Cloud Monitoring ile tek bakış açısı, managed collectors (GKE dahil tüm Kubernetes için önerilir, operatör yönetir) veya self-deployed collectors (binary'nin drop-in yerine geçer), Cloud Monitoring/Grafana gibi herhangi bir PromQL API istemcisiyle çalışır, 1.500+ ücretsiz metrik veri göndermeden bile sorgulanabilir, tam bulut-tabanlı alerting pipeline sağlar.

**Error Reporting:** Çalışan servislerdeki çökmeleri sayar, analiz eder, toplar. Merkezi arayüz: sıralama/filtreleme, zamanlama/oluşum/ilk-son görülme/etkilenen kullanıcı detayları, opt-in e-posta/mobil bildirim, saniyeler içinde işlenir. Hatalar **Error Reporting API ile açıkça raporlanır** ya da **log'lardaki stack trace desenlerinden çıkarılır**; stack trace analiziyle **gruplanır ve deduplike edilir**. Etkinleştirme ortama göre değişir: Cloud Run otomatik, GKE `cloud-platform` access scope, Compute Engine VM service account'a Error Reporting Writer rolü. Go/Java/Node.js/PHP/Python/Ruby/.NET desteklenir; client library/REST API/Cloud Logging üzerinden entegre edilir; raporlama **asenkron**dur. Konsolda hatalar sıklığa göre listelenir, Error Details sayfası oluşum trendini ve stack trace'i gösterir, "View Logs" ile Logs Explorer'a geçilir.

**Cloud Trace:** Dağıtık izleme sistemi; gecikme verisini toplar, near-real-time gösterir, URL bazında raporlar. Otomatik günlük rapor, önceki günü geçen haftanın aynı günüyle karşılaştırır (haftalık trafik desenini hesaba katmak için). İki veri gönderme yolu: **otomatik izleme** (Cloud Run giren/çıkan HTTP — servis içi gecikme görünmez) ve **enstrümantasyon** (daha ayrıntılı; OpenTelemetry + Cloud Trace Exporter önerilir, ya da Cloud Trace API/client library). **Trace** = tek işlemin süresi; **span** = trace içindeki alt-işlemin süresi; trace bir veya daha fazla span'dan oluşur. **Trace Explorer**: scatter plot (x=zaman, y=gecikme), method/status code ile filtreleme, bir noktaya tıklayınca Trace Details paneli açılır, span üzerindeki noktalar annotated event'leri gösterir.

**Cloud Profiler:** Üretim performansını anlamak zordur çünkü test ortamı üretimi taklit edemez. İstatistiksel, düşük ek yüklü profiler; tüm üretim örnekleri boyunca CPU kullanımı ve bellek tahsisini sürekli toplar, kaynak koduna atfeder, uygulamayı yavaşlatmadan tam performans resmi sunar. Trace'ten farkı: Trace istek/zaman eksenli, Profiler kod/kaynak eksenlidir.

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Beş aracın rolü:** Monitoring = genel sağlık + trend + alarm. Logging = ham detay + arama/analiz. Error Reporting = hataları grupla + özetle. Trace = istek bazlı "nerede yavaş". Profiler = kod bazlı "hangi fonksiyon kaynak yiyor".
- **Dört altın sinyal:** Latency (başarılı/başarısız **ayrı** ölçülmeli — 500 hatası ortalamayı yanıltabilir), Traffic (sistem-spesifik: HTTP req/sn ya da DB read-write/sn), Errors (açık hata + yanlış içerikli 200 + SLA/politika ihlali de dahil), Saturation (**%100'den önce** bozulma başlayabilir, hedefi dikkatli seç).
- **Log-based alert vs log-based metric:** Alert = belirli bir desen göründüğü **anda** bildirim. Metric = **sayım/trend/eşik** — "kaç kez oldu, eşiği aştı mı" sorusuna cevap.
- **Text log vs structured log:** Text = `textPayload`, log seviyesi yok, aranması zor. Structured (JSON) = `jsonPayload`, `severity` alanı log seviyesi verir, `message` ana metindir, sorgulanabilir. **Structured önerilir.**
- **Ops Agent'in iki motoru:** **Fluent Bit** = log toplama. **OpenTelemetry Collector** = metrik toplama. Sıfır konfigürasyonla CPU/disk/bellek/ağ/process metrikleri + Tomcat/Apache/NGINX gibi küratörlenmiş üçüncü taraf metrikleri toplar.
- **GKE'de log kalıcılığı:** Container logu pod silinince gider; sistem logu periyodik temizlenir; cluster event'leri **1 saatte** silinir. Kalıcılık için Cloud Logging'e göndermek şart.
- **Error Reporting etkinleştirme:** Cloud Run = otomatik. GKE = `cloud-platform` access scope. Compute Engine = VM service account'a **Error Reporting Writer** rolü.
- **Hata tespiti iki yolu:** Error Reporting **API** ile açık raporlama, ya da log'lardaki **stack trace desenlerinden** otomatik çıkarım. İkisi de stack trace analiziyle **gruplanır/deduplike edilir.**
- **Cloud Trace — otomatik izleme vs enstrümantasyon:** Otomatik (Cloud Run) sadece **giren/çıkan HTTP**'yi kapsar, **servis içi** gecikmeyi göstermez. Servis içini görmek için **enstrümantasyon** (OpenTelemetry + Cloud Trace Exporter önerilen) şart.
- **Trace vs span:** Trace = tek işlemin toplam süresi. Span = trace içindeki bir alt-işlemin süresi. Bir trace ≥ 1 span içerir.
- **Cloud Trace vs Cloud Profiler:** Trace = "hangi istek adımı yavaş" (zaman ekseni, istek bazlı). Profiler = "hangi kod fonksiyonu kaynak tüketiyor" (kod ekseni, kaynak bazlı — CPU + bellek).
- **Cloud Profiler'ın doğası:** **İstatistiksel + düşük ek yüklü** — her işlemi izlemez, örnekleme yapar; bu yüzden **üretimde sürekli açık** bırakılabilir, uygulamayı yavaşlatmaz.
- **Prometheus'ta collector seçimi:** Tüm Kubernetes ortamları (GKE dahil) için **managed collectors önerilir** (Kubernetes operatörü yönetir). Self-deployed collectors, normal Prometheus binary'sinin drop-in yerine geçer.
- **Managed Service for Prometheus'un gücü:** Çoklu-bulut + cross-project + Cloud Monitoring'de tek bakış açısı; PromQL API'sini destekleyen her arayüzle (Cloud Monitoring, Grafana) çalışır; 1.500+ ücretsiz metrik veri göndermeden sorgulanabilir; tam bulut-tabanlı alerting pipeline.

---

> **Kapanış:** Bu modül, bir uygulamayı üretime almanın sadece başlangıç olduğunu, gerçek işin **görünürlük kazanmakla** başladığını öğretti. Cloud Monitoring ile "bir şey ters mi gidiyor" sorusunu, dört altın sinyal çerçevesiyle **doğru metriklerle** cevaplamayı; Cloud Logging ile ham detaya inip yapılandırılmış loglamanın neden aranabilirlik kazandırdığını; Prometheus ekosisteminin Google Cloud'da nasıl yönetilen hale geldiğini; Error Reporting ile hata gürültüsünü anlamlı gruplara nasıl indirgediğini; Cloud Trace ile bir isteğin **nerede** yavaşladığını, Cloud Profiler ile de **neden** yavaşladığını (hangi kod satırının kaynağı tükettiğini) kod seviyesinde nasıl göreceğini gördün. Sınav öncesinde "En Kritik Ayrımlar" ve "Toplu Özet" bölümlerini tekrar oku; özellikle dört altın sinyalin incelik noktalarını, log-based alert/metric farkını, Cloud Trace'in otomatik/enstrümante ayrımını ve Trace/Profiler'ın birbirinden nasıl ayrıldığını aklında sağlamca tut. Başarılar!
