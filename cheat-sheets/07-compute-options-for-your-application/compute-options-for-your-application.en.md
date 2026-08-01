# Module 7 – Compute Options for Your Application

# Amaç

Bu modülde Google Cloud'un uygulamaları çalıştırmak için sunduğu compute servislerini öğreneceğiz.

Modülün sonunda aşağıdaki sorulara cevap verebiliyor olacağız:

- Compute Engine nedir?
- GKE nedir?
- Cloud Run nedir?
- Hangisini hangi durumda kullanmalıyım?
- GKE Standard ile Autopilot arasındaki fark nedir?
- Cloud Run neden serverless olarak adlandırılır?
- Google neden yeni uygulamalar için Cloud Run'ı öneriyor?

---

# 1. Compute (Çalıştırma) Nedir?

Önce "compute" kelimesini anlayalım.

Bir uygulama yazdığımızda kod kendi başına çalışamaz.

Kodun çalışabileceği bir ortama ihtiyacı vardır.

Örneğin:

```
Node.js API

↓

Bir bilgisayar üzerinde çalışır
```

Eskiden bu bilgisayar şirketin veri merkezindeki fiziksel sunucuydu.

Bugün ise bu bilgisayar Google Cloud üzerinde olabilir.

İşte Google Cloud'un uygulamanızı çalıştırmak için sunduğu ortamlara **Compute Options** denir.

---

# 2. Google Cloud Neden Birden Fazla Compute Seçeneği Sunuyor?

Her uygulamanın ihtiyacı aynı değildir.

Örneğin;

- Eski bir Java uygulaması
- Modern bir mikroservis
- AI uygulaması
- Basit bir REST API

Hepsi farklı ihtiyaçlara sahiptir.

Bu yüzden Google üç temel platform sunar.

```
Compute Engine

↓

Google Kubernetes Engine (GKE)

↓

Cloud Run
```

---

# 3. En Önemli Kavram: Control vs Operational Burden

Modül boyunca sürekli şu fikir tekrar edilir.

> Ne kadar fazla kontrol istersen,
> o kadar fazla yönetim yüküne sahip olursun.

Bunu aşağıdaki grafik özetler.

```
Daha Fazla Kontrol
▲

Compute Engine

↓

GKE

↓

Cloud Run

▼
Daha Az Yönetim
```

veya tersinden bakarsak

```
Operational Effort

Compute Engine
↑

GKE
↑

Cloud Run
```

Yani;

- Compute Engine → En fazla kontrol, en fazla bakım
- GKE → Orta seviye kontrol
- Cloud Run → En az kontrol, en az bakım

---

# 4. Compute Engine

Compute Engine aslında Google Cloud üzerinde çalışan Virtual Machine'dir.

Google sana bir bilgisayar kiralar.

Bu bilgisayarın içine istediğin her şeyi kurabilirsin.

```
Google VM

├── Ubuntu
├── Docker
├── Node.js
├── Nginx
└── Application
```

Her şeyi sen yönetirsin.

---

## Avantajları

- İşletim sistemini seçebilirsin.
- İstediğin yazılımı kurabilirsin.
- Root erişimin vardır.
- Maksimum kontrol sağlar.

---

## Dezavantajları

Bütün bakım senden sorumludur.

Örneğin;

- İşletim sistemi güncellemesi
- Güvenlik yamaları
- Docker kurulumu
- Monitoring
- Firewall
- SSL
- Disk yönetimi

---

## Ne zaman kullanılır?

- Legacy uygulamalar
- Lift & Shift migration
- Windows Server
- Lisans bağımlılığı olan yazılımlar
- Çok özel sistem ayarları gerektiren uygulamalar

---

# 5. Kubernetes Nedir?

Container kullanırken işler hızla karmaşıklaşır.

Örneğin;

```
100 Container
```

şeklinde çalışan bir sistem düşün.

Bunların

- yeniden başlatılması
- ölçeklendirilmesi
- güncellenmesi
- ağ yönetimi
- yük dağıtımı

manuel yapılamaz.

Bu yüzden Kubernetes geliştirilmiştir.

---

## Kubernetes ne yapar?

Kubernetes container'ların yöneticisidir.

Şunları otomatik yapabilir.

- Container oluşturur.
- Container siler.
- Çöken container'ı yeniden oluşturur.
- Trafiği dağıtır.
- Ölçeklendirir.
- Rolling Update yapar.
- Secret yönetir.
- Config yönetir.

Yani;

```
Docker

↓

Container

↓

Kubernetes bunları yönetir.
```

---

# 6. Google Kubernetes Engine (GKE)

Google Kubernetes Engine,

Google'ın yönettiği Kubernetes servisidir.

Sen Kubernetes kurmazsın.

Google senin yerine kurar.

```
Application

↓

Container

↓

GKE

↓

Google Cloud
```

---

# 7. Cluster Nedir?

Kubernetes tek bilgisayardan oluşmaz.

Birden fazla makineden oluşur.

Bu makine grubuna

**Cluster**

denir.

```
Cluster

├── Node 1
├── Node 2
├── Node 3
```

---

# 8. Node Nedir?

Node aslında bir Virtual Machine'dir.

```
Node = VM
```

Her node içerisinde Pod'lar çalışır.

---

# 9. Pod Nedir?

Pod,

Kubernetes'in deploy ettiği en küçük birimdir.

```
Node

├── Pod
├── Pod
├── Pod
```

Bir Pod genellikle tek container çalıştırır.

```
Pod

↓

Container
```

Ancak teknik olarak bir Pod içinde birden fazla container olabilir.

---

# 10. Control Plane

Control Plane,

Kubernetes'in beynidir.

Şunları yönetir.

- Scheduling
- Deployment
- Scaling
- Networking
- Health Check

```
Control Plane

↓

Worker Nodes

↓

Pods

↓

Containers
```

---

# 11. GKE Standard

Standard modda bazı işleri Google yapar.

Bazı işleri ise sen yaparsın.

Google;

- Control Plane
- Kubernetes yönetimi

ile ilgilenir.

Sen ise;

- Node Pool
- VM tipi
- Scaling
- Network
- Cluster ayarları

ile ilgilenirsin.

---

# 12. GKE Autopilot

Autopilot'ta Google neredeyse her şeyi yönetir.

```
Application

↓

Pods

↓

Google
```

Node bile oluşturmazsın.

Bu yüzden operasyon maliyeti oldukça düşüktür.

---

# 13. GKE'nin Sağladığı Özellikler

## Auto Scaling

Trafik artarsa yeni Pod oluşturur.

```
10 Pod

↓

50 Pod

↓

100 Pod
```

---

## Auto Repair

Node bozulursa

Google yeni node oluşturur.

---

## Auto Upgrade

Kubernetes sürümü eskiyse

Google otomatik günceller.

---

## Load Balancer

Ingress tanımladığında

Google otomatik Load Balancer oluşturur.

---

## Persistent Volume

Stateful uygulamalar için disk oluşturur.

Örneğin

```
PostgreSQL

↓

Persistent Disk
```

---

# 14. GKE Ne Zaman Kullanılır?

- Büyük mikroservis sistemleri
- Çok fazla container
- Hybrid Cloud
- Multi Cloud
- HTTP dışındaki protokoller
- Stateful uygulamalar
- GPU/TPU kullanan AI uygulamaları

---

# 15. Cloud Run

Cloud Run,

Google'ın tamamen yönetilen Serverless Container platformudur.

Sen sadece uygulamanı verirsin.

Google geri kalan her şeyi yapar.

```
Application

↓

Container

↓

Cloud Run
```

---

# 16. Serverless Ne Demek?

Serverless demek

"Sunucu yok"

anlamına gelmez.

Sunucu vardır.

Ama sen yönetmezsin.

Google yönetir.

Sen sadece kod yazarsın.

---

# 17. Cloud Run Nasıl Çalışır?

```
HTTP Request

↓

Container açılır

↓

Response döner

↓

Container kapanabilir
```

Kullanıcı yoksa

```
0 Instance
```

çalışır.

Bu özelliğe

**Scale to Zero**

denir.

---

# 18. Scale to Zero

Cloud Run'ın en büyük avantajıdır.

Gece boyunca hiç trafik yoksa

```
Instance = 0
```

olur.

Para ödemezsin.

Sabah trafik geldiğinde

```
0

↓

10

↓

50

↓

100 Instance
```

otomatik oluşturulur.

---

# 19. Cloud Run GPU

Cloud Run artık GPU da destekler.

Bu sayede

- Gemini
- Llama
- Mistral

gibi büyük dil modellerini inference amacıyla çalıştırabilirsin.

---

# 20. Cloud Run Deployment Seçenekleri

Cloud Run dört farklı şekilde deploy edilebilir.

## 1. Docker Image

```
Docker Build

↓

Artifact Registry

↓

Cloud Run
```

---

## 2. Source Code

Dockerfile yazmana gerek yoktur.

```
Node.js Source

↓

Cloud Build

↓

Container

↓

Cloud Run
```

---

## 3. Function

Sadece fonksiyon yazarsın.

Google bunu otomatik container haline getirir.

---

## 4. GitHub

```
Git Push

↓

Cloud Build

↓

Cloud Run
```

Tam otomatik CI/CD kurulabilir.

---

# 21. Buildpacks

Dockerfile yazmak istemiyorsan

Google Buildpacks kullanır.

Kodunu analiz eder.

```
Node.js

↓

npm install

↓

Container

↓

Deploy
```

Hepsi otomatik yapılır.

---

# 22. Cloud Run Jobs

Cloud Run sadece HTTP servisleri çalıştırmaz.

Tek seferlik işler de çalıştırabilir.

Örneğin;

- Backup
- ETL
- CSV işleme
- Video dönüştürme

```
Job

↓

Çalışır

↓

Biter
```

HTTP dinlemez.

Tamamlanınca kapanır.

---

# 23. Compute Engine vs GKE vs Cloud Run

| Özellik               | Compute Engine | GKE            | Cloud Run    |
| --------------------- | -------------- | -------------- | ------------ |
| VM                    | ✅             | Google yönetir | Gizlenmiştir |
| Container             | Opsiyonel      | Zorunlu        | Zorunlu      |
| Kubernetes            | ❌             | ✅             | ❌           |
| Serverless            | ❌             | ❌             | ✅           |
| Scale to Zero         | ❌             | ❌             | ✅           |
| Auto Scaling          | Manuel         | Var            | Tam otomatik |
| Stateful              | ✅             | ✅             | Sınırlı      |
| HTTP dışı protokoller | ✅             | ✅             | ❌           |
| Operasyon             | Çok yüksek     | Orta           | Çok düşük    |

---

# 24. Hangi Platformu Ne Zaman Seçmeliyim?

## Compute Engine

Kullan.

- Legacy sistem
- Lift & Shift
- Windows uygulamaları
- Özel OS ayarları

---

## GKE

Kullan.

- Mikroservis
- Çok fazla container
- Hybrid Cloud
- Stateful servisler
- Kubernetes ekosistemi

---

## Cloud Run

Kullan.

- REST API
- Backend servisleri
- Event-driven uygulamalar
- Mikroservisler
- Web uygulamaları
- Serverless sistemler

Google'ın yeni projeler için ilk önerisi Cloud Run'dır.

---

# 25. Modül Özeti

Bu modülde Google Cloud'un üç ana compute platformunu öğrendik.

- **Compute Engine**, en yüksek kontrolü sağlar ancak tüm altyapı yönetimi geliştirici veya operasyon ekibinin sorumluluğundadır.
- **Google Kubernetes Engine (GKE)**, container tabanlı uygulamalar için Kubernetes'in yönetilen sürümüdür. Büyük ölçekli, mikroservis mimarileri ve gelişmiş orkestrasyon ihtiyaçları için uygundur.
- **Cloud Run**, tamamen serverless çalışan bir container platformudur. Altyapı yönetimini Google üstlenir, uygulama otomatik ölçeklenir ve yalnızca kullanılan kaynak kadar ödeme yapılır.

Google'ın genel önerisi şudur:

- Yeni bir proje geliştiriyorsan **Cloud Run** ile başla.
- Kubernetes özelliklerine ihtiyaç duyarsan **GKE**'ye geç.
- İşletim sistemi seviyesinde tam kontrol veya legacy uygulamalar gerekiyorsa **Compute Engine** kullan.
