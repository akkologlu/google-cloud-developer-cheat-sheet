# Modül 6 - Uygulamaları Dağıtma

---

# Genel Bakış

Bir uygulama oluşturmak yazılım geliştirme yaşam döngüsünün sadece bir kısmıdır. Bir sonraki zorluk, bunu güvenli, tutarlı ve otomatik olarak dağıtmaktır.

Google Cloud, geliştiricilerin şunları yapmasını sağlayan tam bir dağıtım ekosistemi sağlar:

- Uygulamaları otomatik olarak oluşturma
- Otomatik testler çalıştırma
- Uygulamaları konteyner haline pakete
- Yapı artefaktlarını güvenli şekilde depolama
- Uygulamaları farklı ortamlara dağıtma
- Bir şey ters giderse hızlı geri alma

Bu modülde tanıtılan ana hizmetler:

- Cloud Build
- Artifact Registry
- Cloud Deploy
- Cloud Monitoring
- Software Delivery Shield

---

# CI/CD Nedir?

CI/CD, **Sürekli Entegrasyon** ve **Sürekli Teslimat/Dağıtım** anlamına gelir.

Yazılımı oluşturan, test eden ve dağıtan otomatik bir boruvattır.

Uygulamaları elle oluşturmak ve dağıtmak yerine, her adım otomatik olarak gerçekleşir.

```text
Geliştirici

↓

Git Commit

↓

CI

↓

Oluştur

↓

Test

↓

Artefakt

↓

CD

↓

Dağıt

↓

Üretim
```

---

# Sürekli Entegrasyon (CI)

Sürekli Entegrasyon, her kod değişikliğini doğrulamaya odaklanır.

Bir geliştirici kodu repository'e ittiğinde, otomatik bir yapı süreci başlar.

Tipik CI adımları:

1. Kaynak kodu indir
2. Bağımlılıkları yükle
3. Projeyi derle
4. Birim testleri çalıştır
5. Uygulamayı oluştur
6. Konteyner görüntüsü oluştur
7. Yapı artefaktını depola

Örnek:

```text
git push feature/login

↓

Cloud Build

↓

npm install

↓

npm test

↓

npm build

↓

Docker build

↓

Görüntüyü Gönder
```

Herhangi bir adım başarısız olursa, yapı hemen durur.

Bu, kırık kodun ana dala ulaşmasını engeller.

---

# Sürekli Teslimat (CD)

Sürekli Teslimat, CI başarılı olduktan sonra başlar.

Uygulama otomatik olarak test ortamlarına dağıtılır.

Tipik akış:

```text
Artifact Registry

↓

Cloud Deploy

↓

Hazırlık

↓

Entegrasyon Testleri

↓

Elle Onay

↓

Üretim
```

Üretim dağıtımı insan onayı gerektirir.

---

# Sürekli Dağıtım

Sürekli Dağıtım, elle onay adımını kaldırır.

Eğer her otomatik test geçerse, dağıtım otomatik olarak gerçekleşir.

```text
CI

↓

Testler Geçti

↓

Üretim
```

Bu yaklaşım, olgun test borularına sahip şirketler tarafından yaygın olarak kullanılır.

---

# Hazırlık Ortamı

Hazırlık ortamı, yazılım yayınlanmadan önce test etmek için kullanılan bir üretim benzeri ortamdır.

Genellikle var:

- Aynı altyapı
- Aynı yapılandırma
- Aynı veritabanı şeması
- Aynı uygulama versiyonu

Ama sadece test ve geliştiriciler tarafından kullanılır.

```text
Geliştirici

↓

Dağıt

↓

Hazırlık

↓

QA Test

↓

Üretim
```

---

# Sürüm Adayı (RC)

Sürüm Adayı, tüm otomatik testleri geçmiş ve üretim için hazır olduğu düşünülen bir yapıdır.

Örnek:

```text
Sürüm 2.3.0

↓

Tüm Testler Geçti

↓

Sürüm Adayı
```

Üretim dağıtımından önceki son versiyondur.

---

# Kanary Dağıtım

Tüm kullanıcılara hemen dağıtmak yerine, trafik kademeli olarak kaydırılır.

Örnek:

```text
%10

↓

%25

↓

%50

↓

%100
```

Avantajları:

- Daha düşük risk
- Daha kolay izleme
- Hızlı geri alma

---

# Mavi-Yeşil Dağıtım

İki üretim ortamı aynı anda vardır.

```text
Mavi → Mevcut Sürüm

Yeşil → Yeni Sürüm
```

Başlangıçta:

```text
Kullanıcılar

↓

Mavi
```

Doğrulamadan sonra:

```text
Kullanıcılar

↓

Yeşil
```

Sorunlar oluşursa:

```text
Yeşil Başarısız

↓

Trafiği Değiştir

↓

Mavi
```

Geri alma neredeyse anında.

---

# Geri Alma

Geri Alma, önceki stabil sürüme dönemek anlamına gelir.

```text
Sürüm 2

↓

Hata Bulundu

↓

Geri Al

↓

Sürüm 1
```

Bu, kesinti süresini en aza indirir.

---

# Konteynerler

Google Cloud uygulamaları öncelikle konteyner olarak dağıtır.

Bir konteyner paketi:

- Uygulama kodu
- Çalışma zamanı
- Kütüphaneler
- Bağımlılıklar
- Yapılandırma

Örnek:

```text
Konteyner

├── Uygulama
├── Node.js Çalışma Zamanı
├── Kütüphaneler
├── Bağımlılıklar
└── Yapılandırma
```

Bu pakete **Konteyner Görüntüsü** denir.

---

# Sanal Makineler ve Konteynerler Karşılaştırması

## Sanal Makine

```text
Donanım

↓

Hipervisor

↓

Misafir İşletim Sistemi

↓

Uygulama
```

Her VM, kendi işletim sistemini içerir.

Avantajları:

- Güçlü izolasyon

Dezavantajları:

- Yavaş başlama
- Yüksek kaynak kullanımı

---

## Konteyner

```text
Donanım

↓

Ana İşletim Sistemi

↓

Konteyner Çalışma Zamanı

↓

Konteyner A

↓

Konteyner B

↓

Konteyner C
```

Konteynerler ana işletim sistemini paylaşırlar.

Avantajları:

- Hafif
- Hızlı başlama
- Daha düşük kaynak kullanımı
- Daha yüksek yoğunluk

---

# Konteynerların Avantajları

## Taşınabilirlik

Aynı konteyner görüntüsü her yerde çalışır.

```text
Geliştirici Dizüstü

↓

Konteyner Görüntüsü

↓

Cloud Run

↓

Aynı Uygulama
```

---

## İzolasyon

Farklı uygulamalar, çatışma olmadan farklı çalışma zamanı versiyonlarını kullanabilir.

Örnek:

Proje A

```text
Node.js 18
```

Proje B

```text
Node.js 22
```

Her ikisi de aynı sunucuda çalışabilir.

---

## Tutarlılık

Aynı görüntü şuraya dağıtılır:

- Geliştirme
- Test
- Üretim

Bu, ortam farklılıklarını ortadan kaldırır.

---

# Konteyner Görüntüsü

Konteyner görüntüsü, bir uygulamayı çalıştırmak için gereken her şeyi içeren salt okunur bir pakettir.

```text
Konteyner Görüntüsü

↓

Çalıştır

↓

Konteyner
```

Görüntü = Şablon

Konteyner = Çalışan örneği

---

# Cloud Build

Cloud Build, Google Cloud'un yönetilen yapı hizmetidir.

Otomatik olarak:

- Uygulamaları oluşturur
- Testleri çalıştırır
- Docker görüntüleri oluşturur
- Görüntüleri Artifact Registry'ye gönderir

İş Akışı:

```text
Git Push

↓

Cloud Build

↓

Docker Görüntüsü

↓

Artifact Registry
```

Yapı sunucularını elle yönetmeye gerek yoktur.

---

# Yapı Tetikleyicileri

Cloud Build, olaylara dayanarak otomatik olarak başlar.

Örnekler:

```text
Main'e Push

↓

Yapı Oluştur
```

```text
release/* 'e Push

↓

Yapı Oluştur
```

```text
Git Tag

↓

Yapı Oluştur
```

---

# cloudbuild.yaml

Cloud Build işlem hatları bir YAML dosyasında tanımlanır.

Örnek:

```yaml
steps:
  - npm install
  - npm test
  - docker build
  - docker push
```

Her adım kendi Docker konteynerisinde çalışır.

---

# Çalışma Alanı

Cloud Build paylaşılan bir dizin oluşturur:

```text
/workspace
```

Her yapı adımı orada dosyaları okuyabilir ve yazabilir.

```text
Adım 1

↓

Dosyalar Oluştur

↓

/workspace

↓

Adım 2

↓

Dosyaları Kullan

↓

/workspace

↓

Adım 3
```

Bu, artefaktların yapı adımları arasında paylaşılmasını sağlar.

---

# Artifact Registry

Artifact Registry, yapı çıktılarını depolar.

Örnekler şunları içerir:

- Docker Görüntüleri
- Maven Paketleri
- npm Paketleri
- Python Paketleri

Örnek:

```text
my-app

├── v1.0
├── v1.1
└── v2.0
```

Cloud Run ve GKE, dağıtım sırasında görüntüleri doğrudan Artifact Registry'den çekerler.

---

# CI/CD Güvenliği

Modern yazılım tedarik zincirleri otomatik dağıtımdan fazlasını gerektirir.

Tüm yapı işlem hattı da güvenli olmalıdır.

Google Cloud, yazılım teslimat sürecini güvenli hale getirmek için birden fazla hizmet sağlar.

---

# Software Delivery Shield

Software Delivery Shield, tam CI/CD işlem hattını korur.

Sağlar:

- Güvenli yapı altyapısı
- Güvenilir artefaktlar
- Güvenlik açığı taraması
- Dağıtım doğrulaması

Amacı, sadece güvenilir yazılımın üretim ortamına ulaşmasını sağlamaktır.

---

# Güvenilir Açık Kaynak Yazılım

Çoğu uygulama açık kaynak kütüphanelerine bağlıdır.

Örnekler:

- React
- Axios
- Lodash

Google, seçilen açık kaynak paketlerini doğrular ve güvenlik açıkları açısından sürekli tarar.

Geliştiriciler bu doğrulanmış paketleri güvenle kullanabilirler.

---

# Artefakt Analizi

Artefakt Analizi, Artifact Registry'de depolanmış görüntüleri tarar.

Tespit eder:

- Güvenlik açıkları
- Eski bağımlılıklar
- Güvenlik sorunları

Tarama, yeni güvenlik açıkları keşfedildiğinde dağıtımdan sonra bile devam eder.

---

# Binary Authorization

Binary Authorization, sadece güvenilir görüntülerin dağıtılabilmesini sağlar.

Tipik dağıtım politikası:

- Cloud Build tarafından oluşturuldu
- Başarıyla tarandı
- Düzgün şekilde imzalandı
- Güvenlik denemeleri geçti

Herhangi bir gereksinim eksikse, dağıtım engellenir.

Bu, yetkisiz veya kötü niyetli görüntülerin üretim ortamına ulaşmasını engeller.

---

# Modül Özeti

```text
Geliştirici
    │
    ▼
Git Commit
    │
    ▼
Cloud Build (CI)
    │
    ├── Oluştur
    ├── Test
    └── Docker Görüntüsü
    │
    ▼
Artifact Registry
    │
    ▼
Cloud Deploy (CD)
    │
    ▼
Hazırlık
    │
    ▼
Onay (Sürekli Teslimat)
    │
    ▼
Üretim (Cloud Run / GKE)
    │
    ▼
Cloud Monitoring
```

---

# Önemli Noktalar

- CI, her kod değişikliğini otomatik olarak oluşturur ve test eder.
- CD, doğrulanmış yapıları hazırlık ve üretim ortamlarına dağıtır.
- Konteynerler uygulamaları tüm bağımlılıklarıyla paketler ve tutarlı dağıtım sağlar.
- Cloud Build, yapıları otomatikleştirir ve konteyner görüntüleri oluşturur.
- Artifact Registry, sürümlendirilmiş yapı artefaktlarını depolar.
- Cloud Deploy, ortamlar arasında dağıtımları otomatikleştirir.
- Kanary ve Mavi-Yeşil dağıtımlar dağıtım riskini azaltır.
- Geri Alma, başarısız yayınlardan hızlı kurtulmayı sağlar.
- Software Delivery Shield, tüm yazılım tedarik zincirini güvenli hale getirir.
- Binary Authorization, sadece güvenilir artefaktların dağıtılmasını sağlar.
