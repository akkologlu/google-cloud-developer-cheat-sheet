# Modül 5 – Uygulamanıza Zeka Ekleme

## Genel Bakış

Makine Öğrenmesi (ML), bilgisayarların verilerden desenleri tanıması ve tahminler yapmasını sağlar.

Karmaşık algoritmalar kendiniz oluşturmak yerine, Google Cloud **önceden eğitilmiş AI modelleri** sağlar ve bunlar basit API'ler aracılığıyla erişilebilir. Sadece birkaç API çağrısı ile, geliştiriciler uygulamalarına görüntü tanıma, konuşma tanıma, çeviri ve belge işleme gibi zeka özellikleri ekleyebilirler.

Bu modül ayrıca **Üretken AI**'yi tanıtır, bu da verileri analiz etmenin ötesine giderek metin, görüntü, kod ve ses gibi tamamen yeni içerik oluşturmaktadır.

---

# Google Cloud Önceden Eğitilmiş AI API'leri

Google Cloud birkaç hazır kullanıma sunan AI hizmetleri sunmaktadır.

En büyük avantaj şudur ki, **makine öğrenmesi bilgisine veya model eğitimine ihtiyacınız yok**. Basitçe verileri bir API'ye gönderirsiniz ve AI tarafından oluşturulmuş sonucu alırsınız.

---

## 1. Vision AI (Görüş AI)

Vision AI görüntüleri analiz eder.

Tipik yetenekleri şunları içerir:

- Nesne tespiti
- OCR (Optik Karakter Tanıma)
- Yüz tespiti
- Logo tespiti
- Yer işareti tanıma
- Açık içerik tespiti
- Görüntü etiketlemesi

### Örnek

Bir fatura görüntüsü yükleyin.

Vision API şunları çıkarır:

- Mağaza adı
- Tarih
- Toplam fiyat

Bir düğün fotoğrafı yükleyin.

Vision API şunları tanımlayabilir:

- Yüzler
- Gülümseme
- Duygusal ifadeler

---

## 2. Speech-to-Text (Konuşmadan Metne)

Konuşmayı metne dönüştürür.

100'den fazla dili destekler.

Yaygın kullanım alanları:

- Sesli asistanlar
- Toplantı transkripsiyonu
- Sesli komutlar
- Çağrı merkezi transkripsiyonu

Örnek:

```
Ses:
"Işıkları aç"

↓

Metin:
"Işıkları aç"
```

---

## 3. Text-to-Speech (Metinden Konuşmaya)

Metni gerçekçi konuşmaya dönüştürür.

Yaygın kullanım alanları:

- Erişebilirlik
- Sesli asistanlar
- Navigasyon sistemleri
- Sesli kitaplar

---

## 4. Translation AI (Çeviri AI)

Metni otomatik olarak başka bir dile çevirir.

Örnek:

```
Hello

↓

Merhaba
```

Yararlı olduğu yerler:

- Çok dilli web siteleri
- Sohbet uygulamaları
- Müşteri desteği
- Küresel ürünler

---

## 5. Natural Language AI (Doğal Dil AI)

Metni analiz ederek anlamını anlar.

Yetenekleri şunları içerir:

- Duygu Analizi
- Varlık Çıkarma
- Söz Dizimi Analizi
- Niyet Tespiti

### Örnek

Müşteri yorumu:

> "Teslimat yavaştı ama ürün mükemmel."

Natural Language API şunları belirleyebilir:

Duygu

```
Olumlu
```

Varlıklar

```
Ürün
Teslimat
```

---

## 6. Video Intelligence AI (Video Zeka AI)

Videoları analiz eder.

Şunları tespit edebilir:

- Nesneler
- Sahneler
- Faaliyetler
- Zaman konumları

Örnek:

Bir güvenlik kamerası videosu.

API şunları raporlayabilir:

```
00:05 Bir kişi giriyor

01:10 Araba tespit edildi

02:20 Köpek tespit edildi
```

---

## 7. Document AI (Belge AI)

Yapısız belgeleri yapılandırılmış veriye dönüştürür.

Desteklenen belgeler şunları içerir:

- Faturalar
- Sözleşmeler
- Kimlikler
- Formlar
- Fişler

Örnek:

Giriş:

```
PDF Fatura
```

Çıkış:

```
Fatura Numarası

Müşteri Adı

Toplam

Vergi
```

Belge işlemesini otomatikleştirmek için mükemmel.

---

## 8. AutoML

AutoML, ML uzmanlığı olmayan geliştiricilerin özel modeller eğitmesine izin verir.

Desteklenen veri türleri:

- Görüntüler
- Videolar
- Tablolar

Kodlama gerekmez.

Örnek:

Bir şirketin 20.000 ürün görüntüsü vardır.

Google'ın genel görüntü modelini kullanmak yerine, kendi ürünlerini tanımak için kendi modellerini eğitirler.

---

## 9. Özel Makine Öğrenmesi

Önceden eğitilmiş API'ler yetersiz kalırsa, geliştiriciler kendi modellerini oluşturabilirler.

Popüler çerçeveler:

- TensorFlow
- PyTorch

Bu tam esneklik sağlar ancak ML bilgisi gerektirir.

---

# Önceden Eğitilmiş API'ler Nasıl Çalışır

İş akışı basittir.

```
Uygulama

↓

REST API

↓

Google AI Modeli

↓

JSON Yanıtı
```

Örnek:

Uygulama gönderir:

```
Görüntü
```

Vision API döndürür:

```json
{
  "label": "Köpek",
  "confidence": 98%
}
```

Model eğitimi gerekmez.

---

# Örnek Kullanım Durumu

Sosyal medya uygulaması oluşturduğunuzu varsayın.

Bir kullanıcı fotoğraf yüklediğinde:

1. Görüntüyü Cloud Storage'da saklayın
2. Vision API'yi çağırın
3. Etiketleri alın
4. Etiketleri Firestore'da kaydedin
5. Görüntü aramasını etkinleştirin

Uygulama sadece birkaç API çağrısıyla "akıllı" hale gelir.

---

# Geleneksel Programlama vs Makine Öğrenmesi vs Üretken AI

Bu üç konsept arasındaki farkı anlamak çok önemlidir.

---

## Geleneksel Programlama

Her kuralı açıkça tanımlarsınız.

```
Kurallar
+
Girdi

↓

Cevap
```

Örnek:

```
EĞER hayvan var ise
4 bacak
2 kulak
kürk

O ZAMAN

Kedi
```

Problem:

Her olası kuralı yazmak imkansızdır.

---

## Makine Öğrenmesi

Kurallar yazmak yerine, örnekler sağlarsınız.

```
Veriler
+
Doğru Cevaplar

↓

Model kuralları öğrenir

↓

Tahmin
```

Örnek:

Modele gösterin:

- 1.000 kedi resmi
- 1.000 köpek resmi

Model farkları öğrenir.

Yeni bir görüntü geldiğinde:

```
Tahmin:

Kedi
```

Makine Öğrenmesi, belirli bir sorunu çözmek için mükemmel.

---

## Üretken AI

Üretken AI çok daha geniş kapsamlıdır.

Bir görevi öğrenmek yerine, devasa veri setlerinden bilgi öğrenir.

```
İnternet

Kitaplar

Görüntüler

Kod

Videolar

↓

Eğitim

↓

Temel Model

↓

İstem

↓

Oluşturulan Yanıt
```

Verileri basitçe tanımak yerine, yeni içerik oluşturur.

Örnekler:

- Makaleler yazma
- Kod üretme
- Soruları yanıtlama
- Görüntüler oluşturma
- Belgeleri özetleme

---

# Temel Modeller

Temel Model, devasa veri setleriyle eğitilmiş çok büyük bir AI modelidir.

Zaten anlar:

- Dil
- Görüntüler
- Mantık
- Programlama
- Muhakeme

Sıfırdan eğitmek yerine, geliştiriciler basitçe mevcut modeli kullanırlar.

Örnekler şunları içerir:

- Gemini
- GPT
- Claude

---

# Büyük Dil Modelleri (LLM'ler)

LLM'ler, dil için özel kılınmış Temel Modellerdir.

Bağlama göre bir sonraki en olası kelimeyi tahmin ederler.

Örnek istem:

```
Cloud Run'ı açıkla
```

Model tam bir açıklama oluşturur.

---

## Neden "Büyük" Denir?

Çünkü sahiptirler:

### Devasa veri setleri

Bazen Petabayt veri.

### Milyarlar veya trilyonlar parametreler

Parametreler, modelin eğitim sırasında öğrendiği her şeyi temsil eder.

Daha fazla parametre genellikle şu anlama gelir:

- Daha iyi muhakeme
- Daha iyi dil anlayışı
- Daha iyi tahminler

---

# Ön Eğitim vs İnce Ayar

## Ön Eğitim

Google, devasa veri setleri kullanarak genel amaçlı bir model eğitir.

Sonuç bir Temel Modeldir.

---

## İnce Ayar

Daha sonra, bir şirket kendi verilerini kullanarak o modeli eğitmeye devam edebilir.

Örnek:

Genel model:

Tıp bilir.

Hastane, şunları kullanarak ince ayar yapar:

- Tıbbi yönetmelikler
- İç belgeler
- Araştırma makaleleri

Şimdi model bir tıbbi asistan haline gelir.

---

# İstem (Prompt)

İstem, modele verdiğiniz talimattır.

Örnekler:

```
Bu makaleyi özetle.

Kubernetes'i açıkla.

React kodu oluştur.

Bu cümleyi çevir.
```

İstem ne kadar iyiyse, yanıt o kadar iyi.

---

# Üretken AI Kullanım Durumları

## İçerik Oluşturma

Oluşturun:

- Makaleler
- E-postalar
- Hikayeler
- Görüntüler
- Pazarlama içeriği

---

## Bilgi Özetlemesi

Örnekler:

- PDF'leri özetle
- Toplantıları özetle
- Videoları özetle
- Uzun makaleleri özetle

---

## Arama ve Keşif

Örnekler:

- Anlamsal arama
- Ürün önerileri
- Belge arama

---

## İş Akışı Otomasyonu

Örnekler:

- Sözleşme çıkarma
- Bilet sınıflandırması
- Fatura işleme
- Müşteri desteği otomasyonu

---

# Yazılım Geliştirme için AI

Üretken AI yazılım geliştirmeyi dönüştürüyor.

Modern kodlama asistanları (Gemini gibi) şunları yapabilir:

## Kod Oluşturma

Örnek:

```
Bir React giriş sayfası oluştur.
```

---

## Kodu Açıklama

```
Bu fonksiyonu açıkla.
```

---

## Hataları Düzeltme

```
Hatayı bul ve düzelt.
```

---

## Birim Testleri Oluşturma

```
Jest testleri oluştur.
```

---

## Kodu Tamamlama

Yazarken, AI kalan kodu tahmin eder.

---

## Kodu Çevirme

Örnek:

```
Python

↓

Java
```

---

## Dokümantasyon Oluşturma

AI otomatik olarak şunları üretebilir:

- Yorumlar
- README dosyaları
- API dokümantasyonu
- Yayın notları

---

# Özet

| Hizmet               | Amaç                                    |
| -------------------- | --------------------------------------- |
| Vision AI            | Görüntüleri analiz et                   |
| Speech-to-Text       | Ses → Metin                             |
| Text-to-Speech       | Metin → Ses                             |
| Translation AI       | Dil çevirisi                            |
| Natural Language AI  | Metni anla                              |
| Video Intelligence   | Videoları analiz et                     |
| Document AI          | Belgelerden yapılandırılmış veri çıkart |
| AutoML               | Kodlama olmadan özel ML modelleri eğit  |
| TensorFlow / PyTorch | Tamamen özel ML modelleri oluştur       |

---

# Önemli Noktalar

- Google Cloud, ML uzmanlığı gerektirmeyen önceden eğitilmiş AI API'leri sunmaktadır.
- Vision AI, Speech, Translation, Natural Language, Video AI ve Document AI yaygın AI sorunlarını çözer.
- AutoML, ML kodu yazmadan özel model eğitimine izin verir.
- Geleneksel programlama, manuel olarak yazılmış kurallar kullanır.
- Makine Öğrenmesi, etiketli örneklerden kuralları öğrenir.
- Üretken AI, devasa veri setlerinden öğrenir ve yeni içerik oluşturur.
- Temel Modeller, doğrudan kullanılabilen veya ince ayar yapılabilen büyük önceden eğitilmiş modellerdir.
- Büyük Dil Modelleri (LLM'ler), dil anlayışı ve üretimine uzmanlaşmıştır.
- İstemler, jeneratif AI modellerine verilen talimatlardır.
- Gemini gibi AI kodlama asistanları, geliştiricilerin kod oluşturmasına, açıklamasına, optimize etmesine ve test etmesine yardımcı olur.
