# Alıştırma Soruları — Modül 7: Uygulaman İçin Compute Seçenekleri

**Uygulaman İçin Compute Seçenekleri** modülü (Compute Engine, GKE, Cloud Run, App Engine) için senaryo tabanlı alıştırma soruları. Bu sorular, [deep dive](../../../deep-dive/07-compute-options-for-your-application/compute-options-for-your-application.md) içindeki sınav tuzaklarına ve karar tablosu ayrımlarına ağırlık verir — önce onu okumadıysan, önce onu oku.

Önce tüm soruları cevapla, sonra cevaplarını aşağıdaki **Cevap Anahtarı ve Açıklamalar** bölümüyle karşılaştır.

---

## Sorular

**1.** Şirketin, on-premises veri merkezini kapatıyor. Taşıman gereken uygulamalardan biri, belirli bir sanal donanım yapılandırmasına bağlı ticari bir veritabanı lisansına sahip, on yıllık bir envanter yönetim sistemi. Yönetim, göçün minimum kod değişikliğiyle ve mimariyi yeniden tasarlamadan tamamlanmasını istiyor. Hangi compute seçeneğini kullanmalısın?

A. Cloud Run, çünkü sunucularla uğraşmadan herhangi bir container'ı dağıtmana izin verir
B. GKE Autopilot, çünkü Google altta yatan altyapıyı senin yerine yönetir
C. Compute Engine, çünkü mevcut VM ve işletim sistemi yapılandırmasını, donanıma bağlı lisanslama üzerinde tam kontrolle yeniden oluşturmana izin verir
D. App Engine flexible environment, çünkü özel çalışma zamanlarını (runtime) destekler

**2.** Bir platform ekibi, Kubernetes üzerinde konteynerize iş yükleri çalıştırmak istiyor, ancak node pool'ları ya da node'ları hiç provizyonlamak, yamalamak ya da yönetmek istemediklerini açıkça belirtti — sadece iş yüklerini tanımlamak ve geri kalan her şeyi, altyapı sıkılaştırması dahil, Google'ın halletmesini istiyorlar. Hangi GKE modunu seçmeliler?

A. GKE Standard, çünkü en fazla yapılandırma esnekliğini sunar
B. GKE Autopilot, çünkü Google control plane'i, node'ları ve node pool'larını tamamen yönetir
C. Kendi kurdukları bir Kubernetes dağıtımını çalıştıran Compute Engine managed instance group'ları
D. Node otomatik provizyonlaması etkinleştirilmiş GKE Standard

**3.** Bir GKE cluster'ı içinde kendi yönettiğin bir ilişkisel veritabanı dağıtıyorsun. Her replica'nın kararlı bir ağ kimliğine ve pod yeniden zamanlamasından (rescheduling) etkilenmeyen kendi persistent volume'una ihtiyacı var. Bu iş yükünü tanımlamak için hangi Kubernetes kaynağını kullanmalısın?

A. StatefulSet, çünkü kararlı kimlik ve replica başına kalıcı depolama sağlar
B. Deployment, çünkü sabit sayıda replica'nın çalışır durumda kalmasını sağlar
C. Bir Kubernetes Service, çünkü pod'ları ağ üzerinden ifşa eder
D. Bir managed instance group, çünkü persistent disk'leri otomatik olarak halleder

**4.** Bir grup kaydı okuyan, bunları birden çok bağımsız paralel görev arasında işleyen, başarısız olan her görevi yeniden deneyen ve bittiğinde çıkış yapan gece çalışan bir uzlaştırma (reconciliation) süreci çalıştırman gerekiyor. Gelen HTTP isteklerini kabul etmesine gerek yok. Ne kullanmalısın?

A. Cloud Scheduler tarafından çağrılan bir Cloud Run service'i
B. Boştayken sıfır replica'ya ölçeklenen bir GKE Deployment
C. Cron job içeren bir Compute Engine preemptible VM'i
D. Cloud Scheduler ile bir zamanlamada tetiklenen bir Cloud Run job'ı

**5.** İstemcilerden gelen HTTP isteklerine gerçek zamanlı yanıt vermesi, trafik zirveleri geldiğinde otomatik olarak yukarı ölçeklenmesi ve trafik olmadığında sıfıra ölçeklenip — ve ücretlendirmenin durması gereken — genel amaçlı bir web API'si inşa ediyorsun. Neyi dağıtmalısın?

A. Cloud Run job
B. GKE StatefulSet
C. Cloud Run service
D. Otomatik ölçeklendirmeli bir Compute Engine managed instance group

**6.** Güvenlik ekibin, container imajının nasıl bir araya getirildiği üzerinde — tam taban imaj, her katman ve eklenen her dosya — üretime çalışmadan önce tam kontrol istiyor. Bu gereksinimi hangi Cloud Run dağıtım seçeneği karşılar?

A. Google Cloud buildpack'lerini kullanan kaynak kod tabanlı dağıtım
B. Kendi önceden build edilmiş imajını sağladığın container tabanlı dağıtım
C. Bir GitHub deposundan sürekli dağıtım
D. Cloud Run functions, çünkü imajı otomatik olarak üretir

**7.** Bir geliştirici, tek bir `gcloud run deploy` komutuyla, bir Dockerfile yazmadan ya da container imajının nasıl build edildiğiyle uğraşmadan, küçük bir Python HTTP servisini Cloud Run'a dağıtmak istiyor. Ne kullanmalı?

A. Google Cloud buildpack'lerinin imajı build etmesine izin veren kaynak kod tabanlı dağıtım
B. Elle build edilmiş bir imajla container tabanlı dağıtım
C. GKE Autopilot, çünkü o da imajları otomatik olarak build eder
D. Cloud Run jobs, çünkü bir container imajı gerektirmez

**8.** Bir iş arkadaşın, Cloud Run functions'ın, Cloud Run'dan ayrı, kendi altyapı modeline sahip özel bir compute ürünü üzerinde çalıştığını iddia ediyor. Bu doğru mu?

A. Evet, Cloud Run functions, Cloud Functions altyapısı üzerinde çalışır — tamamen ayrı bir üründür
B. Evet, fonksiyonlar sadece App Engine standard environment'ında çalışır
C. Hayır, Cloud Run functions özel bir GKE cluster'ı gerektirir
D. Hayır, Cloud Run functions arka planda Cloud Run service'leri olarak dağıtılır

**9.** Veri bilimi ekibin, Compute Engine üzerinde birkaç terabaytlık veriyi yeniden işleyen büyük bir gece batch işi çalıştırıyor. İş, kesintiye uğrayıp yeniden başlatılabiliyor ve ekibin en büyük önceliği compute maliyetini minimize etmek. Ne yapılandırmalısın?

A. Committed use discount'lu standart Compute Engine VM'leri
B. Dikey pod otomatik ölçeklendirmeli GKE Autopilot
C. Standart fiyatlandırmaya göre en az %60 indirim sunan ama Google tarafından sonlandırılabilen Preemptible (Spot) VM'ler
D. Maksimum eşzamanlılığı bire ayarlanmış Cloud Run jobs

**10.** Bir şirket, 7/24 sabit ve yüksek derecede öngörülebilir trafiğe sahip bir servis işletiyor ve mümkün olan en öngörülebilir aylık faturayı istiyor. Bu fiyatlandırma ihtiyacına en uygun compute yaklaşımı hangisi?

A. Cloud Run, çünkü yalnızca kullanılanı ücretlendirir
B. Öngörülebilir fiyatlandırmalı ayrılmış (dedicated) VM kapasitesi kullanan Compute Engine ya da GKE
C. Cloud Run functions, çünkü sıfıra ölçeklenir
D. Maksimum performans için GPU destekli Cloud Run

**11.** Ekibin, büyük bir dil modelini çıkarım (inference) için sunmak istiyor. Trafik öngörülemez, ve hiç sunucu yönetmeden ya da önceden GPU kapasitesi rezerve etmeden, istek olmadığında platformun sıfıra ölçeklenmesini — ve GPU süresi için ödeme yapmayı durdurmasını — istiyorsun. Ne kullanmalısın?

A. GPU destekli Cloud Run
B. GPU'nun doğrudan bağlandığı bir Compute Engine VM'i
C. GPU etkin bir node pool'a sahip GKE Standard
D. Sabit boyutlu bir GPU node pool'una sahip GKE Autopilot

**12.** HTTP ya da gRPC değil, özel bir TCP protokolü üzerinden iletişim kuran ve hem on-premises bir veri merkezinde hem de Google Cloud'da tutarlı biçimde çalışması gereken konteynerize bir uygulamayı taşıyorsun. Her iki gereksinime de hangi platform uyar?

A. Cloud Run, çünkü herhangi bir container'ı çalıştırabilir
B. App Engine flexible environment
C. Özel bir tetikleyiciye sahip Cloud Run functions
D. Google Kubernetes Engine (GKE)

**13.** Bir ekip, sıfırdan yepyeni bir serverless mikroservis başlatıyor ve App Engine ile Cloud Run arasında karar veriyor. Göz önünde bulundurulması gereken mevcut bir App Engine yatırımı yok. Hangisini seçmeliler ve neden?

A. App Engine, çünkü daha uzun süredir var ve daha olgun
B. Google, yeni projeler için ikisini de eşit derecede önerir
C. Cloud Run, çünkü yeni serverless servisler için önerilen platform — daha fazla esneklik ve daha hızlı ölçekleme sunuyor
D. App Engine flexible environment, çünkü özel container'ları destekler

**14.** Bir startup, Cloud Run ile başlamaktan çekiniyor; ileride gereksinimleri büyürse, kilitlenip (locked in) uygulamayı daha fazla altyapı kontrolü sunan bir platforma taşımak için yeniden yazmak zorunda kalacaklarından endişe ediyorlar. Bu endişe haklı mı?

A. Evet, Cloud Run'dan taşınmak her zaman tam bir yeniden yazımı gerektirir
B. Hayır, uygulama Cloud Client Library'ler kullanılarak yazılmış ve container'laştırılmışsa, genellikle yeniden yazmaya gerek kalmadan platformlar arasında taşınabilir
C. Evet, ama sadece uygulama birden fazla Google Cloud servisi kullanıyorsa
D. Hayır, ama sadece App Engine uygulamaları taşınabilir — Cloud Run uygulamaları değil

**15.** Bir mühendislik organizasyonu tamamen uygulama geliştiricilerinden oluşuyor, özel bir operasyon ya da platform ekibi yok. Birkaç stateless konteynerize servis dağıtmaları gerekiyor ve düşünmek zorunda kaldıkları altyapı miktarını minimize etmek istiyorlar. Ekip yapılarına en uygun platform hangisi?

A. Cloud Run, çünkü hiçbir altyapı ya da node yönetimi gerektirmez
B. Maksimum yapılandırma esnekliği için GKE Standard
C. Ortam üzerinde tam kontrol için Compute Engine
D. GKE Autopilot, çünkü yine de Kubernetes kaynaklarını ve YAML manifestlerini yönetmeyi gerektirir

---

## Cevap Anahtarı ve Açıklamalar

**1. Doğru cevap: C** — Compute Engine.
Donanıma/lisansa bağlı yazılımla yapılan lift-and-shift göçleri, klasik Compute Engine sinyalidir — mevcut VM ve işletim sistemi yapılandırmasını sıfır kod değişikliğiyle yeniden oluşturabilirsin. Cloud Run ve GKE Autopilot, operasyonel efor'u minimize ettikleri için cazip görünür, ama ikisi de önce container'laştırma ve yeniden mimarilendirme yapmadan donanıma özgü lisanslı yazılımı çalıştırmana izin vermez — bu da senaryonun açıkça hariç tuttuğu bir şey.

**2. Doğru cevap: B** — GKE Autopilot.
"Node'ları hiç yönetmek istemiyorum" ifadesi doğrudan Autopilot sinyalidir — GKE Standard'da Google yalnızca control plane'i yönetir, node pool provizyonlaması, yamalanması ve yaşam döngüsü varsayılan olarak senin sorumluluğunda kalır. D seçeneği klasik tuzaktır: node otomatik provizyonlamasını etkinleştirmek yine seni Standard'ın paylaşılan sorumluluk modelinde bırakır, Autopilot'un tam yönetilen modelinde değil.

**3. Doğru cevap: A** — StatefulSet.
Kararlı ağ kimliği ile replica başına kalıcı depolama, tam olarak StatefulSet'in sağladığı şeydir; Deployment ise herhangi bir pod'un herhangi bir isteğe cevap verebildiği, birbirinin yerine geçebilen stateless replica'lar için tasarlanmıştır. Deployment cazip ama yanlış cevaptır çünkü daha yaygın, varsayılan iş yükü tipidir — ama senaryonun stateful gereksinimleri onu açıkça eler.

**4. Doğru cevap: D** — Cloud Run job.
Bağımsız olarak yeniden denenebilen paralel görevlerle "çalışır, işini yapar, çıkış yapar" tanımı, tam olarak bir Cloud Run job'ının tanımıdır — bir Cloud Run service'inin aksine, hiçbir zaman bir portu dinlemez. A seçeneği cazip bir çeldiricidir çünkü Cloud Run service'leri de teknik olarak bir zamanlamayla çağrılabilir, ama bir servis HTTP isteklerini dinlemek üzerine kuruludur, bir batch görevini tamamlanana kadar çalıştırmak üzerine değil.

**5. Doğru cevap: C** — Cloud Run service.
HTTP isteklerini sürekli dinlemek, otomatik yukarı ölçekleme ve sıfıra ölçekleme, tam olarak bir Cloud Run service'inin yaptığı şeydir. Cloud Run job burada cazip ama yanlış seçenektir çünkü Cloud Run'ın "diğer" modudur, ama job'lar hiçbir zaman HTTP trafiğini dinlemez — tamamlanana kadar çalışıp çıkış yaparlar.

**6. Doğru cevap: B** — Container tabanlı dağıtım.
Taban imaj ve eklenen her dosya üzerinde tam kontrol, yalnızca container imajını kendin build edip sağladığında garanti edilir; buildpack'ler (kaynak kod tabanlı dağıtım) bu detayları kasıtlı olarak otomatik, güvenli bir varsayılan build'in arkasında gizler. Kaynak kod tabanlı dağıtım cazip bir çeldiricidir çünkü o da geçerli bir Cloud Run yoludur, ama kontrolü kolaylık karşılığında feda eder — bu senaryonun istediğinin tam tersi.

**7. Doğru cevap: A** — Kaynak kod tabanlı dağıtım (buildpack'ler).
Dockerfile olmadan doğrudan kaynak koddan dağıtım yapmak, tam olarak buildpack tabanlı kaynak kod dağıtımının amacıdır — Cloud Build ve buildpack'ler dili otomatik algılar (Python destekleniyor) ve üretime hazır bir imaj üretir. B seçeneği cazip ama yanlış cevaptır çünkü o da geçerli bir Cloud Run dağıtım yoludur, ama geliştiricinin imajı kendisinin build edip yönetmesini gerektirir — senaryonun açıkça kaçınmak istediği şey.

**8. Doğru cevap: D** — Hayır, Cloud Run service'leri olarak dağıtılırlar.
Fonksiyonlar, Cloud Run'ın üzerine kurulu bir dağıtım seçeneğidir — ayrı bir altyapı ürünü olarak değil, Cloud Run service'leri olarak dağıtılır ve çalışırlar. Bu, sık karşılaşılan bir kafa karışıklığı noktasıdır çünkü fonksiyonlar kavramsal olarak (tek amaçlı, olay tetiklemeli) tipik bir uzun süre çalışan servisten farklı hissettirir, ama altta yatan çalışma zamanı aynıdır.

**9. Doğru cevap: C** — Preemptible (Spot) VM'ler.
Kesintiye toleranslı büyük bir batch işi, preemptible VM'lerin tam olarak tasarlandığı kullanım durumudur — Google'ın kapasiteyi istediği zaman geri alabilmesi karşılığında, standart VM'lere göre en az %60 daha ucuzdur. Committed use discount'lar, uzun süre çalıştırmayı planladığın sabit, öngörülebilir iş yükleri için faydalıdır, tek seferlik batch işleri için değil — bu yüzden bu spesifik senaryo için cazip ama yanlış cevaptırlar.

**10. Doğru cevap: B** — Ayrılmış VM kapasiteli Compute Engine ya da GKE.
Sabit, öngörülebilir, 7/24 trafik, ayrılmış (dedicated) VM kapasitesiyle (Compute Engine/GKE) en iyi şekilde eşleşir — bu da tutarlı, tahmin edilebilir bir faturalandırma sağlar. Cloud Run'ın kullandığın kadar öde modeli genel olarak maliyet açısından verimli göründüğü için cazip ama yanlış seçenektir, ama sabit yük altında, kullandığın kadar öde'yi cazip kılan boşta kalma tasarrufunu kaybeder, ve ayrılmış kapasiteye göre daha az fiyatlandırma öngörülebilirliği sunar.

**11. Doğru cevap: A** — GPU destekli Cloud Run.
Tam yönetilen, rezervasyonsuz, sıfıra ölçeklenebilen GPU erişimi, Cloud Run'a özgü bir yetenektir — boştayken hiçbir ücret ödemediğin ve hiç altyapı yönetmediğin tek seçenek budur. GPU node pool'lu GKE cazip bir çeldiricidir çünkü GKE gerçekten GPU destekler, ama node pool yine de provizyonlanmış bir kapasiteyi temsil eder ve Cloud Run'ın istek güdümlü modeli gibi doğası gereği sıfıra ölçeklenmez.

**12. Doğru cevap: D** — Google Kubernetes Engine (GKE).
Cloud Run, HTTP/HTTPS ve gRPC trafiği üzerine kuruludur ve rastgele TCP protokollerini desteklemez — bu yüzden "özel TCP protokolü" ifadesi geçtiği anda elenir; GKE (ya da Compute Engine) gereklidir. GKE, burada Compute Engine'den daha iyi bir seçimdir çünkü senaryo aynı zamanda hibrit on-premises ve bulut ortamları arasında tutarlı container orkestrasyonu da gerektiriyor — bu da temel bir GKE kullanım senaryosudur.

**13. Doğru cevap: C** — Cloud Run.
Yeni servisler için Cloud Run açıkça önerilen yoldur — App Engine'den daha hızlı yukarı ve aşağı ölçeklenir ve Google Cloud'un geri kalanı ile üçüncü taraf servislerle daha derin entegrasyon sunar. "App Engine hâlâ var ve destekleniyor" klasik tuzaktır: mevcut App Engine uygulamalarına verilen sürekli destek, onun yeni bir şey için önerilen başlangıç noktası olduğu anlamına gelmez.

**14. Doğru cevap: B** — Hayır, karar geri alınabilirdir.
Cloud Client Library'ler kullanılarak yazılmış ve container olarak paketlenmiş uygulamalar, genellikle yeniden yazıma gerek kalmadan Compute Engine, GKE ve Cloud Run arasında taşınabilir — platform seçimi geri alınabilir bir karardır, kalıcı bir taahhüt değil. Bu yüzden önerilen varsayılan yaklaşım, basit ve serverless başlamak, gerçek bir ihtiyaç ortaya çıktığında daha fazla kontrol sunan bir platforma geçmektir.

**15. Doğru cevap: A** — Cloud Run.
Operasyon fonksiyonu olmayan, tamamen geliştiricilerden oluşan bir organizasyon doğrudan Cloud Run'a eşlenir — uygulamayı dağıtmanın ötesinde hiçbir şey gerektirmez: yönetilecek node, cluster ya da YAML manifest yoktur. GKE Autopilot cazip bir çeldiricidir çünkü o da altyapı yükünü önemli ölçüde azaltır, ama yine de hiçbir platform uzmanlığı olmayan saf bir geliştirici ekibinin kaçınmak isteyeceği Kubernetes kavramlarını (pod, deployment, manifest) anlamayı gerektirir.
