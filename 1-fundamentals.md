# Google Cloud Developer Handbook

> Personal learning handbook for Google Cloud Developer Certification.

---

# Course 01 - Google Cloud Fundamentals

## Section 1 - Introduction to Google Cloud

---

## 🎯 Learning Objectives

Bu bölümün sonunda aşağıdaki sorulara cevap verebiliyor olmalısın:

- Cloud Computing nedir?
- Google Cloud neden kullanılır?
- IaaS ve PaaS arasındaki fark nedir?
- Managed Service ne demektir?

---

## 🤔 Why does this exist?

Eskiden uygulama geliştirmek isteyen şirketler fiziksel sunucular satın almak zorundaydı.

```text
Application
     │
Buy Server
     │
Install OS
     │
Maintain Hardware
     │
Deploy Application
```

Bu yaklaşım pahalı, zaman alıcı ve ölçeklenmesi zordu.

Cloud Computing bu problemi çözer.

Donanımı satın almak yerine ihtiyacın kadar kiralarsın.

> **Cloud Computing = Computing resources as a service.**

---

## ☁️ What is Google Cloud?

Google Cloud, Google'ın sunduğu cloud computing platformudur.

Sadece sanal sunucu sağlamaz.

Aynı zamanda;

- Compute
- Networking
- Storage
- Databases
- AI
- Security
- Analytics

gibi yüzlerce managed service sunar.

Google Cloud'un amacı geliştiricinin altyapıyla değil, uygulamayla ilgilenmesini sağlamaktır.

---

## ⚙️ Managed Service

"Managed" kelimesi Google Cloud'da çok önemlidir.

Managed demek:

> Google bu kısmı senin yerine yönetiyor.

Örneğin Cloud SQL kullanıyorsan;

Sen:

- Database oluşturursun.
- SQL sorgularını yazarsın.

Google:

- Sunucuyu yönetir.
- Güncellemeleri yapar.
- Disk arızalarıyla ilgilenir.
- Backup süreçlerini yönetebilir.

---

## 🏗️ Infrastructure as a Service (IaaS)

IaaS sana altyapıyı sağlar.

Sen;

- Operating System
- Runtime
- Application

kurarsın.

Örnek servis:

- Compute Engine

### 🧠 Analogy

Boş bir ev kiralarsın.

Duvarlar hazırdır.

Ama tüm eşyaları sen yerleştirirsin.

---

## 🚀 Platform as a Service (PaaS)

PaaS'ta Google daha fazla sorumluluk alır.

Sen yalnızca uygulamanı geliştirirsin.

Örnek:

- Cloud Run

Google;

- Server
- Operating System
- Runtime
- Scaling

gibi işleri yönetir.

### 🧠 Analogy

Hazır ofise taşınırsın.

Elektrik, internet ve masa zaten vardır.

Sen sadece çalışmaya başlarsın.

---

## ⚖️ IaaS vs PaaS

| IaaS               | PaaS                         |
| ------------------ | ---------------------------- |
| Daha fazla kontrol | Daha az operasyon            |
| OS yönetirsin      | OS yönetmezsin               |
| VM kullanırsın     | Managed platform kullanırsın |
| Compute Engine     | Cloud Run                    |

---

## 💡 Real World Example

Yeni bir startup kurduğunu düşün.

İlk ay yalnızca 100 kullanıcı var.

Bir gün sosyal medyada viral oluyorsun.

Kullanıcı sayısı 200.000'e çıkıyor.

Cloud Computing sayesinde yeni sunucu satın alman gerekmez.

Google gerekli kaynakları senin yerine sağlayabilir.

---

## 📌 Key Takeaways

- Cloud Computing donanım satın almak yerine kaynak kiralamaktır.
- Google Cloud yüzlerce managed service sunar.
- Managed Service, altyapının bir kısmının Google tarafından yönetilmesidir.
- IaaS daha fazla kontrol sağlar.
- PaaS geliştiricinin operasyon yükünü azaltır.

---

## 🎯 Exam Notes

Bu bölümde en çok karıştırılan konu:

- IaaS → Compute Engine
- PaaS → Cloud Run

Sınavda genellikle:

> "İşletim sistemini yönetmek istemiyorsan hangi servis?"

şeklinde soru gelebilir.

---

## 🔗 Related Topics

- Compute Engine
- Cloud Run
- Resource Hierarchy
- IAM
