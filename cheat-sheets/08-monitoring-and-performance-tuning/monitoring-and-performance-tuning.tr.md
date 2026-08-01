# Modül 8 – İzleme ve Performans Ayarı

> Uygulamanı üretime aldıktan sonra asıl iş başlıyor: sağlıklı mı, neden yavaş, neden bozuldu — bunları bilmek.

---

# 🎯 Genel Bakış

Bir uygulamayı deploy etmek işin sadece yarısıdır.

Diğer yarısı, uygulamanın sağlıklı olup olmadığını, neden yavaşladığını ve neden bozulduğunu bilmektir.

Google Cloud bu görünürlüğü tek bir şemsiye altında topluyor: **Google Cloud Observability.**

```text
Metrikler

+

Loglar

+

Metadata

↓

Google Cloud Observability
```

Bu araç seti sadece Google Cloud'a özel değildir; başka bulutlarda ve on-premises ortamlarda da çalışır.

Bu modülde ele alınan beş araç:

- Cloud Monitoring
- Cloud Logging
- Error Reporting
- Cloud Trace
- Cloud Profiler

Prometheus ve Google Cloud Managed Service for Prometheus da Logging/Monitoring hikâyesinin bir parçası olarak işleniyor.

---

# Observability Zinciri

Beş aracı ayrı ürünler gibi değil, birbirini tamamlayan bir zincir gibi düşün.

```text
Cloud Monitoring

↓ (bir şey ters gidiyor)

Cloud Logging

↓ (ne oldu, detaylarıyla)

Error Reporting

↓ (hangi hata, kaç kez)

Cloud Trace

↓ (isteğin hangi adımı yavaş)

Cloud Profiler

↓ (hangi kod satırı pahalı)
```

Monitoring sorunu fark eder. Logging bağlam verir. Error Reporting hataları özetler ve gruplar. Trace yavaş adımı bulur. Profiler pahalı kodu bulur.

---

# Cloud Monitoring

Cloud Monitoring, uygulama güvenilirliğinin temelidir.

Google Cloud servislerinden ve uygulamalardan metrikleri, olayları ve metadata'yı toplar; alerting policy'ler tanımlamanı sağlar.

```text
Uygulama

↓

Metrikler / Olaylar / Metadata

↓

Cloud Monitoring

↓

Dashboard'lar + Alertler
```

## Geliştiricilerin Kullandığı Üç Yöntem

- **Dashboard'lar** – zaman içindeki trendleri takip etmek için (veritabanı büyümesi, günlük aktif kullanıcı, özellik kullanımı).
- **Alerting** – bir metrik tehlikeli bir eşiği aşmadan önce haberdar olmak için.
- **Retrospektif analiz** – bir olaydan sonra "aynı anda başka ne oldu" sorusunu cevaplamak için.

---

## Four Golden Signals

Her uygulama dashboard'unun izlemesi gereken dört temel sinyal vardır.

| Sinyal | Ne ölçer? | Örnek metrik | Dikkat! |
| --- | --- | --- | --- |
| Latency | İsteği karşılama süresi | ms cinsinden yanıt süresi | Başarılı ve başarısız istekleri **ayrı** ölç |
| Traffic | Sisteme binen talep | HTTP req/sn, DB read-write/sn | Doğru birim sisteme göre değişir |
| Errors | Başarısız istek sayısı | HTTP 5xx, yanlış içerikli 200, SLA ihlali | "Başarılı" bir HTTP 200 bile hata olabilir |
| Saturation | Sistemin ne kadar dolu olduğu | CPU/bellek/disk kullanımı | Performans **%100'e ulaşmadan önce** bozulabilir |

Latency örneği:

```text
İstek

↓

Backend bağlantısı koptu

↓

Anında başarısız olur (hızlı ama 500 hatası)
```

Hızlı başarısız olan bir istek de yine bir başarısızlıktır. Başarısız istekleri gecikme ortalamasına karıştırmak, gerçek sorunu gizler.

Hatalar sadece HTTP 5xx kodlarından ibaret değildir:

- Açık hata — HTTP 500
- 200 yanıtı ama içerik yanlış
- Politika hatası — yanıt, taahhüt edilen SLA'dan uzun sürdü, hiçbir hata kodu olmasa bile

---

# Cloud Logging

Cloud Logging; depolama, arama, analiz ve uyarı desteğine sahip, gerçek zamanlı bir log yönetim sistemidir.

Google Cloud kaynaklarından logları otomatik toplar; kendi uygulamalarından da log toplayabilirsin.

```text
Uygulama

↓

stdout / stderr

↓

Cloud Logging
```

## Logs Explorer vs Log Analytics

```text
Logs Explorer → tekil log kayıtları

Log Analytics → toplu log verisi üzerinde SQL
```

- **Logs Explorer** – tekil log kayıtlarını ve ilişkili kayıtları görüntüler, tek bir isteği takip etmek için idealdir.
- **Log Analytics** – logların üzerinde SQL sorgularıyla toplu analiz yapmanı sağlar.

## Log-based Alert vs Log-based Metric

```text
Desen bir kez oluşur

↓

Log-based alert

Desen N kez oluşur / trend

↓

Log-based metric
```

- **Log-based alert** – belirli bir desen log girdisinde göründüğü anda bildirim gönderir.
- **Log-based metric** – zaman içinde oluşumları sayar, eşik aşıldığında uyarır ya da bir trendi izler.

## Ops Agent

Ops Agent, Compute Engine VM'lerinde çalışan üçüncü taraf uygulamalardan log ve metrik toplar.

```text
Ops Agent

├── Fluent Bit               → log toplama
└── OpenTelemetry Collector  → metrik toplama
```

- Standart konumlardan (`/var`, `/log`, `/syslog`) logları otomatik toplar.
- Esnek işleme sunar: metni structured log'a dönüştürme, alan ekleme/kaldırma/yeniden adlandırma, etiket/regex ile hariç tutma.
- Sıfır konfigürasyonla standart sistem metriklerini toplar: CPU, disk, bellek, ağ, süreçler.
- Küratörlenmiş üçüncü taraf metriklerini toplar: Apache Tomcat, Apache web server, NGINX.

## Ortama Göre Hazır Gelen Logging

- **Cloud Run** servisleri ve fonksiyonlarında logging yerleşiktir — stdout/stderr otomatik olarak Cloud Logging'e gider.
- **GKE**'de cluster üzerinde "observability for GKE" entegrasyonunu etkinleştirmen gerekir.

## GKE'de Log Kalıcılığı

Kubernetes logları kendiliğinden kalıcı değildir.

```text
Pod silinir

↓

Container logları kaybolur

Sistem logları

↓

Periyodik olarak temizlenir

Cluster events

↓

1 saat sonra kaldırılır
```

Logları saklamak istiyorsan Cloud Logging'e göndermelisin.

## Text Log vs Structured Log

| Özellik | Text log | Structured (JSON) log |
| --- | --- | --- |
| Depolandığı alan | `textPayload` | `jsonPayload` |
| Log seviyesi | Yok | `severity` alanı ile var |
| Aranabilirlik | Zor (düz metin arama) | Kolay (alan bazlı sorgu) |
| Ana görüntü metni | Tüm metin | `message` alanı |
| Önerilir mi? | Sadece basit/hızlı senaryolar için | Genel olarak evet |

Structured logging önerilir: sorgulanabilir bir `severity` seviyesi ve aranabilir alanlar sunar, düz bir metin dizesi değil.

---

# Prometheus ve Managed Service for Prometheus

Prometheus, açık kaynaklı bir sistem izleme ve uyarı araç setidir.

```text
VM'ler / Kubernetes

↓

Prometheus

↓

Zaman Serisi Verisi

↓

PromQL
```

Kubernetes iş yüklerini ve cluster'larını izlemek için popülerdir, ama ölçekte kendin yönetmek operasyonel olarak ağırdır.

**Google Cloud Managed Service for Prometheus** bu yükü ortadan kaldırır.

- Çoklu-bulut ve projeler-arası çalışır — Cloud Monitoring üzerinden single pane of glass sağlar.
- **Managed collectors** – tüm Kubernetes ortamları (GKE dahil) için önerilir; Prometheus'un işletilmesini Kubernetes operatörü üstlenir.
- **Self-deployed collectors** – standart Prometheus binary'sinin doğrudan yerine geçer.
- OpenTelemetry collector'ları ve Ops Agent de veri kaynağı olarak kullanılabilir.
- PromQL destekleyen her sorgu aracı çalışır — Cloud Monitoring ve Grafana dahil.
- 1.500'den fazla ücretsiz metrik, hiç veri göndermeden bile sorgulanabilir.
- Hem Cloud Monitoring hem Prometheus metrikleri için tamamen bulut-tabanlı bir alerting pipeline sağlar.

---

# Error Reporting

Error Reporting, çalışan cloud servislerindeki çökmeleri sayar, analiz eder ve toplar.

```text
Uygulama Hatası

↓

Error Reporting

↓

Gruplanmış + Deduplike Edilmiş

↓

Error Details Dashboard
```

## Hatalar Nasıl Tespit Edilir?

- **Açıkça raporlanır** – Error Reporting API kullanılarak.
- **Çıkarım yoluyla tespit edilir** – Error Reporting, loglardaki stack trace gibi yaygın desenleri tarar.

## Gruplama

Hata olayları, stack trace analiziyle akıllıca gruplanır ve deduplike edilir; böylece tekrar eden aynı hata yerine gerçekten farklı hataları görürsün.

Konsol, her hata grubu için şunları gösterir:

- Oluşum sayısı
- İlk ve son görülme zamanı
- Etkilenen kullanıcı sayısı

Yeni bir hata oluştuğunda e-posta ve mobil bildirim almayı tercih edebilirsin (opt-in).

## Ortama Göre Etkinleştirme

| Ortam | Gereken adım |
| --- | --- |
| Cloud Run | Otomatik |
| GKE | `cloud-platform` access scope ekle |
| Compute Engine | VM Service Account'a Error Reporting Writer rolü ver |

Desteklenen diller: Go, Java, Node.js, PHP, Python, Ruby, .NET.

Raporlama asenkrondur — kodun, hata olayının teslimini beklemeden çalışmaya devam eder.

Error Details sayfasındaki **View Logs** ile doğrudan Logs Explorer'a geçip tam bağlamı görebilirsin.

---

# Cloud Trace

Cloud Trace, uygulamalarından gecikme verisi toplayan ve near-real-time gösteren dağıtık bir izleme sistemidir.

```text
İstek

↓

Servis A (span)

↓

Servis B (span)

↓

Veritabanı (span)

↓

Trace = A + B + Veritabanı
```

## Trace vs Span

- **Trace**, tek bir işlemin tamamlanma süresini tanımlar.
- **Span**, trace içindeki bir alt-işlemin tamamlanma süresini tanımlar.
- Bir trace, bir ya da daha fazla span'dan oluşur.

## Veri Göndermenin İki Yolu

```text
Otomatik izleme → sadece Cloud Run giren/çıkan HTTP

Enstrümantasyon → tam dahili detay (OpenTelemetry + Cloud Trace Exporter)
```

- **Otomatik izleme** – Cloud Run servisleri ve fonksiyonları, giren/çıkan HTTP isteklerinin gecikme verisini otomatik toplar. Servis içindeki gecikmeyi göstermez.
- **Enstrümantasyon** – servis içi detayı görmek için OpenTelemetry + Cloud Trace Exporter (önerilen) ya da Cloud Trace API/client library kullanılır.

## Trace Explorer

- Scatter plot: her istek için bir nokta, x = zaman, y = gecikme.
- Method ya da status code gibi özniteliklere göre filtreleme.
- Bir noktaya tıklayınca Trace Details paneli açılır ve span'ları gösterir.

Cloud Trace ayrıca en çok kullanılan endpoint'ler için, dünün performansını geçen haftanın aynı günüyle karşılaştıran bir günlük rapor otomatik oluşturur; özel analiz raporları da kurabilirsin.

---

# Cloud Profiler

Üretim performansını anlamak ünlü şekilde zordur — test ortamı, üretimin gerçek davranışını nadiren yeniden yaratır.

Cloud Profiler; üretim uygulamalarından CPU kullanımı ve bellek tahsisini sürekli toplayan, istatistiksel ve düşük ek yüklü bir profildir; bu veriyi üreten kaynak koduna atfeder.

```text
Tüm Üretim Örnekleri

↓

Örnekleme (istatistiksel)

↓

CPU + Bellek kullanımı

↓

Kaynak koduna atfedilir
```

İstatistiksel teknikler ve düşük etkili enstrümantasyon kullanır; bu sayede uygulamayı yavaşlatmadan üretimde sürekli açık kalabilir.

Cloud Trace vs Cloud Profiler:

```text
Cloud Trace    → isteğin hangi adımı yavaş?  (zaman ekseni)

Cloud Profiler → hangi kod fonksiyonu pahalı? (kod ekseni)
```

---

# Beş Aracın Karşılaştırması

| Araç | Neyi toplar/gösterir? | Temel soru | Ne zaman kullanılır? |
| --- | --- | --- | --- |
| Cloud Monitoring | Metrikler, olaylar, metadata; dashboard'lar, uyarılar | Sistem genel olarak sağlıklı mı? | Sürekli sağlık takibi, trend izleme, eşik alarmları |
| Cloud Logging | Ham log kayıtları (text/structured); Log Analytics ile SQL | Ne oldu, detaylarıyla? | Bir olayın tam bağlamını incelerken |
| Error Reporting | Gruplanmış, deduplike edilmiş hatalar; stack trace'ler | Hangi hata, kaç kez, ne zamandır? | Üretim hatalarını önceliklendirirken |
| Cloud Trace | İstek bazlı gecikme, trace/span zaman çizelgesi | İsteğin hangi adımı yavaş? | "Bu istek neden bu kadar sürdü?" sorusuna cevap ararken |
| Cloud Profiler | Kaynak koduna atfedilmiş CPU/bellek kullanımı | Hangi kod satırı kaynak tüketiyor? | "Uygulamam neden bu kadar CPU/bellek harcıyor?" sorusuna cevap ararken |

---

# Modül Özeti

Bu modülde Google Cloud Observability şemsiyesi altındaki beş aracı öğrendik.

**Cloud Monitoring**, güvenilirliğin temelidir: dashboard'larla trend takibi, uyarı politikalarıyla erken tespit ve retrospektif analiz sağlar. Four Golden Signals — latency, traffic, errors, saturation — her dashboard'da bulunmalıdır ve her birinin dikkat edilmesi gereken bir inceliği vardır.

**Cloud Logging**, tekil kayıt incelemesini (Logs Explorer) toplu SQL analizinden (Log Analytics) ayırır; log-based alert ile log-based metric farklı amaçlara hizmet eder. Structured (JSON) logging, düz metin loglamaya tercih edilmelidir çünkü aranabilir ve seviye taşır. Ops Agent, Fluent Bit (log) ve OpenTelemetry Collector (metrik) ile Compute Engine VM'lerini enstrümante eder. Kubernetes logları varsayılan olarak kalıcı değildir — saklamak için Cloud Logging'e göndermelisin.

**Prometheus**, güçlü ama kendin yönetmesi zor bir araçtır; **Managed Service for Prometheus** bu yükü ortadan kaldırırken PromQL'i korur.

**Error Reporting**, çökmeleri stack trace analiziyle otomatik olarak gruplar ve deduplike eder; etkinleştirme adımı compute platformuna göre değişir.

**Cloud Trace**, bir istekte zamanın nereye gittiğini trace ve span'lar üzerinden gösterir; Cloud Run'daki otomatik izleme sadece giren/çıkan HTTP'yi kapsar, servis içini görmek için enstrümantasyon gerekir.

**Cloud Profiler**, üretimde CPU ve bellek kullanımını düşük ek yükle sürekli örnekler ve kaynak koduna atfeder — Cloud Trace'ten farklı bir eksende çalışır.

---

# Önemli Noktalar

- Google Cloud Observability, Cloud Monitoring, Cloud Logging, Error Reporting, Cloud Trace ve Cloud Profiler'ı tek bir görünümde birleştirir.
- Monitoring fark eder, Logging bağlam verir, Error Reporting özetler, Trace nerede yavaşladığını gösterir, Profiler neden yavaşladığını kod seviyesinde gösterir.
- Four Golden Signals'da latency başarılı/başarısız ayrı ölçülmeli, errors sadece HTTP 5xx değildir, saturation %100'den önce bozulabilir.
- Structured logging aranabilirlik ve seviye filtreleme için önerilir.
- GKE'de loglar kalıcı değildir; Cloud Logging'e göndermek şarttır.
- Managed Service for Prometheus, Prometheus'u yönetme yükünü kaldırırken PromQL uyumluluğunu korur.
- Error Reporting'in etkinleştirilmesi platforma göre değişir: Cloud Run otomatik, GKE access scope, Compute Engine IAM rolü ister.
- Trace, "nerede yavaş" sorusunu; Profiler, "hangi kod pahalı" sorusunu cevaplar — birbirinin yerine geçmezler.
