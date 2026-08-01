# Uygulaman İçin Compute Seçenekleri (Compute Options for Your Application) — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 7 — Compute Options for Your Application** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Modülün tek ve net bir sorusu var: **"Uygulamamı Google Cloud'da nerede çalıştırmalıyım?"** Bu sorunun cevabı hep aynı cümleyle başlıyor — **"duruma göre değişir."** Bu deep dive, o "duruma göre" cümlesinin arkasındaki gerçek karar mekanizmasını sana kavratmayı hedefliyor: Üç ana platformun (Compute Engine, Google Kubernetes Engine, Cloud Run) her birinin **neden var olduğunu**, **nasıl çalıştığını** ve **hangi somut durumda tercih edilmesi gerektiğini** öğrendikten sonra, bu üçünü karşılaştırmalı olarak bir arada göreceksin. Amaç, "şu servis şunu yapar" listesini ezberletmek değil; bir mimar gibi düşünüp **"bu iş yükü için doğru platform hangisi, ve neden?"** sorusuna kendi başına cevap verebilmeni sağlamak. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül tam olarak neyi öğretiyor ve neden önemli?

Bir düşünce deneyiyle başlayalım. Elinde çalıştırman gereken bir uygulama var. Bu uygulamayı nereye koyacaksın? Cevap tek bir doğru platform değil — Google Cloud sana **bir yelpaze** sunuyor, ve bu yelpazenin bir ucunda "her şeyi ben kontrol ederim" (Compute Engine), diğer ucunda "hiçbir şeyle uğraşmak istemiyorum, sadece kodumu çalıştırın" (Cloud Run) var. Ortada da bu ikisinin dengesini kuran Google Kubernetes Engine (GKE) duruyor.

Modülün açılış cümlesi bu yelpazenin **eksenini** tanımlıyor: **"Ne kadar çok altyapı kontrolüne ihtiyacın varsa, operasyonel yük de o kadar artar."** Bu tek cümle, bu modülün tüm mantığının özeti. Bir platform sana ne kadar çok "kontrol düğmesi" veriyorsa (hangi işletim sistemi, hangi disk tipi, hangi ağ protokolü, hangi güvenlik yaması), o kadar çok şeyi de **sen** yönetmek zorunda kalıyorsun. Bir platform senin elinden bu düğmeleri alıp kendisi yönetiyorsa (serverless), sen sadece kodunla ilgileniyorsun ama esneklik alanın daralıyor.

Modül şu üç platformu bu eksende ele alıyor:

1. **Compute Engine** — sanal makineler (VM). En çok kontrol, en çok operasyonel efor.
2. **Google Kubernetes Engine (GKE)** — konteynerleri yöneten, orkestre eden bir platform. Orta düzey kontrol, orta düzey efor (ve Standard/Autopilot modlarıyla bu ortanın içinde bile bir yelpaze var).
3. **Cloud Run** — tam yönetilen (fully managed), sunucusuz (serverless) bir konteyner platformu. En az kontrol, en az operasyonel efor.

Bunlara ek olarak modül, **App Engine**'i de kısaca Cloud Run'la karşılaştırıyor ve modern geliştirmede hangisinin tercih edilmesi gerektiğini netleştiriyor.

Önemli bir müjde de modülün başında veriliyor: **Eğer uygulamanı Cloud Client Library'ler kullanarak Google Cloud servisleriyle konuşacak şekilde yazarsan, genellikle uygulamanı yeniden yazmadan bir platformdan diğerine taşıyabilirsin.** Bu, aşağıda seçeceğin platformun "ölümüne bağlılık" (lock-in) anlamına gelmediğini, kararının **geri alınabilir** olduğunu gösteriyor — bu da modülün sonunda tekrar karşımıza çıkacak kritik bir fikir.

> **Önceki modüllerle bağ:** Modül 6'da, kodunun commit'ten üretime giden yolunu (CI/CD pipeline) ve bu yolun sonunda kodunun **container imajı** olarak paketlendiğini görmüştün. O container imajının **Cloud Run'a mı yoksa GKE'ye mi** dağıtılacağı sorusunun cevabı işte bu modülde. Compute Engine ise container dünyasının biraz dışında durur — çünkü Compute Engine'de container şart değildir, doğrudan bir işletim sistemi üzerinde her şeyi kendin kurarsın.

Şimdi baştan başlayalım ve üç platformu sırayla, derinlemesine inceleyelim.

---

# PARÇA 1 — Compute Engine: Maksimum Kontrol, Maksimum Sorumluluk

## Neden var?

Bulut öncesi dünyada, bir uygulama çalıştırmak istediğinde fiziksel bir sunucu satın alır, veri merkezine kurar, işletim sistemini yükler, yazılımını kurar ve bakımını yapardın. **Compute Engine, bu tanıdık deneyimi bulutta yeniden yaratır** — ama fiziksel donanımla uğraşmadan. Modülün tanımıyla: Compute Engine, **"geleneksel bir veri merkezinde kullanmış olabileceğin sunucuları taklit eden"** sanal makineler (VM) oluşturmanı sağlar.

Bu "taklit etme" kelimesi kilit noktadır. Compute Engine, sana **soyutlanmış bir platform** vermiyor; sana neredeyse fiziksel bir sunucu deneyimi kadar ham ve esnek bir zemin veriyor. Bu yüzden Compute Engine, **"Google Cloud'da çalıştırabileceğin en esnek seçenektir, ama aynı zamanda yönetmesi en çok operasyonel efor gerektiren seçenektir."** Bu iki cümle birbirinin doğal sonucudur: esneklik ve operasyonel yük, bu eksende her zaman birlikte artar.

> **Sezgi:** Compute Engine'i, boş, çıplak bir daire kiralamaya benzet. Binayı (fiziksel donanımı, elektriği, soğutmayı, ağı) Google inşa etmiş ve bakımını yapıyor. Ama dairenin **içini** — hangi mobilyayı koyacağını, duvarları nasıl boyayacağını, hangi kilit sistemini kullanacağını — tamamen sen belirliyorsun. Bu özgürlük harika, ama aynı zamanda "kilidi kim değiştirecek, temizliği kim yapacak" gibi sorumluluklar da sana ait.

## Nasıl çalışır?

Compute Engine'de VM oluştururken karşına çıkan seçim noktaları, "kontrol" fikrinin somut karşılıklarıdır:

**Makine tipleri.** Compute Engine, popüler donanım konfigürasyonları için **önceden tanımlanmış (predefined) makine tipleri** sunar. Ama sabit seçeneklerle sınırlı kalmak istemezsen, **özel makine tipleri (custom machine types)** oluşturarak VM'inin CPU ve bellek miktarını tam ihtiyacına göre ayarlayabilirsin. Bu, "standart bir konfigürasyon mu yoksa tam istediğim gibi mi" sorusuna verilen esnek bir cevaptır — GKE veya Cloud Run'da bu seviyede ince ayar imkânın yoktur, çünkü orada altyapıyı sen seçmezsin.

**Disklar.** Compute Engine'de **kalıcı disk (persistent disk)** ve **yerel SSD (local SSD)** oluşturup VM'e bağlayabilirsin. Bu disklere, bir masaüstü ya da sunucudaki fiziksel disklere eriştiğin gibi erişebilirsin. Ama fiziksel disklerden farklı olarak, **Compute Engine diskleri çalışırken (yani VM kapatılmadan) boyut olarak büyütülebilir** — ve ilginç bir detay: disk büyüdükçe **performans ve throughput da artar.** Bu, geleneksel donanımda hayal bile edemeyeceğin bir esnekliktir; fiziksel bir sunucuda disk büyütmek demek, sunucuyu kapatıp yeni bir disk takmak demektir.

**Preemptible (önlenebilir) VM'ler.** Bunlar, **büyük hesaplama ve toplu (batch) işler için ideal** olan, indirimli VM'lerdir. Google Cloud, kapasiteyi geri almak zorunda kalırsa bu VM'leri **sonlandırabilir (terminate)**. Karşılığında, kesintilere dayanabilen uygulamalar için **standart VM'lere göre en az %60 indirim** sunulur.

> **Neden bu değiş tokuş mantıklı?** Çünkü Google Cloud'un veri merkezlerinde her an kullanılmayan, atıl kapasite vardır. Bu kapasiteyi "eğer ihtiyacımız olursa geri alabiliriz" koşuluyla sana ucuza satmak, hem Google'a (atıl kaynağı paraya çevirir) hem de sana (büyük ama kesintiye toleranslı işler için devasa tasarruf) fayda sağlar. Ama bu, **her iş yükü için uygun değildir** — bir web sunucusu isteği ortasında VM'in sonlandırılması kabul edilemezken, gece çalışan bir toplu veri işleme (batch) işi, yarıda kesilip yeniden başlatılabilir.

**İşletim sistemi seçimi.** Compute Engine üzerinde Debian, CentOS, Ubuntu ve çeşitli Linux dağıtımları veya Windows çalıştırabilirsin. İstersen Google Cloud topluluğundan paylaşılan bir imaj kullanabilir, istersen **kendi işletim sistemini** getirebilirsin (bring your own OS). Bu seviyede özgürlük, örneğin GKE'de yoktur — GKE, düğümler (node) için Google'ın optimize ettiği bir işletim sistemini kullanır ve sen bunu değiştiremezsin.

## GPU, TPU ve özel donanım ihtiyaçları

Compute Engine VM'lerine **Graphics Processing Unit (GPU)** ve **Tensor Processing Unit (TPU)** bağlayabilirsin — paralel işleme ve makine öğrenmesi iş yüklerini hızlandırmak için. Bu, Compute Engine'in "özel donanım gerektiren uygulamalar için" doğru seçim olduğu senaryoların somut bir örneğidir: eğer uygulaman belirli bir GPU modeline ya da özel bir donanım yapılandırmasına ihtiyaç duyuyorsa, bu seviyedeki kontrolü sana ancak Compute Engine (ve GKE'nin belirli node pool'ları) verebilir.

## Ölçeklendirme ve yüksek kullanılabilirlik: Yönetilen örnek grupları

Compute Engine'de tek tek VM'lerle uğraşmak zorunda değilsin. Bir **instance template (örnek şablonu)** temel alınarak, **yönetilen örnek grupları (managed instance groups, MIG)** oluşturabilirsin. Bu gruplar üzerinde:

- **Global load balancing (küresel yük dengeleme)** yapılandırabilirsin.
- Grubun **otomatik ölçeklenmesini (auto scaling)** ayarlayabilirsin.
- Compute Engine, grup içinde **sağlık kontrolleri (health checks)** yapar ve **sağlıksız örnekleri otomatik olarak değiştirir.**
- Belirli bölgelerdeki trafik hacmine göre örnek sayısını **otomatik olarak ölçeklendirebilirsin.**

Bu özellikler, Compute Engine'in "sadece tek bir VM açıp unutmak" seviyesinin çok ötesine geçebildiğini gösteriyor — ama dikkat: bu ölçeklendirme ve sağlık kontrolü mekanizmalarını **kurmak ve yapılandırmak yine senin görevin.** GKE veya Cloud Run'da bu tür ölçeklendirme davranışları platformun **doğasında zaten var**; Compute Engine'de ise bunları sen inşa edersin.

## Ne zaman kullanmalısın?

Modül, Compute Engine'in kullanım senaryolarını şöyle özetliyor:

- **Altyapı üzerinde tam kontrol istediğinde.** Özel işletim sistemi ya da özel donanım gereksinimleri olan, yüksek düzeyde özelleştirilmiş VM'ler kurman gerektiğinde.
- **Üçüncü taraf lisanslı yazılım çalıştırman gerektiğinde.** Compute Engine üzerinde istediğin herhangi bir üçüncü taraf yazılımı kurup çalıştırabilirsin — bazı lisanslama modelleri belirli bir sunucu/donanım yapılandırmasına bağlı olabilir.
- **HTTP/HTTPS dışındaki TCP ağ protokollerine ihtiyacın olduğunda.** Cloud Run gibi serverless platformlar temelde HTTP/HTTPS ve gRPC isteklerine odaklıdır; farklı bir TCP protokolü konuşman gerekiyorsa Compute Engine (ya da GKE) gerekir.
- **Lift-and-shift göçlerinde.** Kendi veri merkezinden ya da başka bir bulut sağlayıcısından VM'lerini, **uygulamanı değiştirmeden** Google Cloud'a taşımak istediğinde Compute Engine idealdir. "Lift-and-shift" tam olarak bunu ifade eder: mevcut bir şeyi olduğu gibi kaldırıp (lift) yeni bir yere koymak (shift) — mimariyi yeniden tasarlamadan.

> **Sınav tuzağı — "Compute Engine her zaman en güçlü seçenektir, o zaman hep onu seçeyim" yanılgısı:** Compute Engine'in daha fazla kontrol sunması, onu "daha iyi" bir seçenek yapmaz — sadece **farklı bir noktaya** koyar. Soru "minimum operasyonel efor" ya da "sunucularla uğraşmak istemiyorum" diyorsa, doğru cevap neredeyse hiçbir zaman Compute Engine değildir. Compute Engine'in doğru cevap olduğu sinyaller şunlardır: "özel işletim sistemi", "özel donanım/lisans gereksinimi", "lift-and-shift", "HTTP dışı TCP protokolü", "GPU/TPU'ya doğrudan erişim".

---

# PARÇA 2 — Google Kubernetes Engine: Yönetilen Orkestrasyon

## Önce Kubernetes'i anlamak gerekiyor

GKE'yi anlamadan önce, altında yatan açık kaynak teknolojiyi — **Kubernetes**'i — anlaman gerekiyor. Kubernetes, **container'ları dağıtmak, ölçeklendirmek ve işletmek için önde gelen açık kaynak platformdur.** İlk olarak Google'da geliştirilmiş, bugün ise **Cloud Native Computing Foundation (CNCF)**'un bir projesi olarak geniş ve aktif bir topluluk tarafından sürdürülüyor.

Kubernetes'in var oluş nedeni şudur: **Dağıtık, konteynerize sistemleri dayanıklı (resilient) bir şekilde ve ölçekte çalıştırmak için bir çerçeve sunmak.** Tek başına bir container çalıştırmak kolaydır; ama yüzlerce container'ı, bunların birbiriyle nasıl konuşacağını, hangi container'ın hangi kaynağa erişeceğini, bir container çökerse ne olacağını, trafik arttığında kaç kopya (replica) çalışacağını **elle** yönetmek imkânsız hale gelir. Kubernetes bu operasyonel karmaşıklığı bir çerçeveye oturtur: **uygulama bileşenlerinin ölçeklendirilmesi, ağ soyutlamaları, failover orkestrasyonu, dağıtımların (deployment) yayına alınması, depolama orkestrasyonu, secret ve konfigürasyon yönetimi.**

### Kubernetes'in temel yapı taşları

Bir Kubernetes cluster'ı (küme), iki tür bileşenden oluşur:

- **Control plane (kontrol düzlemi).** Cluster'daki worker node'ları ve pod'ları **yönetir.** Cluster'ın "beyni" gibi düşünebilirsin — hangi container'ın nerede çalışacağına, sağlık durumuna, ölçeklendirme kararlarına burada karar verilir.
- **Node'lar (düğümler).** Cluster'daki, uygulamalarını fiilen çalıştıran **makinelerdir** — sanal ya da fiziksel olabilirler.
- **Pod.** Bir node üzerinde **ağ ve depolama kaynaklarını paylaşan bir grup container.** Kubernetes'in en küçük dağıtım (deployment) birimi pod'dur — tek bir container değil.

> **Sezgi:** Control plane'i bir orkestra şefi gibi düşün; node'lar orkestra üyeleri, pod'lar da her üyenin çaldığı enstrüman grubu. Şef, kimin ne zaman çalacağına karar verir, birileri hastalanırsa (node/pod sağlıksız olursa) yerine başkasını koyar — ama enstrümanı fiilen çalan yine üyelerdir (node'lar).

## GKE nedir ve neyi yönetir?

**Google Kubernetes Engine (GKE)**, Google altyapısı üzerinde çalışan **yönetilen bir Kubernetes servisidir.** GKE, konteynerize uygulamaların için Kubernetes ortamlarını dağıtmana, yönetmene ve ölçeklendirmene yardımcı olur. Daha kesin ifadeyle: GKE, Google Cloud'un compute tekliflerinin, Kubernetes iş yüklerini buluta taşımayı kolaylaştıran bir bileşenidir.

Burada kritik bir ayrım var: **Yönetilmeyen (unmanaged) bir Kubernetes cluster'ında**, operasyonel işin büyük çoğunluğunu **sen** yaparsın — control plane'i ayakta tutmak, yamalamak, ölçeklendirmek, yüksek kullanılabilirliğini sağlamak dahil. GKE, bu operasyonel efor'un büyük bölümünü **senin yerine otomatik olarak üstlenir**, Kubernetes cluster'ı oluşturmak ve yönetmek için gereken birçok altyapı görevini ortadan kaldırarak.

### GKE Standard vs GKE Autopilot — İki farklı sorumluluk paylaşımı

GKE'de iki işletim modu vardır ve bu ayrım sınavda sık sorulur:

**GKE Standard modu.** Google şunları yönetir: **control plane, pod'ların ölçeklendirilmesi, node yamaları ve yükseltmeleri, cluster'ın izlenmesi (monitoring), erişilebilirliği ve güvenilirliği.** Ama varsayılan olarak, **altta yatan node'ları ve node pool'larını sen yönetirsin** — provizyonlama, bakım ve yaşam döngüsü yönetimi dahil. Ağ ve güvenlik yapılandırmasını seçmek de senin sorumluluğundadır. Standard mod, **gelişmiş yapılandırma esnekliği** sunar — node tipini, GPU/TPU eklentilerini, ağ ayarlarını ince ayar yapabilirsin.

**GKE Autopilot modu.** Burada **cluster'ın tüm altyapısı** — control plane, node pool'lar ve node'ların kendisi — Google tarafından yönetilir. Autopilot, cluster altyapısını yöneterek **operasyonel ve bakım maliyetlerini azaltmaya**, aynı zamanda **kaynak kullanımını iyileştirmeye** yardımcı olur. Autopilot, tamamen yönetilen bir Kubernetes deneyimi sunarak, senin cluster altyapısının yönetimi yerine **iş yüklerine odaklanmanı** sağlar. Ayrıca Autopilot, **GKE sıkılaştırma (hardening) kılavuzlarını** ve güvenlik/ağ en iyi uygulamalarını otomatik olarak uygular ve daha az güvenli pratikleri engeller.

> **Sezgi — Standard vs Autopilot:** Standard modu, kendi mutfağın olan ama market alışverişini, temizliğini, tamiratını yine senin yaptığın bir eve; Autopilot'u ise oda servisi olan bir otele benzet. Otelde (Autopilot) sen sadece "şu yemeği istiyorum" (workload'unu) söylersin, geri kalan her şeyi (mutfağın kurulumu, temizliği, malzeme tedariki) otel halleder. Ev'de (Standard) ise mutfağın düzenini istediğin gibi kurabilirsin — ama bakımı da sana kalır.

Aynı proje içinde bile **farklı cluster'lar için farklı modları** kullanabilirsin — bu, GKE'nin "kontrol/efor" ekseninde bile kendi içinde esneklik sunduğunu gösteriyor.

> **Sınav tuzağı — "GKE Standard node'ları da Google yönetir" yanılgısı:** GKE Standard modunda Google, **control plane'i** yönetir ama **node'ları varsayılan olarak sen yönetirsin** (provizyonlama, bakım, yaşam döngüsü). Soru "node pool yönetimi de tamamen bana ait olmasın istiyorum" diyorsa cevap **Autopilot**'tur, Standard değil.

## GKE'nin altyapı otomasyonu: Neyi senin yerine yapıyor?

GKE, tam yönetilen (fully managed) olduğu için **altta yatan kaynakları senin provizyonlaman gerekmez.** Birkaç somut otomasyon örneği:

- GKE, iş yüklerini çalıştırmak için **container-optimized bir işletim sistemi** kullanır. Bu işletim sistemini Google bakımını yapar ve **hızlı ölçeklenme ve minimal kaynak ayak izi** için optimize eder.
- **AutoUpgrade** özelliği etkinleştirildiğinde, cluster'ların her zaman **Kubernetes'in en son kararlı (stable) sürümüyle** otomatik olarak güncellenmesini sağlar.
- **Auto-repair**, sağlıksız node'ları otomatik olarak onarabilir. Her node üzerinde periyodik sağlık kontrolleri yapar; bir node'un onarım gerektirdiği belirlenirse, GKE önce o node'u **drain eder** (iş yüklerinin zarif bir şekilde çıkmasına izin verir), ardından node'u **yeniden oluşturur.**
- GKE, hem cluster içindeki **iş yüklerinin ölçeklenmesini** hem de **cluster'ın kendisinin ölçeklenmesini** destekler.
- GKE, uygulamanın performansını ve davranışını anlamana yardımcı olmak için **Cloud Monitoring ve Cloud Logging** kullanır.

## GKE'nin Google Cloud ekosistemiyle entegrasyonu

GKE, Google Cloud'un birçok parçasıyla sorunsuz entegre olur:

- **Cloud Build** ile, **Artifact Registry**'de güvenle sakladığın özel container imajlarını kullanarak iş yüklerinin dağıtımını otomatikleştirebilirsin.
- **Identity and Access Management (IAM)**, hesaplar ve rol izinleri kullanarak erişimi kontrol etmeni sağlar.
- GKE, **Virtual Private Cloud (VPC)** ile entegredir — Google Cloud'un ağ özelliklerini kullanmanı sağlar.
- **Google Cloud Console**, GKE cluster'larına ve kaynaklarına dair içgörüler sunar; cluster'lardaki kaynakları görüntülemene, incelemene ve silmene olanak tanır.

Bu entegrasyonlar, Modül 6'da öğrendiğin CI/CD pipeline'ının (Cloud Build → Artifact Registry → Cloud Deploy) GKE ile **doğrudan uyumlu** çalıştığını gösteriyor — GKE, o pipeline'ın "dağıtım hedefi" uçlarından biridir.

## Ne çalıştırabilirsin? Kullanım senaryoları

GKE, **Docker imajı olarak paketleyebileceğin her uygulama çalışma zamanını (runtime)** destekler. GKE özellikle şu senaryolarda güçlüdür:

- **Konteynerize uygulamalar**, üçüncü taraf konteynerize yazılımlar dahil.
- **Hibrit ya da çoklu bulut ortamları.** Aynı container imajını, on-premises'te ya da farklı bulut sağlayıcılarında çalıştırabilirsin. Bu özellikle uygulamanın bir kısmının on-premises'te, bir kısmının bulutta çalıştığı durumlarda kritiktir.
- **HTTP/HTTPS dışında ağ protokolleri kullanan konteynerize uygulamalar.**

## GKE'nin depolama ve ağ otomasyonu

Bir Kubernetes ortamının altyapısını yönetmek karmaşık olabilir; GKE bu operasyonel görevlerin çoğunu basitleştirir:

- Kubernetes **persistent volume**'lar (kalıcı hacimler) oluşturduğunda, GKE varsayılan olarak **Google Cloud persistent disk'lerini otomatik olarak provizyonlar** — stateful (durum tutan) uygulamalar için depolama sağlamak amacıyla.
- Kubernetes **network load balancer servisleri** dağıttığında, GKE otomatik olarak **Google Cloud ağ yük dengeleyicilerini** provizyonlar.
- Kubernetes **Ingress kaynakları** yapılandırdığında, GKE **Google Cloud HTTP/HTTPS yük dengelemeyi** otomatik olarak provizyonlar.

Bu otomatik provizyonlama, bu kaynakları **elle** yapılandırma ve yönetme ihtiyacını ortadan kaldırır. GKE ayrıca **Google Cloud Observability** desteğine sahiptir — sorun giderme (troubleshooting) ve uygulama/servis izleme araçlarıyla entegrasyon sağlar.

## GKE ve yapay zeka/makine öğrenmesi iş yükleri

GKE ile, yönetilen Kubernetes'in tüm faydalarına sahip **sağlam, üretime hazır bir AI/ML platformu** kurabilirsin. GKE, ölçekte AI/ML iş yüklerinin eğitimini ve sunumunu (serving) destekleyen **GPU ve TPU'ları destekleyen altyapı orkestrasyonu** sunar.

- **GKE Standard modunda**, GPU ya da TPU eklenmiş VM'lerden oluşan **node pool'lar** oluşturursun, ardından bu node'larda çalışan konteynerize iş yüklerine GPU/TPU kaynaklarını tahsis edersin.
- **GKE Autopilot modunda**, iş yükün için ihtiyacın olan GPU/TPU kaynaklarını **belirtirsin**, ve GKE bu kaynakları sağlayan node'ları otomatik olarak yönetir.

Bu, Parça 1'de gördüğün "GPU/TPU'ya doğrudan erişim = Compute Engine" genellemesinin **eksiksiz olmadığını** gösteriyor: GKE de GPU/TPU destekler, ama bunu **container orkestrasyonu içinde**, node pool soyutlaması aracılığıyla sunar. Fark, Compute Engine'de GPU'yu tek bir VM'e doğrudan bağlarken, GKE'de GPU'yu bir node pool'a bağlayıp, üzerindeki container iş yüklerine **paylaştırman**dır.

## Cluster dağıtımı ve ölçeklendirme

GKE, cluster dağıtımını ve ölçeklendirmesini basitleştirir. Uygulamalarının gerektirdiği tüm container'lar için **compute, bellek, ağ ve depolama kaynaklarını** tarif edebilirsin; GKE bu kaynakları otomatik olarak provizyonlar ve yönetir. **Sabit boyutlu cluster'lar** dağıtabilir ya da cluster'ını **otomatik ölçeklenecek** şekilde yapılandırabilirsin — otomatik ölçeklendirme, cluster içinde çalışan container'ların kaynak ihtiyaçlarındaki değişikliklere göre compute örneklerini ekler ya da kaldırır.

## Uygulamayı dağıtmak: kubectl ve YAML manifest

GKE'de uygulamalarını, herhangi bir başka Kubernetes ortamında olduğu gibi dağıtır ve yönetirsin. Çoğu operasyonel görevi **`kubectl` komutu** ile gerçekleştirebilirsin.

Ad hoc kaynakları doğrudan `kubectl` komutlarıyla dağıtmak mümkün olsa da, **önerilen en iyi uygulama, konfigürasyonları tanımlamak için YAML manifest dosyaları kullanmaktır.** Bu dosyalar:

- Uygulamandaki bileşenler için kullanılan container'ların özelliklerini tanımlar.
- Ağ servislerini, güvenlik politikalarını ve dayanıklı, ölçeklenebilir konteynerize uygulamalar sunmak için kullanılan diğer Kubernetes nesnelerini tanımlayabilir.

Uygulamalar **deployment** kullanılarak dağıtılabilir; burada Kubernetes, bir pod ya da pod grubu için belirtilen sayıda **replica**'nın sürekli olarak çalışır durumda kalmasını sağlar. Deployment'lar **stateless (durum tutmayan) bileşenler** için kullanılır. **Stateful set**'ler ise, kalıcı depolamaya ihtiyaç duyan uygulamalar için kullanılır. YAML manifest, bunların dışında başka birçok kaynak tipini de tanımlayabilir.

> **Sınav tuzağı — Deployment vs StatefulSet:** Bir uygulama pod'unun kimliğinin (identity) ya da kalıcı depolamasının **önemli olmadığı**, herhangi bir kopyanın herhangi bir isteğe cevap verebileceği durumlarda **deployment** kullanılır (stateless). Bir uygulamanın **kalıcı depolamaya ve kararlı bir ağ kimliğine** ihtiyacı olduğunda (örneğin bir veritabanı) **stateful set** kullanılır.

## GKE ve CI/CD

CI/CD pipeline'ının bir parçası olarak, her kod commit'i için yeni bir Docker imajı üretebilirsin. Bu pipeline, imajı **geliştirme, test ve üretim ortamlarına otomatik olarak dağıtabilir.** **Cloud Build, Artifact Registry, Cloud Deploy ve GKE** birlikte kullanılarak güçlü bir CI/CD sistemi kurulabilir — bu, Modül 6'da öğrendiğin tüm pipeline anatomisinin GKE tarafında somutlaşmış hâlidir.

---

# PARÇA 3 — Cloud Run: Sunucusuz Konteyner Platformu

## Neden var?

Cloud Run, yelpazenin diğer ucunda duruyor. Modülün tanımıyla: Cloud Run, **isteğe (request) ya da olaya (event) dayalı, stateless (durum tutmayan) iş yüklerini, sunucularla hiç uğraşmadan çalıştırmanı sağlayan tam yönetilen bir compute platformudur.**

Buradaki fark, GKE'ye kıyasla **radikal**dir: GKE'de, Google control plane'i yönetir ama sen hâlâ node pool boyutunu, ölçeklendirme kurallarını, cluster yapılandırmasını düşünürsün. Cloud Run'da ise **tüm altyapı yönetimi** — provizyonlama, yapılandırma, sunucu yönetimi — **tamamen soyutlanmıştır.** Sen sadece kodunu yazmaya odaklanırsın.

> **Sezgi:** Eğer Compute Engine "boş bir daire kiralamak", GKE "kendi kurallarını koyduğun ama binanın altyapısını yönetmediğin bir site yönetimi" ise, Cloud Run **"oda servisi olan, check-in/check-out'un saniyeler sürdüğü bir otel odasıdır."** Sen sadece valizini (kodunu) getirirsin; oda hazırdır, temizliği, elektriği, su faturası hep otelin (Google'ın) işidir. Odayı kullanmadığın saatler için **ödeme bile yapmazsın** — bu otel benzetmesinin gerçek hayatta olmayan ama Cloud Run'da gerçek olan kısmı budur.

## Sıfıra ölçeklenme ve kullandığın kadar ödeme

Cloud Run'ın en karakteristik özelliği şudur: **Trafiğe bağlı olarak neredeyse anında sıfırdan yukarı ve yukarıdan sıfıra ölçeklenir.** Bu iki yönlü ölçeklenmeyi biraz açalım:

- Trafik geldiğinde, Cloud Run container'ları **otomatik olarak** başlatır ve talebi karşılamak için gerektiği kadar çoğaltır (yatay ölçeklendirme).
- Trafik azaldığında, Cloud Run container sayısını azaltır — **hatta sıfıra kadar.** Yani hiç istek gelmiyorsa, hiçbir container çalışmaz.

Bunun doğal sonucu **fiyatlandırma modelidir**: Cloud Run, sadece istek işlenirken tükettiğin **CPU, bellek ve ağ** kaynakları için, **en yakın 100 milisaniyeye yuvarlanarak** ücretlendirir. Yani hiçbir zaman **fazla provizyonlanmış (overprovisioned) kaynak için** ödeme yapmazsın — GKE ya da Compute Engine'de olduğu gibi, kullanmadığın zamanlarda da açık duran bir kaynak için para ödemezsin.

> **Neden bu kadar önemli?** Çünkü birçok gerçek dünya iş yükü **düzensiz (bursty)** trafik desenlerine sahiptir — gece boş, öğlen yoğun; ya da ayda bir kez çalışan bir batch işi. Sabit kapasiteli bir platformda (Compute Engine, GKE) bu düzensizliğe karşı ya fazla kapasite ayırıp boşa para harcarsın, ya da yetersiz kapasite ayırıp trafik zirvelerinde performans kaybedersin. Cloud Run, bu ikilemi **talebe göre anlık ölçeklenerek ve sadece kullanımın kadar ödeyerek** ortadan kaldırır.

## GPU desteği: Sunucusuzluğun sınırlarını genişletmek

Cloud Run servisleri ve fonksiyonları da **GPU kullanabilir.** Cloud Run üzerindeki GPU, **tam yönetilendir** — ekstra sürücü ya da kütüphane kurulumuna gerek yoktur. GPU özelliği, **rezervasyon gerektirmeden isteğe bağlı (on-demand) kullanılabilirlik** sunar. GPU yapılandırılmış bir Cloud Run servisinin örnekleri, kullanılmadığında **maliyet tasarrufu için sıfıra kadar ölçeklenebilir.** Her Cloud Run örneği için **bir GPU** yapılandırabilirsin.

GPU'lu Cloud Run servisleri ve fonksiyonları şu iş yükleri için uygundur:

- **Büyük dil modelleriyle AI çıkarım (inference) iş yükleri.**
- **AI dışı, yoğun hesaplama gerektiren senaryolar** — örneğin video transkodlama ve 3D render.

> **Neden bu önemli bir gelişme?** Çünkü geleneksel olarak "serverless" ile "GPU/AI iş yükleri" birbirine uzak kavramlar olarak görülürdü — GPU iş yükleri genelde özel, uzun süre çalışan altyapı (Compute Engine ya da GKE) gerektirirdi. Cloud Run'ın GPU desteği, **serverless'ın esnekliğini (sıfıra ölçeklenme, kullandığın kadar öde) AI iş yüklerine taşıyarak**, önceden Compute Engine ya da GKE gerektiren bazı senaryoları artık Cloud Run'a taşınabilir hale getiriyor.

## Kod senin yolun: Diller, çerçeveler ve kısıtlama yokluğu

Birçok serverless platform, dil ve kütüphane desteğine kısıtlamalar getirir ya da kodunu belirli bir şekilde yazmanı zorunlu kılar. Cloud Run bunun tersini yapar: **HTTP ya da gRPC üzerinden gelen istekleri ya da olayları dinleyen herhangi bir stateless container'ı kolayca dağıtmana izin vererek, kodunu kendi yolunla yazmanı sağlar.**

Container'lar sana **esneklik ve taşınabilirlik** kazandırır. Cloud Run ile, uygulamalarını **istediğin dilde, istediğin framework ve araçlarla** inşa edebilir ve **saniyeler içinde** dağıtabilirsin. Tek bir komutla konteynerize uygulamaları dağıtabilirsin; sonrasında Cloud Run, gelen istekleri karşılamak için container imajını **otomatik olarak yatay ölçeklendirir**, talep azaldığında da ölçeği düşürür. **Sadece istek işlenirken tüketilen CPU, bellek ve ağ için** ödersin.

## Dağıtım seçenekleri: Dört yol, tek hedef

Cloud Run birden çok dağıtım seçeneği sunar. **Bütün dağıtım seçenekleri sonuçta, Google Cloud'un tam yönetilen ve yüksek ölçeklenebilir altyapısında çalışan bir container imajı üretir.** Bu önemli bir kavramdır: hangi yolu seçersen seç, altta yatan çalışma zamanı hep aynıdır — sadece **imajın nasıl üretildiği** değişir.

### 1. Container

Kendi seçtiğin herhangi bir taban imajı (base image) kullanan ve istediğin dilde kod çalıştıran herhangi bir container'ı sağlayabilirsin — container ve içindeki uygulamanın belirli kurallara uyması koşuluyla. Örneğin: container ve uygulama, **`0.0.0.0` üzerinde bir portta** istekleri dinlemelidir, ve container **TLS (Transport Layer Security) olmadan HTTP kullanmalıdır.** TLS, Cloud Run tarafından sonlandırılır (terminate edilir) ve istekler container'a **TLS'siz olarak proxy'lenir.**

Container imajlarını doğrudan **Artifact Registry** ya da **Docker Hub**'da saklanan yerlerden dağıtabilirsin.

### 2. Kaynak kod (source code)

Cloud Run, tek bir `gcloud run deploy` komutuyla **kaynak kodunu build edip dağıtmana** izin verir. Kaynak kod dağıtıldığında, **Cloud Build**, kodu Artifact Registry'de saklanan bir container imajına dönüştürür.

Dağıttığın kaynak kod bir **Dockerfile** içerebilir. Eğer kaynak kod dizininde bir Dockerfile yoksa, kaynak kod **Google Cloud buildpack'lerinin desteklediği dillerden birinde** yazılmış olmalıdır. Desteklenen diller: **Python, Node.js, Go, Java, Ruby, PHP ve .NET.**

Deploy komutu, kaynak kodundan otomatik olarak container imajı inşa etmek için **Google Cloud buildpack'lerini ve Cloud Build'i** kullanır:

- Dockerfile **varsa**, yüklenen kaynak kod o Dockerfile kullanılarak build edilir.
- Dockerfile **yoksa**, kullandığın dil otomatik olarak tespit edilir, kodun bağımlılıkları getirilir (fetch edilir), ve **güvenli bir taban imaj kullanarak üretime hazır bir container imajı** inşa edilir.

İnşa edilen container imajları **Artifact Registry**'de saklanır.

> **Neden bu iki seçenek (container vs source) bir arada var?** Modülün kendi ifadesiyle: **"Bir container imajı build ederken, yazılımın nasıl build edildiği ve her dosyanın nasıl eklendiği üzerinde tam kontrole sahip olursun. Bazen bu kontrole ihtiyaç duyarsın. Ama bir uygulamayı build etmek zaten yeterince zor; bazen sadece kaynak kodunu bir HTTPS uç noktasına (endpoint) dönüştürmek istersin."** Container-tabanlı iş akışı, kontrolün önemli olduğu durumlar için; source-tabanlı iş akışı, "container imajımın güvenli, iyi yapılandırılmış ve tutarlı biçimde inşa edildiğinden emin olmak istiyorum ama bununla bizzat uğraşmak istemiyorum" diyenler için vardır.

### 3. Fonksiyonlar (functions)

Cloud infrastruktüründen ya da servislerinden ya da desteklenen üçüncü taraf sağlayıcılardan yayımlanan olaylarla (event) tetiklenen, **tek amaçlı fonksiyonlar** dağıtabilirsin. Fonksiyonlar için sadece **fonksiyon kodunu** desteklenen dillerden birinde sağlarsın, ve bir buildpack container'ı oluşturur. **Fonksiyonlar, Cloud Run servisleri olarak dağıtılır** — yani fonksiyonlar, Cloud Run'ın ayrı bir ürünü değil, Cloud Run'ın **üzerine kurulu bir dağıtım şeklidir.**

### 4. Sürekli kaynak dağıtımı (continuous source deployment)

Bir GitHub deposundan **sürekli kaynak dağıtımı** kullanarak bir Cloud Run servisi ya da fonksiyonu dağıtabilirsin. **Cloud Build**, bir Git deposunun belirli bir dalına yeni commit'ler push edildiğinde build'leri ve dağıtımları otomatikleştirebilir. Dockerfile içeren ya da Google Cloud buildpack'lerinin desteklediği dillerden birinde yazılmış kaynak kodu dağıtabilirsin. Farklı depo sağlayıcıları için, Cloud Build ve **Cloud Deploy** kullanarak kendi CI/CD sürekli dağıtımını kurabilirsin.

## Geliştirici iş akışı: Üç adım

Cloud Run geliştirici iş akışı basit, üç adımlı bir süreçtir:

1. **Sevdiğin programlama dilini kullanarak uygulamanı yaz.** Bu uygulama web isteklerini dinlemelidir.
2. **Uygulamanı bir container imajına build et ve paketle.**
3. **Container imajını Cloud Run'a dağıt.**

Container imajını dağıttığında, benzersiz bir **HTTPS URL** alırsın. Cloud Run, gelen istekleri karşılamak için container'ını **talep üzerine (on demand)** başlatır ve tüm gelen isteklerin kapasitesini sağlamak için container'ları dinamik olarak **ekler ve kaldırır.**

## Kullanım senaryoları

Cloud Run birçok senaryo için kullanılabilir:

- **Veri işleme (data-processing) uygulamaları.** Provizyonlanmış kaynak için değil, kullanım için ödediğinden, sadece **veri işlenirken** ödeme yaparsın.
- **Küçük ya da büyük web uygulamaları ve web API'leri.** Cloud Run, gerektiğinde yukarı ölçeklenir, kullanılmadığında sıfıra ölçeklenir.
- **Pub/Sub ya da Eventarc olaylarına yanıt olarak çalıştırılması gereken işler (job'lar).**
- **Hafif ETL (Extract Transform Load) işlemleri** ya da bir Pub/Sub konusuna yayımlanan mesajları işleme.
- **Webhook'lar için hedef.** Uygulamaların ya da servislerin mikroservisleri tetiklemek için doğrudan HTTP çağrıları yapmasına izin verir.
- **Bir olaya yanıt olarak hızlıca küçük bir veri parçasını işlemesi gereken mikroservisler.**

### Node.js örneği: Fonksiyonların pratik detayı

Modül, Node.js runtime'ını somut bir örnek olarak veriyor: Node.js runtime kullanırken, fonksiyonunun kaynak kodu bir **Node.js modülü** olarak export edilmelidir. Bağımlılık paketlerini içeren zip dosyaları yüklemene gerek yoktur — Node.js fonksiyonunun bağımlılıklarını bir **`package.json`** dosyasında belirtebilirsin; Cloud Run, kodunu çalıştırmadan önce **tüm bağımlılıkları otomatik olarak kurar.** Diğer Google Cloud servisleriyle programatik olarak etkileşim kurmak için **Cloud Client Library'lerini** kullanabilirsin.

## Cloud Run Jobs: Servislerden farklı bir hayvan

Buraya kadar gördüğün her şey **Cloud Run servisleri (services)** ile ilgiliydi — HTTP isteklerini dinleyen, isteğe göre ölçeklenen yapılar. Ama Cloud Run'ın ikinci, farklı çalışan bir modu daha var: **Cloud Run jobs (işler).**

**Cloud Run job'ları, HTTP tabanlı Cloud Run servislerinden farklı çalışır.** Bir Cloud Run job'ı, HTTP isteklerini **dinlemez ve karşılamaz.** Bir porta bağlanmasına (listen) ya da bir web sunucusu başlatmasına gerek yoktur. Bunun yerine, job **tek seferlik bir görev (one-off task)** olarak ya da bir workflow'un parçası olarak **çalıştırılır.** Job'ı **düzenli bir zamanlamayla** çalıştırmak için **Cloud Scheduler** de kullanabilirsin.

Bir Cloud Run job'ı tamamlandığında, **job çıkış yapar (exits).** Her job, **tek bir görevden (task)** ya da **birden çok bağımsız görevden** oluşabilir. Bir job'daki birden çok görev birbirinden bağımsız olduğu için, bu görevler **paralel olarak çalıştırılabilir.** Her görev çalıştırması (task execution), kendi **görev indeksinin (task index)** farkındadır — bu indeks, göreve bir **ortam değişkeni (environment variable)** aracılığıyla sağlanır. Ayrıca, başarısız olan görevler **otomatik olarak yeniden denenebilir (retried).** Backend servislerini çok fazla eşzamanlı görevle boğmamak için **maksimum paralel görev sayısı** belirtilebilir.

Bir job içindeki her görev, **tek bir container imajı** çalıştırır. Bu container, **tamamlanana kadar (to completion)** çalışır. Cloud Run servisleri gibi, Cloud Run job'ları da **tamamen serverless bir platformda çalışır** — job'larını çalıştırmak için herhangi bir altyapı yönetmen gerekmez.

> **Sınav tuzağı — Cloud Run service vs Cloud Run job:** Bir servis, **sürekli** HTTP/gRPC isteklerini dinler ve karşılar; talep olduğu sürece "canlı" kalır. Bir job, **başlar, işini yapar, biter** — HTTP dinlemez, port açmaz. Soru "düzenli aralıklarla çalışması gereken bir toplu işim var, bitince kapanmalı" diyorsa cevap **Cloud Run job**'dır. Soru "gelen isteklere HTTP üzerinden cevap vermeli" diyorsa cevap **Cloud Run service**'tir.

| Özellik | Cloud Run **service** | Cloud Run **job** |
| --- | --- | --- |
| Tetikleyici | Gelen HTTP/gRPC isteği | Manuel çalıştırma, workflow, ya da Cloud Scheduler |
| Davranış | Sürekli istek dinler, isteğe göre ölçeklenir | Başlar, işini bitirir, çıkış yapar (exits) |
| Port dinleme | Evet (`0.0.0.0` üzerinde) | Hayır |
| Paralellik | İstek bazlı otomatik yatay ölçekleme | Bağımsız task'ların paralel çalıştırılması |
| Tipik kullanım | Web API, web uygulaması, webhook hedefi | Batch işleme, zamanlanmış işler, ETL |

---

# PARÇA 4 — Platformları Karşılaştırmak: Hangisini Ne Zaman Seçmeliyim?

Üç platformu artık tek tek derinlemesine gördün. Şimdi modülün asıl sorduğu soruya dönelim: **"Uygulamalarımı nerede çalıştırmalıyım?"** Cevap hâlâ **"duruma göre değişir"** — ama artık bu cevabı **somut sorularla** çözebilecek durumdasın.

## Soru 1: Ne kadar altyapı kontrolüne ihtiyacım var?

Bu, karar ağacının **ilk ve en önemli** dalıdır:

- **Legacy sistemleri lift-and-shift ile buluta taşımak istiyorsan**, ya da **belirli donanıma bağlı özel lisanslama gereksinimlerin varsa** → **Compute Engine** ihtiyacın olabilir.
- **Container'lar çalıştırabiliyorsan** ve **birden fazla bulut ya da veri merkezinde hibrit sistemlerin varsa**, ya da **HTTP tabanlı olmayan uygulamaların varsa** → **Google Kubernetes Engine** doğru seçim olabilir.
- **Stateless container'lar çalıştırmak istiyorsan ama altyapıyı hiç yönetmek istemiyorsan**, ya da **sadece bulut servislerini birbirine bağlayan olay güdümlü (event-driven) fonksiyonlar yazman gerekiyorsa** → **Cloud Run** muhtemelen en iyi seçimdir.

## Soru 2: Operasyonel efor dengesi nasıl işliyor?

Daha fazla altyapı kontrolü kazanmak, daha fazla operasyonel efor gerektirir. Bunu somutlaştıralım:

- **Compute Engine** VM'i oluşturduğunda, **işletim sistemi ve yazılım güncellemelerini sen kontrol edersin.**
- **GKE** ile, Google cluster'ının **sanal makine node'larını** yönetir, ama sen hâlâ **cluster'ın boyutunu yönetir** ve **cluster içindeki her uygulamanın nasıl ölçekleneceğine sen karar verirsin.**
- **Cloud Run** sunucusuzdur (serverless). Sadece uygulamanı dağıtman yeterlidir; Google altyapıyı ve otomatik ölçeklendirmeyi yönetir.

## Soru 3: Ekiplerim nasıl yapılanmış?

Bu soru, teknik olmaktan çok **organizasyonel** bir soru ama kararı doğrudan etkiliyor:

- Ekiplerin **çoğunlukla geliştirici odaklıysa**, Cloud Run muhtemelen senin için en iyisidir.
- Hem **geliştirici hem operasyon** ekiplerin varsa, uygun olduğunda yine Cloud Run servisleri ve fonksiyonlarını kullanabilirsin. Ama aynı zamanda, **hibrit sistemlerinle entegre olmak** ve iş yükleri üzerinde **daha fazla kontrol** sahibi olmak için **GKE**'ye de karar verebilirsin. **Stateful uygulamalar ve HTTP dışı ağ protokolleri**, GKE ile kullanılabilir ama **Cloud Run ile kullanılamaz.**
- Uygulamalarını zaman içinde modernize ediyorsan, on-premises veri merkezlerinden göç ettirilmiş **Compute Engine VM'lerini** yönetmen gerekebilir. Operasyon ekibinin bu VM'lerin sağlığını ve güvenliğini yönetebiliyor olması gerekir.

## Soru 4: Fiyatlandırma modeli neyi tercih ettiriyor?

Platformların fiyatlandırması farklıdır ve bu da seçimi etkileyebilir:

- **Compute Engine ve Google Kubernetes Engine** ücretleri, **ayrılmış (dedicated) VM kullanımına** dayanır. Ücretler **öngörülebilirdir** (predictable) ve bu platformlar, uygulamaların **tutarlı (consistent) kapasiteye** ihtiyaç duyduğu durumlarda idealdir.
- **Cloud Run servisleri ve fonksiyonları kullandığın kadar ödeme (pay per use)** modeliyle çalışır — bu, özellikle **trafiğin tutarsız/düzensiz olduğu durumlarda önemli tasarruflar** sağlayabilir.

> **Sezgi:** Sabit, öngörülebilir bir trafik deseni varsa (7/24 çalışan, yükü nispeten sabit bir servis), Compute Engine/GKE'nin "ayrılmış kaynak" fiyatlandırması genelde daha ucuz ve öngörülebilirdir. Trafik dalgalıysa (bazen boş, bazen zirve, ya da nadiren çalışan bir batch iş), Cloud Run'ın "kullandığın kadar öde" modeli parasal olarak çok daha avantajlı olur — çünkü boşta duran kaynağa asla para ödemezsin.

## App Engine ile Cloud Run: Neden Cloud Run'ı tercih etmelisin?

Modül burada, tarihsel bir bağlamı da netleştiriyor. **App Engine**, tam yönetilen, serverless bir compute ortamıdır ve **standart** ile **esnek (flexible)** olmak üzere iki ortamı destekler.

**Cloud Run**, Google Cloud serverless'ının **en son evrimidir** — App Engine'i on yılı aşkın süredir çalıştırmanın deneyimi üzerine inşa edilmiştir. Cloud Run servisleri, App Engine servisleriyle aynı iş yüklerini karşılayabilir, ama Cloud Run bu servisleri uygularken **çok daha fazla esneklik** sunar. Bu esneklik — ve Google Cloud ile üçüncü taraf servislerle **geliştirilmiş entegrasyonlar** — Cloud Run'ın, **App Engine üzerinde çalışamayan** iş yüklerini de çalıştırabilmesini sağlıyor.

App Engine'in aksine, Cloud Run trafikteki artışlara **neredeyse anında** yukarı ve aşağı ölçeklenebilir. Ve varsayılan olarak, Cloud Run servisleri için **sadece istekler işlenirken** ödeme yaparsın.

> **Modülün net tavsiyesi:** **"Yeni bir servis oluşturuyorsan, App Engine yerine Cloud Run kullanmalısın."** Bu, sertifikasyon sınavında karşına çıkabilecek doğrudan bir kural cümlesidir — App Engine, artık **yeni projeler için önerilen** bir başlangıç noktası değildir; Cloud Run onun yerini almıştır.

> **Sınav tuzağı — "App Engine hâlâ var, o zaman hâlâ tercih edilebilir" yanılgısı:** App Engine'in var olması ve desteklenmeye devam etmesi (özellikle eski/mevcut uygulamalar için), onun **yeni** uygulamalar için önerilen seçenek olduğu anlamına gelmez. Soru "yeni bir serverless uygulama kuracağım, hangisini seçmeliyim" diyorsa cevap her zaman **Cloud Run**'dır.

## Kararının geri alınabilir olması

Modülün kapanışında çok değerli bir fikir var: **"Nerede çalıştırmalıyım?" sorusunun en iyi cevabı, her iş yükünü, gereksinimlerine en uygun platformda çalıştırmandır.** Tek bir platformda standartlaşmak **zorunda değilsin** — **tek bir uygulama içinde bile** değil. Büyük uygulamalar, her problemi doğru araçla çözmelerine imkân tanıyan **birden fazla platform** kullanmaktan fayda görebilir.

Genel eksende, kontrol eksenin **soluna** doğru gittikçe, uygulaman ve altyapın üzerinde **daha fazla kontrole** sahip olursun, ama bu altyapıyı yönetmek **daha fazla operasyonel maliyet ve efor** gerektirir.

Ve en önemlisi: **Cloud Client Library'ler ile yazılmış çoğu uygulama, platformdan platforma kolayca taşınabilir** — yani kararını **daha sonra değiştirebilirsin.** Karmaşık altyapı gereksinimlerin yoksa, **serverless bir platformla başla** — bu, altyapı yerine uygulamana odaklanmanı sağlar. Daha sonra altyapı üzerinde daha fazla kontrole ihtiyaç duyarsan, uygulamanı **daha fazla operasyonel efor gerektiren ama ihtiyacın olan kontrolü/esnekliği sağlayan** bir platforma taşıyabilirsin.

> **Neden bu kadar önemli bir kapanış fikri?** Çünkü birçok kişi "yanlış platformu seçersem her şeyi baştan yazmam gerekir" korkusuyla karar felcine (analysis paralysis) kapılır. Modül bu korkuyu doğrudan gideriyor: Doğru mimari pratiklerle (Cloud Client Library kullanımı, container'laştırma) yazılmış bir uygulama, **platform bağımsızlığını korur.** Bu da "önce basit ve serverless başla, ihtiyaç oldukça daha fazla kontrol gerektiren platforma geç" stratejisini **güvenli ve makul** bir varsayılan yaklaşım haline getiriyor.

---

# Hangi Platformu Ne Zaman Kullanırım? (Karar Tablosu)

| Senaryo | Doğru platform |
| --- | --- |
| Legacy/on-premises sistemleri değiştirmeden buluta taşımak (lift-and-shift) | Compute Engine |
| Belirli donanıma/lisansa bağlı özel yazılım çalıştırmak | Compute Engine |
| Özel işletim sistemi ya da tam VM kontrolüne ihtiyaç duymak | Compute Engine |
| HTTP/HTTPS dışında bir TCP protokolüne ihtiyaç duymak (Cloud Run desteklemiyor) | Compute Engine ya da GKE |
| Büyük, kesintiye toleranslı batch/hesaplama işleri, düşük maliyetle | Compute Engine (Preemptible VM) |
| Container'lı iş yükü, hibrit/çoklu bulut ortamı | GKE |
| Cluster altyapısını (node dahil) tamamen elimden çıkarmak istiyorum | GKE Autopilot |
| Node pool'lar, ağ, güvenlik üzerinde ince ayar kontrolü istiyorum | GKE Standard |
| Stateful uygulama (kalıcı depolama + kararlı ağ kimliği) | GKE (StatefulSet) |
| Stateless container, altyapıyı hiç yönetmek istemiyorum | Cloud Run |
| Sadece kaynak kodumu HTTPS endpoint'ine dönüştürmek istiyorum (build detaylarıyla uğraşmadan) | Cloud Run (source-based deploy, buildpacks) |
| Container imajının build sürecini tam kontrol etmek istiyorum | Cloud Run (container-based deploy) |
| Pub/Sub ya da Eventarc olaylarına tepki veren, tek amaçlı kod | Cloud Run functions |
| Düzenli aralıklarla (ya da tek seferlik) çalışıp biten bir toplu iş | Cloud Run jobs |
| AI çıkarım (LLM), video transkodlama, 3D render — sunucusuz + GPU | Cloud Run (GPU destekli) |
| Trafik düzensiz/dalgalı, boşta kaynağa ödeme yapmak istemiyorum | Cloud Run (pay-per-use) |
| Trafik sabit ve öngörülebilir, öngörülebilir fatura istiyorum | Compute Engine / GKE (dedicated VM ücreti) |
| Ekip tamamen geliştiricilerden oluşuyor, ops ekibi yok | Cloud Run |
| Yeni bir serverless servis kuruyorum (App Engine değil) | Cloud Run |
| GPU/TPU'yu doğrudan tek bir makineye bağlamak istiyorum | Compute Engine |
| GPU/TPU'yu bir container orkestrasyon katmanı içinde, node pool üzerinden kullanmak istiyorum | GKE |

---

# Toplu Özet (Hızlı Tekrar)

**Modülün ana ekseni:** Google Cloud'da compute seçimi tek bir eksen üzerinde düşünülür — **altyapı kontrolü arttıkça operasyonel efor da artar.** Compute Engine bu eksenin "en çok kontrol" ucunda, Cloud Run "en az efor" ucunda, GKE ise ikisi arasında (ve Standard/Autopilot ile kendi içinde de bir yelpazede) durur.

**Compute Engine:** Sanal makineler; geleneksel sunucu deneyimini bulutta taklit eder. Predefined ya da custom makine tipleri, büyürken performansı da artan persistent disk/local SSD, kesintiye toleranslı işler için %60+ indirimli preemptible VM'ler, istediğin işletim sistemi, GPU/TPU doğrudan bağlama. Managed instance group'larla otomatik ölçekleme, sağlık kontrolü, global load balancing kurulabilir — ama bunların **hepsini sen yapılandırırsın.** İdeal kullanım: lift-and-shift, özel lisans/donanım, HTTP-dışı protokoller.

**Google Kubernetes Engine (GKE):** Kubernetes'in (container orkestrasyon platformunun) yönetilen hâli. Control plane node'ları ve pod'ları yönetir; pod = ağ/depolama paylaşan container grubu. **Standard mod** = Google control plane'i yönetir, sen node pool + ağ + güvenliği yönetirsin (esneklik önceliği). **Autopilot mod** = Google her şeyi (control plane + node + node pool) yönetir, sen sadece workload'a odaklanırsın (operasyonel maliyet azaltma önceliği). GKE otomatik: container-optimized OS, AutoUpgrade, auto-repair (drain + recreate), Cloud Monitoring/Logging entegrasyonu, otomatik persistent disk/load balancer/HTTP(S) load balancer provizyonlama. GPU/TPU node pool'lar üzerinden desteklenir. Deployment = stateless, StatefulSet = stateful. `kubectl` ile yönetilir, en iyi pratik YAML manifest kullanmaktır.

**Cloud Run:** Tam yönetilen, sunucusuz konteyner platformu; stateless, isteğe/olaya dayalı iş yükleri için. Sıfırdan-yukarı ve yukarıdan-sıfıra neredeyse anlık ölçekleme; sadece istek işlenirken (100ms hassasiyetle) ödeme. GPU desteği (tam yönetilen, rezervasyonsuz, sıfıra ölçeklenebilir) — LLM inference, video transkod, 3D render için uygun. Dört dağıtım yolu: container (Artifact Registry/Docker Hub'dan), kaynak kod (buildpacks + Cloud Build, Python/Node.js/Go/Java/Ruby/PHP/.NET), fonksiyonlar (Cloud Run servisi olarak dağıtılır), sürekli kaynak dağıtımı (GitHub + Cloud Build). Geliştirici iş akışı: yaz → build/paketle → dağıt → benzersiz HTTPS URL al. **Cloud Run jobs**, servislerden farklıdır: HTTP dinlemez, tek seferlik/zamanlanmış görev olarak çalışır, tamamlanınca çıkış yapar; task'lar paralel çalışabilir, başarısız olan yeniden denenir.

**Karşılaştırma soruları:** (1) Ne kadar kontrol gerekiyor? (2) Operasyonel efor dengesi ne? (3) Ekip yapım nasıl (dev-only vs dev+ops)? (4) Trafik sabit mi düzensiz mi (dedicated VM ücreti vs pay-per-use)?

**App Engine vs Cloud Run:** Cloud Run, App Engine'in deneyimi üzerine inşa edilen daha esnek, daha hızlı ölçeklenen, daha entegre bir sonraki nesil serverless platformdur. **Yeni servisler için Cloud Run tercih edilmelidir.**

**Kararın geri alınabilirliği:** Cloud Client Library'lerle yazılmış uygulamalar platformlar arası kolayca taşınabilir. Karmaşık gereksinim yoksa serverless ile başla, ihtiyaç oldukça daha fazla kontrol gerektiren platforma geç.

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Kontrol ↔ operasyonel efor ekseni:** Compute Engine (en çok kontrol, en çok efor) → GKE (orta) → Cloud Run (en az kontrol, en az efor). Bir platformun "daha güçlü" olması onu "daha iyi" yapmaz — ihtiyaca göre değişir.
- **GKE Standard vs Autopilot:** Standard = Google control plane'i yönetir, **sen node/node pool'u yönetirsin** (esneklik). Autopilot = Google **her şeyi** (control plane + node'lar) yönetir (minimum operasyon). "Node yönetimini de bırakmak istiyorum" → Autopilot.
- **Deployment vs StatefulSet:** Deployment = stateless, replica sayısını sabit tutar. StatefulSet = stateful, kalıcı depolama ve kararlı ağ kimliği gerektiren uygulamalar (örn. veritabanı) için.
- **Cloud Run service vs Cloud Run job:** Service = sürekli HTTP/gRPC isteği dinler, isteğe göre ölçeklenir. Job = HTTP dinlemez, port açmaz, tek seferlik/zamanlanmış görev olarak çalışıp **biter (exits)**; task'lar paralel çalışabilir ve yeniden denenebilir.
- **Cloud Run container-based vs source-based deploy:** Container-based = build sürecinin tam kontrolü sende. Source-based = buildpacks + Cloud Build, senin yerine güvenli/tutarlı bir imaj üretir (Dockerfile varsa onu, yoksa dili otomatik algılar).
- **Cloud Run functions ≠ ayrı bir ürün:** Fonksiyonlar **Cloud Run servisleri olarak** dağıtılır; tek amaçlı, olay güdümlü kod parçalarıdır.
- **Preemptible VM:** Compute Engine'e özgü, en az %60 indirimli, Google tarafından her an sonlandırılabilen (terminate edilebilen) VM'ler — sadece kesintiye toleranslı batch işler için uygundur, sürekli çalışması gereken servisler için değil.
- **Fiyatlandırma mantığı:** Compute Engine/GKE = ayrılmış (dedicated) kaynak ücreti, öngörülebilir, sabit/tutarlı trafik için ideal. Cloud Run = kullandığın kadar öde (pay-per-use), düzensiz/dalgalı trafik için avantajlı.
- **HTTP-dışı protokoller:** Cloud Run bunu desteklemez (temelde HTTP/gRPC'ye dayalıdır) — bu ihtiyaç varsa Compute Engine ya da GKE'ye yönel.
- **GPU/TPU erişimi:** Compute Engine'de **doğrudan tek bir VM'e** bağlanır. GKE'de **node pool üzerinden**, container iş yüklerine paylaştırılır (Standard'da sen node pool'u kurarsın, Autopilot'ta ihtiyacı belirtirsin GKE halleder). Cloud Run'da da artık GPU var — tam yönetilen, rezervasyonsuz, sıfıra ölçeklenebilir, tek instance'a bir GPU.
- **App Engine vs Cloud Run:** Cloud Run, App Engine'in yerini alan modern serverless platformdur; App Engine'in yapamadığı işleri de yapabilir, anında ölçeklenir, varsayılan olarak sadece istek işlenirken ücretlendirir. **Yeni projelerde Cloud Run tercih edilir.**
- **Kararın geri döndürülebilirliği:** Cloud Client Library kullanan uygulamalar platformlar arası taşınabilir — "yanlış platform seçersem her şey biter" korkusu geçersizdir. Basit başla (serverless), ihtiyaç arttıkça kontrol gerektiren platforma geç.

---

> **Kapanış:** Bu modül, "uygulamamı nerede çalıştırmalıyım" sorusuna tek bir cevap vermek yerine, bu soruyu **doğru sormayı** öğretti. Compute Engine'in neden en fazla kontrolü (ve en fazla sorumluluğu) verdiğini, GKE'nin Kubernetes'in gücünü nasıl yönetilebilir hale getirdiğini (Standard ile esneklik, Autopilot ile sıfır operasyon arasında seçim sunarak), ve Cloud Run'ın neden "sadece kodunu yaz, gerisini bırak" felsefesinin en olgun hâli olduğunu gördün. Bu üçünü karşılaştırırken kullandığın dört soru — kontrol ihtiyacı, operasyonel efor, ekip yapısı, fiyatlandırma modeli — artık senin kendi projelerinde de kullanabileceğin kalıcı bir karar çerçevesi. Ve en önemlisi, bu kararın **taş üstüne taş değil, geri alınabilir bir tercih** olduğunu unutma: Cloud Client Library'lerle doğru yazılmış bir uygulama, ihtiyaç değiştikçe platformdan platforma taşınabilir. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; özellikle Standard/Autopilot ayrımını, Cloud Run service/job farkını ve "yeni servis = Cloud Run, App Engine değil" kuralını aklında tut.
