# Modül 8 — İzleme ve Performans Ayarı: Pratik Sorular

Bu soru seti Cloud Monitoring, Cloud Logging, Google Cloud Managed Service for Prometheus, Error Reporting, Cloud Trace ve Cloud Profiler konularını kapsıyor.

Sorular, gerçek sınavda insanların gerçekten takıldığı ayrımlara ağırlık veriyor: Four Golden Signals, log-based alert ile log-based metric farkı, structured (yapılandırılmış) log ile text log farkı, otomatik ile enstrümante edilmiş izleme farkı ve Cloud Trace ile Cloud Profiler farkı.

Önce tüm soruları cevaplamayı dene, ardından cevaplarını aşağıdaki [Cevap Anahtarı ve Açıklamalar](#cevap-anahtarı-ve-açıklamalar) bölümüyle karşılaştır.

---

## Sorular

**1.** Bir olay (incident) sırasında ekibinizin dashboard'u, ortalama istek gecikmesinin (latency) keskin biçimde düştüğünü gösteriyor. İnceleme sonucunda, arka uçtaki bir veritabanı bağlantısının doğrudan reddedildiği ve isteklerin büyük bir kısmının anında HTTP 500 yanıtıyla başarısız olduğu ortaya çıkıyor. Bir takım arkadaşınız, uygulamanın olay sırasında aslında daha hızlı çalıştığı sonucuna varıyor. Buradaki asıl sorun nedir?

A. Hiçbir sorun yok — bir olay sırasında ortalama gecikmenin düşmesi zaten beklenen bir durumdur.
B. Dashboard, başarılı ve başarısız isteklerin gecikmesini tek bir ortalamada birleştiriyor ve bu, asıl sorunu gizliyor. Gecikme, başarılı ve başarısız istekler için **ayrı ayrı** ölçülmelidir.
C. Ekip, olaylar sırasında gecikme yerine doygunluğu (saturation) grafiklemelidir.
D. Ekip, olaylar sırasında gecikme yerine trafiği (traffic) grafiklemelidir.

**2.** Bir ödeme API'si her isteğe HTTP 200 döndürüyor, ancak bir hata nedeniyle "başarılı" yanıtların %0,1'i yanlış para birimi toplamı bildiriyor. Ayrı olarak, servisin bir saniyeden kısa yanıt süresi vaat eden bir SLA'sı var ve isteklerin %2'si 1,5 saniye sürüyor. Bir takım arkadaşınız "5xx durum kodu olmadığı için sıfır hatamız var" diyor. Bu doğru mu?

A. Doğru — hata yalnızca sunucu açık bir HTTP hata durum kodu döndürdüğünde vardır.
B. Yanlış — hatalar; açık hata kodlarını, yanlış içerikli başarılı görünen yanıtları ve vaat edilen bir politikanın (örneğin gecikme SLA'sının) ihlalini de kapsar.
C. Yanlış — yalnızca yanlış içerikli yanıtlar hata sayılır; SLA ihlallerinin errors sinyaliyle bir ilgisi yoktur, ayrı bir konudur.
D. Doğru, ama sadece para birimi hatasının errors golden signal'i yerine Error Reporting'e ait olması nedeniyle.

**3.** Nöbet ekibiniz, doygunluk (saturation) uyarısını yalnızca CPU kullanımı %100'e ulaştığında tetiklenecek şekilde ayarlıyor. CPU kullanımı hâlâ yaklaşık %70 civarındayken kullanıcılar yanıt sürelerinin bozulduğunu bildirmeye başlıyor. Doğru yorum nedir?

A. Bu, Cloud Monitoring'de bir hata olduğunu gösterir — CPU metrikleri kullanımı olduğundan düşük gösteriyor.
B. Sistemler, %100 kullanıma ulaşmadan çok önce performans olarak bozulmaya başlayabilir; bu yüzden kullanım hedefleri, %100 tavanına göre değil, sistemin **gerçek davranışına** göre dikkatli biçimde belirlenmelidir.
C. Doygunluk yalnızca bellek kullanımıyla ölçülmelidir, asla CPU ile değil.
D. Bu, doygunluk yerine trafiğin uyarı kurulması gereken golden signal olduğu anlamına gelir.

**4.** Web önyüzü dashboard'unuz trafiği saniyedeki HTTP istek sayısı olarak ölçüyor. Bir takım arkadaşınız, "trafik" her yerde aynı anlama gelsin diye aynı metrik tanımını bir NoSQL veritabanı dashboard'unda da kullanmak istiyor. Ona ne söylemelisiniz?

A. Tutarlılık için saniyedeki HTTP istek sayısı metriğini tüm dashboard'larda aynen kullanın.
B. Trafik, sistem-spesifik bir metrikle ölçülür — bir NoSQL veritabanı için bu, saniyedeki HTTP isteği değil, saniyedeki okuma veya yazma işlemi sayısıdır.
C. Trafik veritabanları için anlamlı olmadığından, trafik yerine doygunluk kullanın.
D. Veritabanı dashboard'unda trafik yerine CPU kullanımını vekil (proxy) olarak kullanın.

**5.** Loglarınızda belirli bir kritik hata mesajı (örneğin `OutOfMemoryError: heap space exhausted`) göründüğü **anda** bildirim almak istiyorsunuz. Hangi Cloud Logging özelliğini yapılandırmalısınız?

A. Sayım eşiği 1 olan bir log-based metric.
B. Log-based alert, çünkü eşleşen bir log deseni oluştuğu anda bir bildirim tetikler.
C. Error Reporting Writer IAM rolü.
D. Bir Cloud Monitoring uptime check.

**6.** Belirli bir uyarı mesajının zaman içinde kaç kez oluştuğunu izlemek, yalnızca sayım belirli bir saat içinde bir eşiği aştığında bildirim almak ve haftalar boyunca uzun vadeli trendi de gözlemlemek istiyorsunuz. Bu ihtiyaca hangi Cloud Logging özelliği uyar?

A. Log-based alert.
B. Log-based metric, çünkü zaman içindeki oluşumları saymak, trendleri gözlemlemek ve bir eşik aşıldığında uyarı vermek için tasarlanmıştır.
C. Bir Cloud Trace özel analiz raporu.
D. Error Reporting'in gruplanmış hata görünümü.

**7.** Üretim servisiniz şu anda stdout'a düz metin satırları yazıyor. Ekip, logları yalnızca `ERROR` seviyesindeki kayıtları gösterecek şekilde filtrelemek ve özel bir `component` alanına göre güvenilir biçimde arama yapmak istiyor. Ne değiştirmelisiniz?

A. Hiçbir şey — text logging zaten `textPayload` alanı üzerinden severity filtrelemeyi destekler.
B. Structured (JSON) logging'e geçin: `severity` alanı ve özel bir `component` alanı içeren, tek satırlık serileştirilmiş bir JSON nesnesi yayınlayın. Bunlar `jsonPayload`'a yerleşir ve sorgulanabilir hale gelir.
C. Log Analytics'i açın — hiçbir format değişikliği yapmadan yapılandırılmamış metin satırlarından severity seviyelerini otomatik olarak çıkaracaktır.
D. Logları Error Reporting üzerinden yönlendirin, çünkü severity seviyelerini sizin için o yönetir.

**8.** NGINX çalıştıran bir Compute Engine VM'ine Ops Agent kuruyorsunuz ve hem uygulama loglarını hem de küratörlenmiş NGINX metriklerini toplamasını istiyorsunuz. Bu iki işten gerçekte hangi alt bileşenler sorumludur?

A. Fluent Bit adında tek, birleşik bir binary hem logları hem metrikleri işler.
B. Fluent Bit logları toplar, OpenTelemetry Collector metrikleri toplar.
C. Fluent Bit metrikleri toplar, OpenTelemetry Collector logları toplar.
D. Ops Agent'ın iç alt bileşenleri yoktur — tek, monolitik bir toplayıcıdır.

**9.** Bir GKE pod'u crash-loop'a girdi ve cluster tarafından otomatik olarak yeniden oluşturuldu. Bir saat sonra bir SRE, ne olduğunu anlamak için o pod'la ilgili cluster olaylarını (events) kontrol etmek istiyor, ama olaylar zaten kaybolmuş. Bu neden oldu ve bunu önlemek için ne yapılmalıydı?

A. Cluster olayları GKE tarafından süresiz olarak saklanır; SRE'nin sadece konsolda farklı bir sayfaya bakması gerekiyor.
B. GKE bu veriyi süresiz olarak saklamaz: container logları pod kaldırıldığında kaybolur, sistem logları periyodik olarak temizlenir ve cluster olayları bir saat sonra kaldırılır. Logları Cloud Logging'e göndermek, bunları ihtiyaç duyulan süre kadar korur.
C. Bu yalnızca cluster'da Cloud Trace etkin değilse olur.
D. Cluster olayları Cloud Logging yerine Cloud Profiler'da saklanır, bu yüzden SRE yanlış araca bakıyordu.

**10.** Ekibiniz Kubernetes iş yüklerini GKE'ye taşıyor. Zaten PromQL biliyorlar ve buna dayalı dashboard'lar ile uyarılar kurmak istiyorlar, ama açıkça bir Prometheus dağıtımını kendileri işletip ölçeklendirmek istemiyorlar. Ne kullanmalılar?

A. Self-deployed collectors, çünkü standart Prometheus binary'sinin doğrudan yerine geçen (drop-in) bir çözümdür.
B. Managed collectors — GKE dahil tüm Kubernetes ortamları için önerilir, çünkü Prometheus'un işletilmesini sizin için Kubernetes operatörü halleder.
C. Ops Agent, çünkü Prometheus ihtiyacını tamamen ortadan kaldırır.
D. Cloud Trace, çünkü o da PromQL uyumlu bir sorgu arayüzü sunar.

**11.** Eski (legacy) bir servis Error Reporting API'siyle hiç entegre edilmemiş, ama işlenmeyen bir istisna (unhandled exception) oluştuğunda tam stack trace'leri stdout'a logluyor. Error Reporting yine de bu hataları yakalayacak mı?

A. Hayır — Error Reporting yalnızca kendi API'si üzerinden açıkça raporlanan hataları tespit eder.
B. Evet — Error Reporting, açık API çağrıları gerektirmeden, log kayıtlarını stack trace gibi yaygın metin desenleri için tarayarak da hataları çıkarım yoluyla tespit edebilir.
C. Hayır — Error Reporting'in bunları kullanabilmesi için stack trace'lerin önce Cloud Trace'e gönderilmesi gerekir.
D. Evet, ama yalnızca servis Cloud Profiler'ı da etkinleştirmişse.

**12.** Ekibiniz üç servis çalıştırıyor — biri Cloud Run'da, biri GKE'de, biri Compute Engine'de — ve üçünde de Error Reporting'i etkinleştirmek istiyor. Her birinde etkinleştirme açısından ne farklı?

A. Üçü de tamamen aynı manuel IAM rolü atamasını gerektirir.
B. Cloud Run'da otomatik olarak etkindir; GKE, cluster'a `cloud-platform` erişim kapsamının eklenmesini gerektirir; Compute Engine, VM'in Service Account'ına Error Reporting Writer rolünün verilmesini gerektirir.
C. Error Reporting yalnızca Cloud Run'da çalışır — GKE ve Compute Engine bunun yerine Cloud Trace'e güvenmelidir.
D. Üçü de hiçbir yapılandırma gerektirmeden otomatik olarak etkindir.

**13.** Bir Cloud Run servisi, gelen HTTP istekleri için trace verisi gösteriyor, ama bu istekleri işlerken yapılan dahili bir veritabanı sorgusunun gecikmesi trace zaman çizelgesinde tamamen görünmüyor. Neden, ve ekip bunun için ne yapmalı?

A. Cloud Trace zaten tüm dahili çağrıları otomatik olarak yakalar; sorgu span'ı konsolda görünmesi için sadece biraz zaman alıyor.
B. Cloud Run'daki otomatik izleme yalnızca gelen ve giden HTTP isteklerinin zamanlamasını yakalar, servis içindeki gecikmeyi değil. Veritabanı sorgusu gibi dahili span'ları görmek için uygulamayı enstrümante etmek gerekir — örneğin OpenTelemetry ve Cloud Trace Exporter ile.
C. Bunun yerine Cloud Profiler etkinleştirilmelidir; veritabanı sorgu gecikmesini gösterebilecek tek araç odur.
D. Veritabanı çağrısının gecikmesini ölçmek için Cloud Logging log-based metric'e geçin.

**14.** Servisinize gelen bir istek, yanıt vermeden önce arka uçtaki bir servise bir RPC çağrısı ve ardından bir veritabanı sorgusu tetikliyor. Cloud Trace terminolojisiyle bu nasıl tanımlanmalı?

A. Her RPC çağrısı ve her veritabanı sorgusu kendi başına ayrı bir trace'tir; genel isteğin kendine ait bir adı yoktur.
B. Baştan sona istek trace'tir; RPC çağrısı veya veritabanı sorgusu gibi her alt-işlem bir span'dır. Bir trace, bir ya da daha fazla span'dan oluşur.
C. Span, toplam istek süresidir; trace ise bunun içindeki tek bir alt-işlemdir.
D. Trace ve span aynı kavramın Trace Explorer'da farklı yakınlaştırma seviyelerinde gösterilen iki adıdır.

**15.** Bir mühendis, üretimde kod tabanındaki hangi fonksiyonun en çok CPU tükettiğini, uygulamayı gözle görülür biçimde yavaşlatmadan, sürekli olarak bilmek istiyor. Hangi araç buna uyar ve neden?

A. Cloud Trace, çünkü neredeyse gerçek zamanlı (near-real-time) performans içgörüleri sağlar.
B. Cloud Profiler, çünkü her tek işlemi izlemek yerine düşük ek yüklü istatistiksel örnekleme kullanarak, tüm üretim örnekleri boyunca CPU ve bellek kullanımını sürekli olarak kaynak koduna atfeder.
C. Log Analytics, CPU ile ilgili log kayıtlarına karşı bir SQL sorgusu yazarak.
D. Error Reporting, çünkü CPU sıçramaları orada hata olarak gösterilir.

---

## Cevap Anahtarı ve Açıklamalar

**1. Doğru cevap: B.**
Bu senaryo, modülün işaret ettiği tam sınav tuzağıdır: bir bağlantı reddi hızlıca başarısız olur, bu yüzden bu başarısız isteğin gecikmesini başarılı isteklerle aynı ortalamaya katmak, ortalamayı yapay olarak düşürür ve servisin aslında ne kadar "sağlıklı-yavaş" olduğunu gizler. Çözüm her zaman başarılı ve başarısız istek gecikmesini ayrı ölçmektir, gecikmeyi başka bir sinyalle değiştirmek değil (C, D).

**2. Doğru cevap: B.**
Errors golden signal'i HTTP durum kodlarından daha geniştir: açık hataları (5xx), yanlış içerikli başarılı görünen yanıtları (para birimi hatası) ve politika/SLA ihlallerini (1,5 saniyelik yanıtların bir saniyeden kısa vaadini ihlal etmesi) kapsar. Cazip yanıltıcı seçenek A/D'dir, çünkü "5xx yok" ifadesinin "hata yok" anlamına geldiğini varsayar — modülün açıkça uyardığı yanılgı tam olarak budur.

**3. Doğru cevap: B.**
Doygunluk, bir sistemin ne kadar "dolu" olduğuyla ilgilidir ve modül, sistemlerin %100 kullanıma ulaşmadan çok önce bozulmaya başlayabildiğini vurgular — örneğin kuyruklama gecikmesi CPU çok daha düşük bir eşiği geçtiğinde sıçrayabilir. Buradaki ders, kullanım hedeflerini gerçek gözlemlenen davranışa göre kalibre etmek, %100'ün anlamlı bir sınır olduğunu varsaymamaktır (bu yüzden B doğru, A/C/D yanlıştır).

**4. Doğru cevap: B.**
Trafik açıkça sistem-spesifik bir metrikle ölçülür: saniyedeki HTTP isteği bir web sunucusu için mantıklıdır, ama bir NoSQL veritabanının trafiği doğal olarak saniyedeki okuma veya yazma sayısıyla ölçülür. Her sisteme tek bir evrensel trafik tanımını dayatmak (A) tuzaktır; C ve D ise tamamen yanlış bir golden signal'i devreye sokar.

**5. Doğru cevap: B.**
"Bu desen göründüğü anda beni haberdar et" ifadesi, log-based alert'in tanımlayıcı kullanım senaryosudur — belirli bir log desenine bağlı, anlık, olay tabanlı bir bildirimdir. Log-based metric (A) zaman içinde sayım/trend için tasarlanmıştır, anlık tekil olay bildirimi için değil; bu yüzden burada cazip ama yanlış seçenektir.

**6. Doğru cevap: B.**
Oluşumları saymak, bir eşik aşıldığında uyarı vermek ve uzun vadeli trendleri gözlemlemek, tam olarak log-based metric'lerin tasarlandığı amaçtır. Log-based alert (A) cazip bir yanıltıcıdır çünkü o da "bildirim almayı" içerir, ama o anlık desen eşleşmeleri için tasarlanmıştır, sayım/trend analizi için değil.

**7. Doğru cevap: B.**
Text logların bir `severity` alanı yoktur ve güvenilir biçimde aranması zordur; structured (JSON) loglar veriyi `jsonPayload`'a yerleştirir, `severity`'yi açıkça ayarlamana izin verir ve `component` gibi özel alanları sorgulanabilir hale getirir. Log Analytics (C) cazip bir yanıltıcıdır çünkü loglar üzerinde SQL çalıştırmanı gerçekten sağlar, ama ham yapılandırılmamış metinden severity seviyeleri ya da yapılandırılmış alanlar icat edemez — formatın kaynakta değişmesi gerekir.

**8. Doğru cevap: B.**
Ops Agent tek bir monolitik araç değildir — iki özelleşmiş açık kaynak bileşenden oluşur: Fluent Bit yüksek verimli log toplamayı, OpenTelemetry Collector ise metrik toplamayı üstlenir. Bunları karıştırmak (C) ya da tek bir binary'nin her şeyi yaptığını varsaymak (A, D) modülün işaret ettiği tam tuzaktır.

**9. Doğru cevap: B.**
GKE, logları ve olayları süresiz olarak saklamaz: container logları pod kaldırıldığında kaybolur, sistem logları periyodik olarak temizlenir ve cluster olayları özellikle bir saat sonra kaldırılır. Her şeyi Cloud Logging'e göndermek, tam da bu tür bir olay-sonrası (post-incident) inceleme için ihtiyaç duyacağın kanıtı korur — modül bunu, GKE observability entegrasyonunun Cloud Logging ile neden önemli olduğunun doğrudan nedeni olarak sunuyor.

**10. Doğru cevap: B.**
GKE dahil tüm Kubernetes ortamları için managed collectors önerilir, çünkü Prometheus'un işletilmesini Kubernetes operatörü senin için halleder — manuel ölçekleme ya da bir Prometheus dağıtımını işletme derdi yoktur. Self-deployed collectors (A) Prometheus binary'sinin geçerli bir drop-in yerine geçenidir, ama daha çok Kubernetes dışı senaryolarda ya da daha sıkı manuel kontrol gerektiğinde önemlidir — bu yüzden burada cazip ama yanlış seçimdir.

**11. Doğru cevap: B.**
Error Reporting, hataları iki yoldan tespit eder: kendi API'si üzerinden açık raporlama, ve stack trace gibi yaygın metin desenleriyle eşleşen log kayıtlarından çıkarım. İkinci yolun çalışması için servisin herhangi bir özel kod değişikliğine ihtiyacı yoktur — stdout'a normal stack-trace loglaması yeterlidir; bu da A'yı "API'yi entegre etmek zorundasın" tuzağı haline getirir.

**12. Doğru cevap: B.**
Error Reporting'i etkinleştirmek, compute seçeneğinin sana verdiği operasyonel kontrol miktarına göre ölçeklenir: Cloud Run otomatiktir, GKE cluster oluşturulurken `cloud-platform` erişim kapsamının eklenmesini gerektirir, Compute Engine ise VM'in Service Account'ına Error Reporting Writer rolünün verilmesini gerektirir. Üçünde de tek tip bir kurulum adımı olduğunu varsaymak (A, D) tuzaktır.

**13. Doğru cevap: B.**
Cloud Run'daki otomatik izleme yalnızca gelen ve giden HTTP istek sınırını kapsar — servis içinde, örneğin bir veritabanı çağrısında ne olduğunu görmez. Bu dahili gecikmeyi yakalamak için uygulamanın enstrümante edilmesi gerekir; önerilen yaklaşım OpenTelemetry artı Cloud Trace Exporter'dır. "Cloud Run demek tamamen otomatik, eksiksiz izleme demektir" varsayımı (A) modülün tam olarak uyardığı sınav tuzağıdır.

**14. Doğru cevap: B.**
Trace, tek bir genel işlemin tamamlanmasını tanımlar; span ise o trace içindeki bir alt-işlemin tamamlanmasını tanımlar — bir trace her zaman bir ya da daha fazla span içerir. Tanımları ters çevirmek (C) ya da her alt-işlemi kendi başına ilişkisiz bir trace gibi ele almak (A) yaygın karıştırmalardır.

**15. Doğru cevap: B.**
Cloud Profiler tam olarak bunun için tasarlanmıştır: tüm üretim örnekleri boyunca CPU ve bellek kullanımını sürekli olarak örnekler ve bunu kaynak koduna atfeder; istatistiksel, düşük ek yüklü teknikler kullandığı için uygulamayı gözle görülür biçimde yavaşlatmadan üretimde çalışabilir. Cloud Trace (A) cazip bir yanıltıcıdır çünkü o da "performans" ile ilgilidir, ama farklı bir soruyu cevaplar — hangi kod fonksiyonunun kaynak tükettiğini değil, hangi istek adımının yavaş olduğunu.
