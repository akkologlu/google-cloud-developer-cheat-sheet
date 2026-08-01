# Google Cloud'a Giriş

> "Bulut bilişim, sunucu kiralamaktan ibaret değildir. İtfaiye hizmetlerinize odaklanmanız yerine uygulamanıza odaklanmaktır."

---

## 🎯 Öğrenme Hedefleri

Bu bölümün sonunda, aşağıdaki soruları cevaplayabilmelisiniz:

- Bulut Bilişim Nedir?
- Şirketler Neden Google Cloud Kullanırlar?
- Altyapı Hizmetleri Olarak (IaaS) ile Platform Hizmetleri Olarak (PaaS) Arasındaki Fark Nedir?
- Yönetilen Hizmet Nedir?
- Bulut Bilişim Neden Modern Yazılım Geliştirmenin Standardı Haline Geldi?

---

# 🤔 Neden Bu Var?

Yeni bir web uygulaması oluşturduğunuzü hayal edin.

On yıl önce, tek bir kod satırı yazmadan önce, önce fiziksel sunucular satın almanız, bir sunucu odası hazırlamanız, işletim sistemleri kurmanız, ağı yapılandırmanız ve donanımı bakımını yapmanız gerekiyordu.

```text
Uygulama
    │
Sunucu Satın Al
    │
İşletim Sistemi Kur
    │
Ağı Yapılandır
    │
Donanım Bakımını Yap
    │
Uygulamayı Dağıt
```

Bu süreç pahalı, yavaş ve ölçeklemesi zorluydu.

Bulut bilişim bu modeli tamamen değiştirdi.

Şirketler artık altyapı satın almak yerine, ihtiyaç duydukları zaman bilişim kaynaklarını kiralayabilirler.

Sonuç olarak:

- Altyapı haftalar yerine dakikalar içinde sağlanabilir.
- Kaynaklar talebe göre otomatik olarak ölçeklenebilir.
- Şirketler sadece gerçekten kullandıkları kaynaklar için ödeme yaparlar.
- Geliştirici, sunucu yönetimine harcanan süre yerine yazılım oluşturmaya daha fazla zaman ayırırlar.

Google Cloud, bunu mümkün kılan en büyük bulut bilişim platformlarından biridir.

---

# ☁️ Google Cloud Nedir?

Google Cloud, Google'ın bulut bilişim platformudur.

Fiziksel altyapıya sahip olmadan geliştiricilerin uygulamalar oluşturmalarına, dağıtmalarına ve işletmelerine yardımcı olan yüzlerce hizmet sağlar.

Bu hizmetler şunları içerir:

- İşlem Gücü
- Ağ Oluşturma
- Depolama
- Veritabanları
- Güvenlik
- Yapay Zeka
- Analitik
- DevOps

Donanım satın almak yerine, geliştiriciler bu hizmetleri isteğe bağlı olarak tüketirler.

Google temel altyapıyı yönetirken, geliştiriciler işletme değeri sunmaya odaklanır.

---

# ⚙️ Yönetilen Hizmetler

Google Cloud'daki en önemli kavramlardan biri **yönetilen hizmetler** fikridir.

Yönetilen hizmet, Google'ın temel altyapının bir kısmından (veya tamamından) işletilmesinden sorumlu olduğu anlamına gelir.

Örneğin, Cloud SQL kullanırken:

**Sizin sorumlu olduğunuz:**

- Veritabanınızı tasarlama
- SQL sorguları yazma
- Uygulama verilerinizi yönetme

**Google'ın sorumlu olduğu:**

- Sunucuları yönetme
- Güncellemeleri yükleme
- Donanım bakımı
- Altyapı kullanılabilirliği
- İsteğe bağlı yedeklemeler ve kurtarma

Bu paylaşılan sorumluluk, geliştiricilerin işlemler üzerine daha az zaman harcamalarına ve yazılım geliştirmeye daha fazla zaman ayırmalarına olanak tanır.

> **Temel fikir:** Bir hizmet ne kadar "yönetilen" ise, o kadar az operasyonel çalışma yapmanız gerekir.

---

# 🏗️ Altyapı Hizmetleri Olarak (IaaS)

Bazen geliştiriciler ortamları üzerinde tam kontrol gereksinim duyarlar.

Şunları yapmaları gerekebilir:

- İşletim sistemini seç
- Özel yazılım yükle
- Ağ yapılandır
- Çalışma zamanı ortamını ayarla

Altyapı Hizmetleri Olarak (IaaS) bu esnekliği sağlar.

Google sanal altyapıyı sağlarken, içinde çalışan her şeyi siz yönetirsiniz.

İyi bir örnek **Compute Engine**, İçinde Sanal Makine (VM) örnekleri oluşturduğunuz ve kendiniz yapılandırdığınızdir.

### Sorumluluk Bölüşümü

| Google             | Siz               |
| ------------------ | ----------------- |
| Fiziksel sunucular | İşletim Sistemi   |
| Ağ altyapısı       | Çalışma Zamanı    |
| Depolama donanımı  | Yüklü yazılım     |
| Veri merkezleri    | Sizin uygulamanız |

### 🧠 Analoji

**Boş bir daire kiralamayı** düşünün.

Bina zaten var, ama içindeki her şeyi nasıl döşeyip düzenleyeceğine siz karar verirsiniz.

---

# 🚀 Platform Hizmetleri Olarak (PaaS)

Bazen geliştiriciler sunucuları hiç yönetmek istemezler.

Sadece uygulamalarını dağıtmak ve platformun geri kalanını halletmesine izin vermek isterleri.

Platform Hizmetleri Olarak (PaaS) tam olarak bunu sağlar.

**Cloud Run** gibi hizmetlerle, Google yönetir:

- Altyapı
- İşletim sistemi
- Çalışma zamanı ortamı
- Ölçeklendirme
- Kullanılabilirlik

Sadece uygulamanızı sağlarsınız.

### 🧠 Analoji

Boş bir daire kiralamak yerine, tam donanımlı bir ofise taşınıyorsunuz.

Elektrik, internet, mobilya ve bakım zaten hazırdır.

Sadece gelip çalışmaya başlarsınız.

---

# 🔄 IaaS ve PaaS Karşılaştırması

| Özellik                      | IaaS           | PaaS      |
| ---------------------------- | -------------- | --------- |
| Altyapı Tarafından Yönetilen | Google         | Google    |
| İşletim Sistemi              | Siz            | Google    |
| Çalışma Zamanı               | Siz            | Google    |
| Uygulama                     | Siz            | Siz       |
| Esneklik                     | Yüksek         | Orta      |
| Operasyonel Ek Yük           | Yüksek         | Düşük     |
| Örnek                        | Compute Engine | Cloud Run |

Evrensel olarak "daha iyi" bir seçenek yoktur.

Uygulamanızın gereksinimlerine en uygun olanı seçin.

- Maksimum esnekliğe ihtiyacınız mı? → IaaS
- Geliştirmeye odaklanmak istiyor musunuz? → PaaS

---

# 🏗️ Genel Resim

Bulut, geliştiricilerin altyapı sorumluluklarını kademeli olarak devretmelerine olanak tanır.

```text
Geleneksel Altyapı

Her şeyi siz yönetirsiniz
────────────────────────────

Bulut (IaaS)

Google donanımı yönetir
Siz yazılımı yönetirsiniz

────────────────────────────

Bulut (PaaS)

Google neredeyse her şeyi yönetir
Siz kod yazarsınız
```

Google Cloud, bu tüm spektrum boyunca hizmetler sunmaktadır.

---

# 💡 Gerçek Dünya Örneği

Bir startup oluşturduğunuzu hayal edin.

İlk başlarda, her gün uygulamanıza sadece 100 kullanıcı ziyaret eder.

Birkaç ay sonra, uygulamanız viral olur ve aniden yüzbinlerce kullanıcı alır.

Geleneksel altyapıyla, ek sunucular satın almanız ve kapasitenizi manuel olarak genişletmeniz gerekecektir.

Google Cloud sayesinde, birçok hizmet artan talebi karşılamak için otomatik olarak ek kaynaklar tahsis edebilir.

Bu elastikiyet, bulut bilişimin tanımlayıcı özelliklerinden biridir.

---

# 🧠 Analoji

Bir restoran açtığınızı hayal edin.

### Geleneksel Altyapı

Binaları, mobilyaları, mutfak ekipmanlarını satın alırsınız ve bakım personeli kiralarsınız.

Her şey size aittir.

### Bulut Bilişim

Tamamen yönetilen bir mutfak kiralarsınız.

Sadece harika yemek pişirmeye odaklanırsınız.

Google bina, elektrik, bakım ve daha fazla müşteri geldiğinde genişletmeyi halleder.

---

# 📌 Önemli Noktalar

- Bulut bilişim, donanım sahipliğini isteğe bağlı hizmetlerle değiştirir.
- Google Cloud yüzlerce yönetilen hizmet sağlar.
- Yönetilen hizmetler operasyonel ek yükü azaltır.
- IaaS, size işletim sistemi ve çalışma zamanı kontrolü vererek maksimum esneklik sağlar.
- PaaS, geliştiricilerin öncelikle uygulama geliştirmeye odaklanmalarını sağlar.
- Modern bulut platformları, uygulamaların geleneksel altyapıdan çok daha hızlı ölçeklemesini mümkün kılar.

---

# 🎯 Sınav Notları

Professional Cloud Developer sertifikasyonu için unutmayın:

- **Compute Engine**, **Altyapı Hizmetleri Olarak (IaaS)** örneğidir.
- **Cloud Run**, **Platform Hizmetleri Olarak (PaaS)** örneğidir.
- Google Cloud genel olarak, gereksinimlerini karşılayan en yüksek soyutlama düzeyini kullanmanızı teşvik eder.
- Yönetilen hizmetler operasyonel karmaşıklığı azaltır ve düşük seviyeli özelleştirme gerekli olmadıkça genellikle tercih edilir.

---

# ⚠️ Yaygın Hatalar

### ❌ "Bulut, birinin bilgisayarıdır."

Teknik olarak doğru, ama eksik.

Bulut bilişim, uygulamaları uzak sunucularda barındırmaktan ziyade **bilişim kaynaklarını hizmet olarak tüketmek** hakkındadır.

---

### ❌ PaaS her zaman IaaS'dan daha iyidir.

Mutlaka değil.

PaaS operasyonel çalışmayı azaltır, ancak IaaS daha fazla esneklik ve özelleştirme sağlar.

Doğru seçim uygulamanın gereksinimlerine bağlıdır.

---

### ❌ Yönetilen, sıfır sorumluluk anlamına gelir.

Yönetilen hizmetler operasyonel sorumluluklarınızı azaltır, ama yine de uygulamanız, yapılandırma, güvenlik ve verilerinizden sorumlusunuz.

---

# 🔗 İlişkili Hizmetler

- Compute Engine
- Cloud Run
- Cloud SQL
- Google Kubernetes Engine (GKE)
- Cloud Storage

---

# 📚 Daha Fazla Okuma

- Google Cloud Kaynak Hiyerarşisi
- Kimlik ve Erişim Yönetimi (IAM)
- Compute Engine
- Cloud Run

---

> **Unutmayın:** Google Cloud, sadece bir hizmetler koleksiyonu değildir. Geliştiricilerin altyapı yönetimine daha az zaman ayırmasını ve yazılım oluşturmaya daha fazla zaman ayırmasını sağlamak için tasarlanmış bir platformdur.
