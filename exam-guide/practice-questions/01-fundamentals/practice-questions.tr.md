# Modül 1 — Temeller: Uygulama Soruları

"Google Cloud Fundamentals: Core Infrastructure" modülü için senaryo tabanlı uygulama soruları. Bu sorular [derinlemesine öğreticiden](../../../deep-dive/01-fundamentals/fundamentals.md) alınmıştır ve içindeki sınav tuzağı uyarılarına ve karar tablolarına — yani gerçek sınavda insanların en çok kaçırdığı ayrımlara — ağırlık verir.

Önce 15 sorunun tamamını cevapla, sonra aşağıdaki [Cevap Anahtarı ve Açıklamalar](#cevap-anahtarı-ve-açıklamalar) bölümünü kontrol et.

---

## Sorular

**1.** Bir şirket içi platform ekibi self-servis bir portal kurar: çalışanlar bir web arayüzünden VM'leri anında (hiçbir talep formu, hiçbir onay olmadan) sağlar, platforma VPN üzerinden her yerden erişir, paylaşılan bir sunucu havuzundan kaynak alır ve bir iş yükünü dakikalar içinde büyütüp küçültebilir. Ancak her ekip, gerçekte ne kadar tükettiğinden bağımsız olarak sabit bir üç aylık ücret öder. NIST tanımını sıkı sıkıya uygularsak, hangi temel bulut özelliği eksiktir?

A) Geniş ağ erişimi — platform VPN gerektiriyor
B) Kaynak havuzu — sunucuların çok kiracılı (multi-tenant) olduğu belirtilmemiş
C) Ölçülen hizmet (kullandığın kadar öde) — maliyet gerçek kullanımı takip etmiyor
D) Elastiklik — platformun geri küçülebildiğini doğrulayan bir şey yok

**2.** Bir ekip, yeni bir şirket içi araç için Compute Engine ile App Engine arasında seçim yapıyor. Bir mühendis şunu iddia ediyor: "Hangisini seçersek seçelim fark etmez — her iki durumda da sadece kodumuz çalışırken tükettiğimiz işlem gücü için öderiz." Bu doğru mu?

A) Doğru — IaaS ve PaaS aynı şekilde faturalandırılır
B) Yanlış — Compute Engine (IaaS) kullanılsın ya da kullanılmasın, ayırdığın (allocate ettiğin) kapasite için faturalandırır; App Engine (PaaS) gerçekten tükettiğin kaynak için faturalandırır
C) Yanlış — App Engine ayrılan kapasite için faturalandırır; Compute Engine yalnızca gerçek kullanım için faturalandırır
D) Yanlış — ikisi de yalnızca bir geliştirici elle bir build tetiklediğinde faturalandırır

**3.** Bir girişim yeniden markalanıyor ve Google Cloud projesinin konsolda yeni markayı yansıtmasını istiyor; ama projeye referans veren her CI/CD betiği, faturalandırma dışa aktarımı ve IAM bağlaması değişmeden çalışmaya devam etmeli. Ne yapmalılar?

A) Project ID'sini yeni marka adına çevirsinler — ID'ler sadece etikettir, hiçbir şeyi bozmaz
B) Yalnızca proje adını (project name) değiştirsinler; proje bir kez oluşturulduktan sonra project ID değişmezdir (immutable), bu yüzden ona referans veren betikler etkilenmez
C) Hem project ID'yi hem de project number'ı değiştirsinler; yalnızca ad sabittir
D) Yeni markayı yansıtması için project number'ı değiştirsinler

**4.** Bir toplu iş (batch) hattı, GKE API'sini art arda çağırdığında yoğun anlarda kısıtlanıyor (throttle) ama kısa süre sonra kendiliğinden düzeliyor. Ayrı bir olayda, aynı ekip tek bir projede 21. VPC ağını oluşturmaya çalışıyor ve ne kadar beklerlerse beklesinler kesin olarak reddediliyor. Her iki durum da hangi kota türüne çarpıyor?

A) İkisi de tahsis (allocation) kotası
B) İkisi de oran (rate) kotası
C) API kısıtlaması bir oran kotasıdır (bir zaman penceresi sonra sıfırlanır); VPC ağı sınırı bir tahsis kotasıdır (kaç adet tutabileceğini sınırlar)
D) API kısıtlaması bir tahsis kotasıdır; VPC ağı sınırı bir oran kotasıdır

**5.** Bir organizasyon yöneticisi, organizasyon düğümünde kullanıcı X'e Cloud Storage bucket'larını silme iznini reddeden bir IAM deny politikası tanımlıyor. Ayrı bir olayda, deny politikasından habersiz bir proje sahibi, kullanıcı X'e belirli bir bucket üzerinde doğrudan Storage Admin rolünü veriyor; bu rol silme iznini de içeriyor. Kullanıcı X o bucket'ı silebilir mi?

A) Evet — daha spesifik olan bucket düzeyindeki allow bağlaması, daha geniş kapsamlı organizasyon düzeyindeki deny'ı geçersiz kılar
B) Hayır — IAM her zaman önce deny politikalarını, sonra allow politikalarını kontrol eder; bu yüzden organizasyon düzeyindeki deny, nerede verilmiş olursa olsun allow'u geçersiz kılar
C) Hangi politikanın önce oluşturulduğuna bağlıdır
D) Evet — deny politikaları yalnızca önceden tanımlı (predefined) rolleri kısıtlar, doğrudan verilen izinleri değil

**6.** Bir güvenlik ekibi, bir yüklenicinin (contractor) tek bir klasör altında gruplanmış üç projede yalnızca Compute Engine VM'lerini başlatıp durdurabilmesini istiyor — başka hiçbir şey değil. Şu seçenekleri tartıyorlar: (a) klasörde tanımlanan özel (custom) bir rol, (b) klasörde uygulanan önceden tanımlı (predefined) Compute Instance Admin rolü, ya da (c) her projede temel (basic) Editor rolü. Hangi yaklaşım gerçekten işe yarar?

A) Klasör düzeyinde özel bir rol, çünkü ihtiyaç duyulan iki izni tam olarak tanımlamanı sağlar
B) Klasör düzeyinde uygulanan önceden tanımlı rol — özel roller yalnızca proje veya organizasyon düzeyine uygulanabilir, asla klasör düzeyine uygulanamaz
C) Her projede temel Editor rolü, çünkü basic roller dar senaryolar için en basitidir
D) Hiyerarşi düzeyi ne olursa olsun, en az ayrıcalık için özel rol her zaman doğru seçimdir

**7.** Bir ekip, CI hattında kullanılan Service Account'ı kimin taklit edebileceğini (impersonate) Alice'in kontrol etmesini, Bob'un ise yalnızca o Service Account'ı hangi VM'lerin kullandığını görebilmesini istiyor — ek Service Account'lar oluşturmadan. Bu mümkün mü?

A) Hayır — Service Account'lar saf kimliklerdir ve onlara IAM politikaları eklenemez
B) Evet — bir Service Account aynı zamanda bir kaynaktır da; bu yüzden Alice'e Service Account'ın kendisi üzerinde editor düzeyinde bir rol, Bob'a ise viewer düzeyinde bir rol doğrudan verilebilir
C) Yalnızca biri Alice, diğeri Bob için olmak üzere iki ayrı Service Account oluşturarak mümkündür
D) Hayır — bir Service Account'a erişimi yalnızca Organization Administrators yönetebilir

**8.** Bir şirketin `vpc1` adlı tek bir VPC ağı ve `asia-east1` bölgesinde tanımlanmış tek bir alt ağı (subnet) var. Bu şirket, `asia-east1` içindeki iki farklı zonda VM'ler dağıtıyor ve ikisi de aynı alt ağa bağlı. Yeni bir mühendis bunun yanlış yapılandırıldığını düşünüyor çünkü "farklı zonlardaki VM'ler aynı alt ağı paylaşamaz." Bu mühendis haklı mı?

A) Evet — alt ağlar zonaldir, bu yüzden her zonun kendi alt ağına ihtiyacı vardır
B) Hayır — bir alt ağ bölgeseldir ve o bölgedeki her zonu kapsayabilir; bu yüzden `asia-east1`'in farklı zonlarındaki VM'ler aynı alt ağı paylaşabilir
C) Hayır — VPC ağları zonaldir ama alt ağlar küreseldir
D) Evet, Shared VPC özellikle etkinleştirilmediği sürece

**9.** Bir e-ticaret uygulaması, `/api` isteklerini bir arka uca, `/images` isteklerini farklı bir arka uca yönlendirmeli — hepsi HTTPS üzerinden, kenarda SSL/TLS sonlandırmasıyla. Hangi load balancer türü buna uyar?

A) Passthrough Network Load Balancer, çünkü kaynak IP'yi korur
B) Application Load Balancer, çünkü içerik tabanlı yönlendirme ve SSL/TLS sonlandırma Katman 7'de gerçekleşir
C) Proxy Network Load Balancer, çünkü Katman 4'te çalışır
D) İkisi de aynı OSI katmanında çalıştığı için ikisi de aynı derecede uyar

**10.** Bir oyun şirketi, doğrudan sunucu dönüşü (direct server return) gerektiren ve anti-cheat coğrafi konumlandırma için her istemcinin gerçek kaynak IP adresini görmesi gereken UDP tabanlı bir çok oyunculu arka uç çalıştırıyor. Hangi load balancer türünü kullanmalılar?

A) Application Load Balancer, çünkü herhangi bir protokolü işleyebilir
B) Proxy Network Load Balancer, çünkü gelişmiş trafik yönetimi sunar
C) Passthrough Network Load Balancer, çünkü bağlantıyı sonlandırmaz ve orijinal istemci IP'sini korur
D) Cloud CDN, çünkü trafiği ağ kenarında önbelleğe alır

**11.** Bir veri bilimi ekibi haftada birkaç kez toplu (batch) bir analiz işi çalıştırıyor. Her çalıştırma yaklaşık dört saat sürüyor, garanti bir başlangıç zamanına ihtiyaç duymuyor ve kesintiye uğrarsa en son checkpoint'ten devam edebiliyor. Bu senaryoda maliyeti en aza indiren Compute Engine fiyatlandırma seçeneği hangisidir?

A) 1 yıllık vCPU/bellek taahhüdüyle taahhütlü kullanım (committed-use) indirimi
B) Standart fiyata göre %90'a varan tasarruf karşılığında sonlandırılma (preemption) riskini kabul eden bir Spot VM
C) Ay içindeki kullanım %25'i aştığında otomatik uygulanan sürekli kullanım (sustained-use) indirimi
D) vCPU/bellek ince ayarı her zaman en büyük indirimi verdiği için özel bir makine tipi

**12.** Bir finans ekibi Cloud Storage'da iki tür nesne saklıyor: (1) yaklaşık ayda bir geri yüklemesi gerekebilecek aylık veritabanı yedekleri ve (2) yılda birden az, belki de hiç erişmeyecekleri yedi yıllık düzenleyici arşiv kayıtları. Bu ikisine sırasıyla hangi depolama sınıfları uyar?

A) İkisi için de Standard, çünkü uyumluluk açısından hassas veri için en güvenlisi Standard'dır
B) Yedekler için Nearline, düzenleyici kayıtlar için Archive
C) Yedekler için Coldline, düzenleyici kayıtlar için Nearline
D) Yedekler için Archive, düzenleyici kayıtlar için Standard

**13.** Bir analitik ekibi günde yaklaşık 2 TB IoT sensör verisi alıyor. Cihaz ID'si ve zaman damgasıyla anahtarlanmış 10 milisaniyenin altında sorgulara ihtiyaçları var ama SQL join'lerine veya çok satırlı işlemlere (multi-row transaction) ihtiyaçları yok. Spanner ile Bigtable arasında karar veriyorlar. Doğru seçim nedir ve dikkat edilmesi gereken tuzak ne?

A) Spanner, çünkü güçlü tutarlılık sunar
B) Bigtable, çünkü devasa, yarı yapılandırılmış, zaman serisi tarzı veride üstündür — ama SQL join'lerini ve çok satırlı işlemleri desteklemediğini unutma
C) Cloud SQL, çünkü IoT telemetri verisi doğası gereği ilişkiseldir
D) Firestore, çünkü IoT cihazları için çevrimdışı senkronizasyon destekler

**14.** Küçük bir ekip GKE üzerinde konteynerli iş yükleri çalıştırmak istiyor. Özel bir altyapı mühendisleri yok ve Google'ın node sağlama, otomatik ölçekleme, yükseltmeler ve temel güvenliği yönetmesini istiyorlar — sadece her iş yükünün neye ihtiyacı olduğunu tanımlamak istiyorlar. Hangi GKE modu buna uyar ve alternatife kıyasla neyden vazgeçiyorlar?

A) GKE Standard, çünkü tam node düzeyinde kontrol verir
B) GKE Autopilot — Google, node yapılandırmasını, otomatik ölçeklemeyi, yükseltmeleri ve temel güvenlik duruşunu senin yerine yönetir; bunun bedeli bir miktar ince ayarlı yapılandırma kontrolüdür
C) Hiçbiri — Cloud Run, Google Cloud'daki tek serverless konteyner seçeneğidir
D) GKE Standard, çünkü Autopilot üretim iş yükleri için tasarlanmamıştır

**15.** Bir uygulamanın kullanıcı isteklerini sürekli karşılayan uzun ömürlü bir HTTP API'si var, ayrıca Cloud Storage bucket'ına yeni bir dosya geldiğinde (küçük resim/thumbnail oluşturma) çalışması gereken küçük bir mantık parçası var. Uygun servis dağılımı nedir?

A) İkisi için de Cloud Run — Cloud Run functions kullanımdan kaldırıldı
B) İkisi için de Cloud Run functions, çünkü serverless olan her şey bir fonksiyon olmalı
C) Uzun ömürlü HTTP API için Cloud Run (istekleri dinleyen durumsuz bir konteyner); Cloud Storage olayıyla tetiklenen tek amaçlı thumbnail üretici için Cloud Run functions
D) Olay güdümlü olduğu için thumbnail üreticisi için Cloud Run; serverless olduğu için API için Cloud Run functions

---

## Cevap Anahtarı ve Açıklamalar

**1. Cevap: C.** Senaryo, tüketimden bağımsız sabit bir ücret olduğunu açıkça belirtiyor; bu doğrudan "sadece kullandığın kadar öde" özelliğiyle çelişir. D seçeneği çekici bir tuzaktır — metin iş yükünün "büyüyüp küçülebildiğini" açıkça söylüyor, yani elastiklik aslında sağlanmış durumda; tuzak, faturalandırma detayını gözden kaçırmaktır.

**2. Cevap: B.** IaaS (Compute Engine), boşta olsun ya da olmasın ayırdığın kapasite için faturalandırır; PaaS (App Engine) gerçekten tükettiğin kaynak için faturalandırır. A seçeneği çekici-ama-yanlış cevaptır çünkü "bulut = sadece kullandığın kadar öde" mantığının her yerde geçerli olduğu varsayılabilir — ama bu özellik özellikle PaaS/serverless'a özgüdür, IaaS'a değil.

**3. Cevap: B.** Proje oluşturulduktan sonra project ID değişmezdir (immutable); üç tanımlayıcıdan yalnızca project name serbestçe değiştirilebilir. A seçeneği klasik tuzaktır — project ID'yi, her betiğin ve entegrasyonun bağlı olduğu kalıcı bir tutamaç yerine kozmetik bir etiket gibi ele alır.

**4. Cevap: C.** Oran (rate) kotaları belirli bir zaman penceresi sonra sıfırlanır (örneğin GKE'nin varsayılan olarak her 100 saniyede 3.000 çağrı sınırı); tahsis (allocation) kotaları bir kaynaktan aynı anda kaç tane tutabileceğini sınırlar (örneğin proje başına varsayılan 15 VPC ağı). Her iki kota türü de proje düzeyinde uygulanır; bu da "zamanla sıfırlanma" ile "kesin sınır"ı karıştırırsan B ve D'yi çekici kılar.

**5. Cevap: B.** IAM her zaman önce deny politikalarını, sonra allow politikalarını kontrol eder ve ikisi de kaynak hiyerarşisinde aşağı doğru miras alınır — bu yüzden organizasyon düzeyindeki bir deny, daha sonra ve daha spesifik verilmiş bucket düzeyindeki bir allow'u geçersiz kılar. A seçeneği çekici bir tuzaktır çünkü "daha spesifik olan kazanır" başka izin sistemlerinde yaygın bir kalıptır, ama IAM'in önce-deny değerlendirmesinde geçerli değildir.

**6. Cevap: B.** Önceden tanımlı (predefined) roller bir projeye, bir klasöre veya bir organizasyona uygulanabilir; bu yüzden önceden tanımlı Compute Instance Admin rolünü klasörde uygulamak tam ihtiyaç duyulan şeydir. A seçeneği tuzaktır: özel bir rol en isabetli araç gibi görünür, ama özel roller yalnızca proje veya organizasyon düzeyine uygulanabilir — asla klasör düzeyine.

**7. Cevap: B.** Bir Service Account'ın ikili doğası vardır: hem bir kimlik olarak davranır (CI hattı için) hem de kendi IAM bağlamalarını taşıyabilen bir kaynaktır — bu yüzden Alice Service Account'ın kendisi üzerinde editor tipi bir rol, Bob ise viewer tipi bir rol alabilir. A seçeneği çekicidir çünkü Service Account'lar genellikle sadece "kimlik doğrulayan şeyler" olarak düşünülür ve kaynak yönleri gözden kaçırılır.

**8. Cevap: B.** Alt ağlar bölgeseldir ve o bölgedeki her zonu kapsayabilir; bu yüzden aynı bölgenin farklı zonlarındaki VM'lerin aynı alt ağa bağlı olması tamamen normaldir ve aynı ağ segmentinde sayılırlar. A seçeneği, yeni Google Cloud kullanıcılarının VPC ağı hakkındaki en yaygın yanlış kanısıdır.

**9. Cevap: B.** İçerik tabanlı yönlendirme (`/api` ile `/images`) ve SSL/TLS sonlandırma Katman 7 davranışlarıdır; bu tam olarak Application Load Balancer'ın sunduğu şeydir. C seçeneği çekicidir çünkü "proxy" kelimesi gelişmiş gibi görünür, ama Proxy Network Load Balancer Katman 4'te çalışır ve HTTP path tabanlı yönlendirme yapmaz.

**10. Cevap: C.** Passthrough Network Load Balancer bağlantıyı sonlandırmaz ya da değiştirmez; bu yüzden istemcinin orijinal kaynak IP'sini korur ve doğrudan sunucu dönüşünü destekler — anti-cheat ve UDP gereksinimlerinin tam istediği şey budur. B seçeneği tuzaktır: Proxy Network Load Balancer da TCP/UDP ailesindeki trafiği işler, ama istemci bağlantısını sonlandırıp arka uca yeni bir bağlantı açtığı için orijinal kaynak IP korunmaz.

**11. Cevap: B.** Kesintiyi tolere eden ve garanti bir başlangıç zamanına ihtiyaç duymayan bir toplu iş, ders kitabı düzeyinde bir Spot VM kullanım örneğidir ve %90'a varan tasarruf sağlar. A seçeneği çekicidir çünkü taahhütlü kullanım indirimleri de önemli tasarruf sağlar, ama bunlar kararlı, öngörülebilir, uzun süre çalışan iş yükleri içindir — sabit programı olmayan, kesintiye toleranslı bir iş için değil.

**12. Cevap: B.** Nearline, ayda bir veya daha seyrek erişilen veriyi hedefler (yedeklere uyar) ve Archive, yılda birden az erişilen ve 365 günlük minimum saklama süresi olan veriyi hedefler (çok yıllı düzenleyici kayıtlara uyar). C seçeneği çekicidir çünkü Coldline (~90 günlük erişim deseni) ikisinin arasında durur, ama tarif edilen erişim desenlerinden hiçbirine Nearline ve Archive kadar tam uymaz.

**13. Cevap: B.** Bigtable, zaman serisi IoT verisi gibi devasa yarı yapılandırılmış veri kümelerinde yüksek verimli, düşük gecikmeli, anahtar tabanlı erişim için tam olarak tasarlanmıştır — ama SQL join'lerini ve çok satırlı işlemleri desteklemediği açıkça belirtilir. A seçeneği çekicidir çünkü Spanner de yatay ölçeklenir, ama Spanner join'lere, ikincil indekslere ve güçlü küresel tutarlılığa ihtiyaç duyduğunda tercih edilen, daha ağır, ilişkisel bir seçenektir — basit, yüksek verimli bir anahtar-değer deseni için değil.

**14. Cevap: B.** GKE Autopilot; node yapılandırmasını, otomatik ölçeklemeyi, yükseltmeleri ve temel güvenliği senin yerine yönetir — bu da özel altyapı personeli olmayan bir ekibin tam olarak ihtiyacı olan şeydir; bedeli, Standard'a kıyasla azalan node düzeyinde yapılandırma kontrolüdür. A seçeneği ödünleşimi tersine çevirir: Standard daha fazla kontrol verir ama node'ları ekibin kendisinin yönetmesini gerektirir, bu da senaryonun istediğiyle çelişir.

**15. Cevap: C.** Cloud Run, istekleri dinleyen tam, durumsuz, uzun ömürlü bir konteyner çalıştırır (HTTP API); Cloud Run functions, Cloud Storage'a yeni bir nesnenin gelmesi gibi bir olayla tetiklenen küçük, tek amaçlı mantık içindir (thumbnail üretici). D seçeneği tuzak cevaptır — iki servisi, her birinin gerçekte ne için tasarlandığına bakmadan, "olay güdümlü" ve "serverless" kelimelerinin yüzeysel okumasına dayanarak yer değiştirir.
