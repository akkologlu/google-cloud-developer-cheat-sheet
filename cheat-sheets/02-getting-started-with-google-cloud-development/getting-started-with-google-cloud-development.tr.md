# Modül 2 - Google Cloud Geliştirmeye Başlangıç

> Uygulamaların Google Cloud hizmetleriyle nasıl iletişim kurduğunu öğrenin ve bulut geliştirme için Google'ın sağladığı araçları keşfedin.

---

# 🎯 Öğrenme Hedefleri

Bu modülü tamamladıktan sonra, şunları yapabileceksiniz:

- Uygulamaların Google Cloud ile nasıl iletişim kurduğunu açıklayın.
- Cloud API'lerinin amacını anlayın.
- REST ve gRPC arasındaki farkı belirleyin.
- Google Cloud SDK'nın rolünü anlayın.
- gcloud CLI, Cloud Client Libraries, Cloud Shell ve Cloud Code'un ne zaman kullanılacağını bilin.
- Google Cloud'un geliştirici deneyimini nasıl basitleştirdiğini açıklayın.

---

# Genel Resim

Node.js kullanarak bir web uygulaması geliştirdiğinizi hayal edin.

Bir kullanıcı bir profil fotoğrafı yükler ve bunu **Cloud Storage**'da depolamak istiyorsunuz.

Uygulamanız Google'ın altyapısıyla nasıl iletişim kurar?

Bu modül bu soruya cevap verir.

İletişim akışı her zaman benzerdir:

```text
Uygulama
   │
   ▼
Cloud API
   │
   ▼
Google Cloud Hizmeti
```

Her Google Cloud hizmeti bir API aracılığıyla erişilir.

---

# 1. Cloud API'leri

Cloud API'leri Google Cloud'un temelini oluşturur.

Her Google Cloud hizmeti, uygulamaların onunla etkileşime girmesini sağlayan bir veya daha fazla API ortaya koymaktadır.

Örnekler şunları içerir:

- Cloud Storage API
- Compute Engine API
- Firestore API
- BigQuery API
- Pub/Sub API

Uygulamanız bir dosya yüklemek, sanal bir makine oluşturmak veya bir veritabanını sorgulamak istediğinde, karşılık gelen Cloud API'sine bir istek gönderir.

Örneğin, bir dosya yüklerken:

```text
Kullanıcı
  │
  ▼
Uygulama
  │
  ▼
Cloud Storage API
  │
  ▼
Cloud Storage
```

Uygulamanız asla Google'ın depolama altyapısıyla doğrudan iletişim kurmaz.

Her şey bir Cloud API aracılığıyla gerçekleşir.

---

# 2. REST ve gRPC

Cloud API'leri iki iletişim protokolünü destekler.

## REST

REST, en yaygın API stilidir.

Şunları kullanır:

- HTTP
- JSON

Örnek:

```http
POST /buckets

{
  "name": "images"
}
```

Avantajları:

- Anlaması kolay
- İnsan tarafından okunabilir
- Hata ayıklamak kolay

REST, yaygın olarak geliştirici ve üçüncü taraf entegrasyonları tarafından kullanılır.

---

## gRPC

gRPC, Google'ın yüksek performanslı iletişim protokolüdür.

JSON yerine, Protocol Buffers adı verilen kompakt bir ikili biçim kullanır.

Avantajları:

- Daha hızlı iletişim
- Düşük gecikme süresi
- Daha küçük yük
- Daha iyi performans

Çoğu geliştirici gRPC'yi asla doğrudan kullanmaz.

Google Cloud Client Libraries otomatik olarak mümkün olan her yerde gRPC kullanır.

---

# REST ve gRPC Karşılaştırması

| REST                        | gRPC                      |
| --------------------------- | ------------------------- |
| İnsan tarafından okunabilir | İkili protokol            |
| HTTP + JSON kullanır        | Protocol Buffers kullanır |
| Hata ayıklama kolay         | Yüksek performans         |
| Genel API'ler için harika   | İç hizmetler için harika  |

---

# 3. Kimlik Doğrulama

Google Cloud hiçbir zaman gelen bir isteğe otomatik olarak güvenmez.

Herhangi bir işlem yapmadan önce sorar:

> **Sen kimsin?**

Her API isteği geçerli kimlik bilgilerini içermelidir.

Yaygın kimlik doğrulama yöntemleri şunları içerir:

- OAuth 2.0
- Service Accounts

Kimlik doğrulaması olmadan, Cloud API'leri isteği reddeder.

Kimlik doğrulama, Google Cloud kaynaklarınızı yetkisiz erişimden korur.

---

# 4. Google Cloud SDK

Google Cloud SDK, geliştiricilerin Google Cloud ile etkileşime girmesine yardımcı olan araçlar koleksiyonudur.

Bunu bir araç kutusu olarak düşünün.

Şunlar gibi araçları içerir:

- gcloud
- gcloud storage
- kubectl
- bq
- Yerel emülatörler

SDK'nın kendisi doğrudan Google Cloud ile iletişim kurmaz.

Bunun yerine, araçları perde arkasında Cloud API'leri kullanırlar.

---

# 5. Google Cloud CLI (gcloud)

Google Cloud CLI, Google Cloud kaynaklarını yönetmek için kullanılan bir komut satırı arayüzüdür.

Örnek:

```bash
gcloud compute instances list
```

Bu komut, projenizde tüm Compute Engine sanal makinelerini listeler.

Komut basit görünse de, CLI otomatik olarak birkaç görev gerçekleştirir:

1. Kullanıcıyı kimlik doğrulaması yapar.
2. API isteğini oluşturur.
3. İsteği uygun Cloud API'sine gönderir.
4. Yanıtı gösterir.

İletişim akışı:

```text
Geliştirici
    │
    ▼
gcloud CLI
    │
    ▼
Cloud API
    │
    ▼
Google Cloud
```

---

# 6. gcloud storage, gsutil ve bq

Google Cloud, özel komut satırı araçları sağlar.

### gcloud storage

Cloud Storage'ı yönetmek için modern komut satırı aracı.

Örnekler:

- Dosya yükleme
- Dosya indirme
- Bucket oluşturma
- Nesneleri silme

Google, yeni projeler için **gsutil** yerine **gcloud storage** kullanmayı önerir.

---

### gsutil

Orijinal Cloud Storage komut satırı aracı.

Hâlâ desteklenmiş, ancak kademeli olarak **gcloud storage** tarafından değiştirilmektedir.

---

### bq

BigQuery için komut satırı aracı.

Öncelikle şunlar için kullanılır:

- SQL sorguları çalıştırma
- Veri setlerini yönetme
- Tabloları yönetme

---

# 7. Cloud Client Libraries

Uygulamalar nadiren Cloud API'leri doğrudan çağırırlar.

Bunun yerine, Google **Cloud Client Libraries** kullanmayı önerir.

Örneğin, Node.js'te:

```javascript
const { Storage } = require("@google-cloud/storage");

const storage = new Storage();
```

Kütüphane otomatik olarak şunları halleder:

- Kimlik doğrulama
- Yeniden deneme
- Hata işleme
- İstek biçimlendirme
- gRPC optimizasyonu

Bu, geliştiricilerin ağ kodu yerine uygulama mantığını yazmaya odaklanmalarını sağlar.

---

# 8. Cloud Shell

Cloud Shell, Google Cloud tarafından sağlanan tarayıcı tabanlı bir Linux ortamıdır.

İçerir:

- Google Cloud SDK
- Git
- kubectl
- Docker

Kurulum gerekmez.

Sadece Google Cloud Console'dan Cloud Shell'i açarsınız ve hemen çalışmaya başlarsınız.

Cloud Shell, farklı bilgisayarlardan çalışırken veya SDK'yı yerel olarak kurmak istemediğinizde idealdir.

---

# 9. Cloud Code

Cloud Code, Google Cloud için bir IDE uzantıları setidir.

Şunlar için mevcuttur:

- Visual Studio Code
- JetBrains IDE'leri
- Cloud Shell Editörü

Cloud Code, geliştiricilerin şunları yapmasını sağlar:

- Cloud Run hizmetlerini dağıtma
- Kubernetes uygulamaları geliştirme
- Cloud API'lerini inceleme
- Secret Manager'a erişme
- Günlükleri görüntüleme
- Otomatik tamamlamayla Kubernetes YAML dosyalarını düzenleme

IDE ile Google Cloud Console arasında geçiş yapmak yerine, birçok bulut işlemi doğrudan editör içinde gerçekleştirilebilir.

---

# 10. Yerel Emülatörler

Bulut hizmetlerine karşı doğrudan geliştirme yapmak yavaş ve pahalı olabilir.

Google, birçok hizmet için yerel emülatörler sağlar.

Örnekler şunları içerir:

- Firestore
- Pub/Sub
- Bigtable
- Datastore
- Spanner

Uygulamanız gerçek bulut hizmetinin yerine emülatöre bağlanır.

Avantajları:

- Daha hızlı geliştirme
- İnternet bağlantısı gerekli değil
- Bulut kaynağı maliyeti yok
- Güvenli test ortamı

---

# 11. Cloud Workstations

Cloud Workstations, tamamen yönetilen bulut geliştirme ortamları sağlar.

Yazılımı yerel olarak kurmak yerine, geliştiriciler önceden yapılandırılmış bir geliştirme makinesi alırlar.

Avantajları:

- Tutarlı ortamlar
- Daha hızlı onboarding
- İyileştirilmiş güvenlik
- Tarayıcı tabanlı erişim
- Herhangi bir yerden uzaktan geliştirme

Geliştirme ortamı müşterinin Google Cloud projesi içinde çalıştığından, kaynak kodu güvenli kalır.

---

# Modül Özeti

Bu modül tek bir temel konsepti öğretir:

Google Cloud ile her etkileşim benzer bir iletişim yolunu takip eder.

```text
                 Geliştirici
                      │
        ┌─────────────┴─────────────┐
        │                           │
   Uygulama                      Terminal
        │                           │
Cloud Client Library          gcloud CLI
        │                           │
        └─────────────┬─────────────┘
                      │
                  Cloud API
                      │
                      ▼
           Google Cloud Hizmeti
```

Bu mimarilerin anlaşılması, sonraki her Google Cloud ürününü öğrenmeyi çok daha kolay hale getirir.

---

# Önemli Noktalar

- Her Google Cloud hizmeti bir veya daha fazla Cloud API ortaya koymaktadır.
- REST ve gRPC, desteklenen iki iletişim protokolüdür.
- Her API isteği için kimlik doğrulama gereklidir.
- Google Cloud SDK, geliştirici araçları koleksiyonudur.
- gcloud CLI, terminalden Cloud API kullanımını basitleştirir.
- Cloud Client Libraries, uygulamalardan Google Cloud'a erişmenin önerilen yoludur.
- Cloud Shell, kullanıma hazır bir geliştirme ortamı sağlar.
- Cloud Code, Google Cloud'u doğrudan IDE'nize entegre eder.
- Yerel emülatörler, bulut kaynaklarını tüketmeden çevrimdışı geliştirmeye izin verir.
- Cloud Workstations, güvenli, bulut tarafından barındırılan geliştirme ortamları sağlar.

---

# Sertifika İpuçları

Google Cloud Developer Sertifikasyonu için bu farklılıkları unutmayın:

| Konsept              | Unutmayın                                        |
| -------------------- | ------------------------------------------------ |
| Cloud API            | Google Cloud hizmetlerine programlama arayüzü    |
| REST                 | HTTP + JSON iletişimi                            |
| gRPC                 | Yüksek performanslı ikili iletişim               |
| Google Cloud SDK     | Geliştirici araçları koleksiyonu                 |
| gcloud CLI           | SDK'da bulunan komut satırı aracı                |
| Cloud Client Library | Uygulama geliştirme için önerilen                |
| Cloud Shell          | Tarayıcı tabanlı geliştirme ortamı               |
| Cloud Code           | IDE'ler için Google Cloud entegrasyonu           |
| Yerel Emülatör       | Geliştirme için bulut hizmetinin yerel versiyonu |
| Cloud Workstations   | Yönetilen bulut geliştirme ortamı                |

Bu modülü hatırlamanın basit bir yolu:

> **Uygulama → Client Library (veya CLI) → Cloud API → Google Cloud Hizmeti**
