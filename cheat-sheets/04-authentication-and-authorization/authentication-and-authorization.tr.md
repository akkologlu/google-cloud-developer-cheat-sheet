# Modül 4 – Kimlik Doğrulama ve Yetkilendirme

> **Kurs:** Google Cloud ile Uygulama Geliştirme – Temeller

---

# Kimlik Doğrulama ve Yetkilendirme

Bu modülün en önemli konseptidir.

İnsanlar bu iki terimi sık sık karıştırır, ama tamamen farklı sorunları çözerler.

## Kimlik Doğrulama (AuthN)

Kimlik doğrulama şu soruya cevap verir:

> **"Sen kimsin?"**

Kimliği doğrular.

Örnekler:

- Google ile giriş yapma
- GitHub ile giriş yapma
- Email/şifre ile giriş yapma
- Cloud Run hizmeti Google Cloud'a kimliğini ispatlama

Örnekler:

- Kullanıcı → "Ben Abdullah'ım."
- Uygulama → "Ben backend-service@project.iam.gserviceaccount.com'um"

---

## Yetkilendirme (AuthZ)

Yetkilendirme şu soruya cevap verir:

> **"Ne yapmanıza izin verilir?"**

Kimlik doğrulandıktan sonra, Google izinleri kontrol eder.

Örnek:

```
Kullanıcı:
✔ Storage Bucket'ı Oku
✔ Dosya Yükle
❌ Bucket'ı Sil
```

Kimlik doğrulama önce gerçekleşir.

Yetkilendirme sonra gerçekleşir.

```
Kimlik Doğrulama
      ↓
Sen kimsin?

Yetkilendirme
      ↓
Ne yapabilirsin?
```

---

# IAM (Kimlik ve Erişim Yönetimi)

IAM, Google'ın yetkilendirme sistemidir.

Tanımlar:

- KİM erişebilir
- HANGİ kaynağa
- HANGI izinlerle

Google, IAM'ı üç konseptle açıklar:

```
Principal (Asıl)
↓

Role (Rol)
↓

Resource (Kaynak)
```

Örnek:

```
Principal (Asıl):
abdullah@gmail.com

Role (Rol):
Storage Object Viewer (Depolama Nesnesi Görüntüleyicisi)

Resource (Kaynak):
photos-bucket
```

Sonuç:

Abdullah sadece bucket içindeki nesneleri okuyabilir.

---

# IAM Principals (Asıllar)

Principal basitçe bir kimlik gösterimidir.

Olası principals şunları içerir:

## Google Hesap

Gerçek bir kişiyi temsil eder.

Örnek:

```
john@gmail.com
```

Kullanım alanları:

- Geliştiriciler
- Yöneticiler
- Son kullanıcılar

---

## Service Account (Hizmet Hesabı)

Bir insanın yerine bir uygulamayı temsil eder.

Örnek:

```
backend-service@project.iam.gserviceaccount.com
```

Bu muhtemelen Google Cloud'daki en önemli konsepttir.

Uygulamanızın Cloud Storage'dan dosyaları okuması gerekiyor olsun.

Kim istek yapıyor?

Kullanıcı değil.

Uygulamanın kendisi.

Bu nedenle uygulamanın da bir kimliği olması gerekir.

Bu kimlik Service Account'tır.

Bunu uygulamanız için çalışan rozeti olarak düşünün.

```
İnsan
↓

Google Hesabı

Uygulama
↓

Service Account
```

Kodunuza izin vermek yerine, Service Account'a izin verirsiniz.

Örnek:

```
Cloud Run

↓

Bağlı Service Account

↓

Storage Admin

↓

Cloud Storage
```

Cloud Run, Cloud Storage'ı her çağırdığında,

Google görür:

> "Bu istek bu Service Account'tan geliyor."

ve IAM rollerini kontrol eder.

---

## Google Grubu

Kullanıcıların bir koleksiyonu.

İzinleri tek tek vermek yerine,

izni gruba verin.

Örnek:

```
Backend Takımı

↓

Alice
Bob
Charlie
David
```

Gruba bir rol verin.

Herkes bunu miras alır.

---

## Google Workspace

Tüm şirketinizi temsil eder.

Örnek:

```
company.com
```

Şirket çapında izinleri yönetmek için kullanışlıdır.

---

## Cloud Identity

Workspace'e benzer,

ama Gmail, Docs, Drive vb. olmadan.

Sadece kimlik yönetimi.

---

# Roller

İzinler asla doğrudan atanmaz.

Bunun yerine,

Google izinleri Rollere gruplayır.

Örnek:

```
Storage Viewer (Depolama Görüntüleyicisi)

içerir

storage.objects.get
storage.objects.list
```

Rolü atayın,

bireysel izinleri değil.

---

## Rol Türleri

### Temel Roller

Çok geniş kapsamlı.

Örnekler:

```
Viewer (Görüntüleyici)

Editor (Editör)

Owner (Sahip)
```

Çoğunlukla test için kullanılır.

Üretimde kaçının.

---

### Önceden Tanımlanmış Roller

Google tarafından oluşturulmuş.

Örnek:

```
Cloud Run Invoker

Storage Object Viewer

BigQuery Admin
```

En sık kullanılanıdır.

---

### Özel Roller

Sizin tarafından oluşturulmuş.

Google'ın önceden tanımlanmış rolleri çok geniş olduğunda kullanışlıdır.

Örnek:

```
Bucket'ı Okuyabilir

Resim Yükleyebilir

Silemez
```

---

# En Az Ayrıcalık İlkesi

Her zaman gereken en az izinleri verin.

Kötü:

```
Cloud Storage Admin
```

İyi:

```
Storage Object Viewer
```

Bir hesap tehlikeye girerse,

zarar sınırlıdır.

---

# API Kimlik Doğrulama Yöntemleri

Uygulamalar, Google Cloud API'lerine birçok şekilde kimlik doğrulayabilir.

## 1. API Anahtarı

Çok basit.

Sadece bir string.

```
API Anahtarı

↓

Google API
```

Çoğunlukla şunlar için kullanılır:

- Maps
- Genel API'ler

Hassas işlemler için uygun değildir.

Neden?

Çünkü anahtarı alan herkes kullanabilir.

---

## 2. OAuth Kullanıcı Hesabı

Gerçek bir kullanıcıyı temsil eder.

```
Kullanıcı

↓

Google Giriş

↓

OAuth Token

↓

Google API
```

OAuth token:

- otomatik olarak sona erer
- API anahtarından daha güvenlidir
- kullanıcının izinlerini taşır

---

## 3. Service Account (Hizmet Hesabı)

Bir uygulamayı temsil eder.

```
Uygulama

↓

Service Account

↓

OAuth Token

↓

Google API'leri
```

Bu, arka uç uygulamaları için tercih edilen kimlik doğrulama yöntemidir.

---

# Neden Service Accounts Vardır

Bu arka ucu düşünün:

```
Frontend

↓

Backend

↓

Cloud Storage
```

Kim Cloud Storage'a erişmeli?

Her kullanıcı değil.

Sadece backend.

Bu nedenle:

```
Backend

↓

Service Account

↓

Cloud Storage
```

Kullanıcılar asla Storage kimlik bilgilerini almaz.

Backend, onlar adına işlemleri gerçekleştirir.

---

# Service Account Anahtarları

Başlangıçta,

uygulamalar bir JSON anahtar dosyası indirdi.

```
service-account.json
```

Bu dosya özel anahtarı içerir.

Bunu kullanarak,

uygulama OAuth erişim token'ları ister.

Problem:

Bu JSON'u biri çalarsa,

o kişi uygulamanız olur.

Bu yüzden Google, mümkün olan her yerde indirilen anahtarlardan kaçınmayı önerir.

---

# Kimlik Doğrulama Karar Ağacı

Google, uygulamanızın nerede çalıştığına bağlı olarak farklı kimlik doğrulama yöntemleri önerir.

## Senaryo 1

Uygulama yerel olarak çalışır.

Örnek:

```
VS Code

↓

Node.js

↓

Google API'leri
```

Şunu kullanın:

```
gcloud auth application-default login
```

Bu, geçici kullanıcı kimlik bilgilerini makinenizde depolar.

Sadece geliştirme için iyidir.

---

## Senaryo 2

Uygulama Google Cloud'da çalışır

Örnekler:

- Cloud Run
- Compute Engine
- Cloud Functions

Service Account'u doğrudan ekleyin.

```
Cloud Run

↓

Bağlı Service Account

↓

Google API'leri
```

JSON anahtarı gerekmez.

Bu, Google'ın önerilen üretim yaklaşımıdır.

---

## Senaryo 3

Uygulama GKE'de çalışır

Service Account JSON dosyalarını kullanmayın.

Bunun yerine kullanın:

```
Workload Identity
```

Akış:

```
Pod

↓

Kubernetes Service Account

↓

IAM Service Account

↓

Google API'leri
```

Bu otomatik olarak Kubernetes kimlik bilgilerini Google kimlik bilgilerine değiştirir.

Daha güvenli.

Yönetimi daha kolay.

---

## Senaryo 4

Uygulama Google Cloud dışında çalışır

Örnekler:

- AWS
- Azure
- Şirket içi

Mümkünse,

şunu kullanın:

```
Workload Identity Federation
```

Google anahtarı depolamak yerine,

Google başka bir kimlik sağlayıcıya güvenir.

Akış:

```
AWS

↓

OIDC Token

↓

Google

↓

Geçici Erişim Token

↓

Google API'leri
```

Service Account anahtarı depolanmaz.

---

## Senaryo 5

Federation imkansız

Son çare:

```
Service Account JSON Anahtarı
```

Google, bunu mümkün olan her yerde kaçınmayı kuvvetle önerir.

---

# Application Default Credentials (ADC)

ADC, Google Cloud'daki en önemli konseptlerden biridir.

Bu bir kimlik bilgisi **değildir**.

Bu bir mekanizmadır.

Görevi basitçe:

> "Kimlik bilgilerini otomatik olarak bul."

Bunun gibi kod yazmak yerine:

```python
client = StorageClient(key="service-account.json")
```

Şunu yazarsınız:

```python
client = StorageClient()
```

Kütüphane ADC'ye sorar:

> "Lütfen benim için kimlik bilgilerini bul."

ADC daha sonra şu sırayla arar.

---

## Adım 1

Kontrol et:

```
GOOGLE_APPLICATION_CREDENTIALS
```

Bu ortam değişkeni varsa,

ADC o JSON dosyasını yükler.

---

## Adım 2

Bulunmazsa,

bak:

```
gcloud auth application-default login
```

kimlik bilgileri.

Geliştirme sırasında kullanılır.

---

## Adım 3

Hâlâ hiçbir şey yoksa,

bak ekli Service Account.

Örnek:

```
Cloud Run

↓

Ekli Service Account
```

ADC otomatik olarak kullanır.

---

## Neden ADC Vardır

Çünkü kodunuz asla değişmez.

Yerel:

```
Geliştirici

↓

ADC

↓

Kullanıcı Kimlik Bilgileri
```

Üretim:

```
Cloud Run

↓

ADC

↓

Ekli Service Account
```

Aynı kod.

Farklı kimlik bilgileri.

Kod değişikliği gerekmez.

---

# OAuth 2.0

Bazen uygulamanız, kullanıcıya ait kaynakları erişmek zorundadır.

Örnek:

```
Kullanıcının Google Drive'ı

Kullanıcının BigQuery Veri Seti
```

Uygulama izin ister.

Kullanıcı onaylar.

Google bir OAuth token döndürür.

Uygulamanız, o token'ı kullanıcı adına kullanır.

---

# Identity-Aware Proxy (IAP)

Normalde,

geliştiriciler uygulamaları kendileri korurlar.

Örnek:

```
if user is admin:
    allow
else:
    deny
```

IAP bu sorumluluğu Google'a aktarır.

```
Kullanıcı

↓

IAP

↓

Cloud Run
```

Google, istek uygulamanıza ulaşmadan önce kimliği kontrol eder.

Uygulamanızda kimlik doğrulama kodu gerekmez.

---

# Firebase Authentication

Firebase Authentication, uygulama kullanıcıları için tasarlanmıştır.

Şunları destekler:

- Email/şifre
- Google
- Apple
- GitHub
- Telefon kimlik doğrulaması

Sağlar:

- Giriş UI
- SDK'lar
- Token oluşturma
- Şifre kurtarma

Mobil ve web uygulamaları için idealdir.

---

# Identity Platform

Identity Platform, özünde kurumsal ortamlar için Firebase Authentication'dır.

Ek özellikler şunları içerir:

- Multi-factor Authentication (MFA)
- OpenID Connect
- SAML
- Daha iyi IAM entegrasyonu
- Identity-Aware Proxy entegrasyonu

---

# Secret Manager

Uygulamalar sık sık şunlar gibi sırlara ihtiyaç duyarlar:

- API anahtarları
- Şifreler
- Sertifikalar
- Veritabanı kimlik bilgileri

Asla şunlarda depolamamalısınız:

- kaynak kodu
- Git depoları
- yapılandırma dosyaları

Bunun yerine Secret Manager'ı kullanın.

```
Uygulama

↓

Secret Manager

↓

Veritabanı Şifresi
```

Avantajları:

- IAM kontrollü erişim
- Sürüm oluşturma
- Denetim günlükleri
- Şifreleme
- Cloud KMS entegrasyonu

Sadece yetkili kullanıcılar ve hizmetler sırları alabilir.

---

# Modül Özeti

Bu modülün sonunda, anlamanız gereken konular:

- Kimlik Doğrulama ve Yetkilendirme arasındaki fark.
- IAM'ın Principals, Roller ve Kaynakları kullanarak erişimi nasıl kontrol ettiği.
- Service Accounts'ın neden uygulamaları temsil ettiği.
- Neden indirilen Service Account anahtarları önerilmiyor.
- ADC'nin kimlik bilgilerini otomatik olarak nasıl keşfettiği.
- Yerel Giriş, Ekli Service Accounts, Workload Identity, Workload Identity Federation veya Service Account Anahtarlarının ne zaman kullanılacağı.
- OAuth 2.0, IAP, Firebase Authentication ve Identity Platform'un amacı.
- Secret Manager'ın hassas kimlik bilgilerini depolamak için önerilen yer olması neden.
