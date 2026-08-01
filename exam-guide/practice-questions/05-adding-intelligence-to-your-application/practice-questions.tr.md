# Modül 5 — Uygulamana Zekâ Ekleme: Alıştırma Soruları

Professional Cloud Developer sertifikasyon yolundaki **Adding Intelligence to Your Application (Uygulamana Zekâ Ekleme)** modülü için senaryo tabanlı alıştırma soruları. Bu sorular, deep dive metnindeki sınav tuzaklarına ve karar tablosu ayrımlarına ağırlık verir — özellikle önceden eğitilmiş API vs AutoML vs özel model vs generative AI ayrımına, ve "dar ML vs generative AI" sınırına.

Önce **Sorular** bölümünü çöz, sonra cevaplarını **Cevap Anahtarı ve Açıklamalar** ile karşılaştır.

---

## Sorular

**1.** Bir fotoğraf paylaşım uygulaması, bir düğün fotoğrafındaki hangi misafirlerin gülümsediğini, şaşırdığını ya da şapka/gözlük gibi baş giyimi taktığını — sadece görseli analiz ederek — otomatik olarak etiketlemek istiyor. Geliştirici hangi yeteneği kullanmalı?

A. Vision AI, yüz tespiti (face detection) yeteneğini kullanarak
B. Natural Language AI, çünkü duygu (sentiment) tespit edebilir
C. Video AI, çünkü fotoğraflar tek kareli video olarak ele alınabilir
D. Document AI, çünkü yüzler bir tür yapılandırılmamış veridir

**2.** Bir seyahat uygulaması, kullanıcının yüklediği fotoğraftaki nirengi noktasını (landmark) tanımlamasını backend'den istiyor. Bir kullanıcı bir Sfenks heykeli fotoğrafı yüklüyor ve uygulama, bunun Mısır'daki Giza'daki orijinal değil, bir Las Vegas otelinin önündeki kopyası olduğunu doğru biçimde bildiriyor. Bu düzeydeki bağlamsal isabeti, hiçbir ek eğitim olmadan hangi servis sağlıyor?

A. Özellikle Sfenks fotoğrafları üzerinde eğitilmiş özel bir AutoML görüntü sınıflandırıcısı
B. Bir görsel açıklaması (caption) üreten generative AI
C. Vision AI'ın landmark (nirengi noktası) tespiti
D. Document AI'ın yapılandırılmış veri çıkarımı

**3.** Bir e-ticaret sitesi dünya çapında ziyaretçilere hizmet veriyor ve ürün açıklamalarının her ziyaretçinin tarayıcı diline göre, istek anında anlık olarak üretilmesini istiyor — her yerel dil (locale) için önceden çevrilmiş statik dosyalar halinde tutulmasını değil. Hangi yaklaşım buna en uygun?

A. Desteklenen her yerel dil için, her ürün sayfasının çevrilmiş bir kopyasını önceden üretip saklamak
B. Document AI
C. Natural Language AI
D. Cloud Translation API, metni istek anında dinamik olarak çevirerek

**4.** Bir şirket, ürünüyle ilgili sosyal medya paylaşımlarını izleyerek müşterilerin memnun olup olmadığını anlamak istiyor; ayrıca müşterinin satın almak, şikayet etmek ya da destek istemek niyetinde olup olmadığına göre mesajları otomatik olarak yönlendirmek istiyor. Bir geliştirici, her mesajı özel bir prompt ile genel amaçlı bir LLM'den geçirerek duygu ve niyet çıkarmayı öneriyor. Bu, en uygun yaklaşım mı?

A. Evet — metinden duygu ve niyeti sadece genel amaçlı bir LLM (generative AI) belirleyebilir
B. Hayır — Natural Language AI, metinden duygu ve niyet çıkarmak için özel olarak tasarlanmıştır ve bu dar, iyi tanımlı görev için daha uygundur
C. Hayır — bu iş Video AI gerektirir, çünkü sosyal medya paylaşımları genelde görsel ve video da içerir
D. Evet — önce metni işlemek için Speech-to-Text kullanılmalı

**5.** Bir medya şirketinin büyük bir video arşivi var ve saatlerce süren görüntü içinde belirli bir şirket logosunun ekranda göründüğü her anı, kesin zaman damgasıyla birlikte bulması gerekiyor. Bu iş için hangi servis tasarlanmıştır?

A. Video AI (Video Intelligence API), varlıkları çekim, kare ya da video düzeyinde, zamanlamayla birlikte açıklayan (annotate eden) servis
B. Vision AI, çünkü zaten görsellerde logo tespiti yapabiliyor
C. Document AI
D. Natural Language AI

**6.** Bir finans ekibinin, tutarsız düzenlerde ve yazı tiplerinde binlerce taranmış faturası var. Sayfadan sadece ham metnin çekilmesini değil; fatura tarihi, toplam tutar ve tedarikçi adı gibi tutarlı, sorgulanabilir alanların otomatik olarak çıkarılmasını istiyorlar. Hangi servis buna uygun?

A. Vision AI'ın OCR yeteneği, taranmış sayfalardan ham metin çıkarır
B. Translation AI
C. Document AI, yapılandırılmamış belgeleri yapılandırılmış veriye dönüştürür
D. Agent Platform AutoML

**7.** Butik bir perakendecinin, tablolar halinde yıllara dayanan kendine özel satış verisi var ve bu veriyle müşteri kaybını (churn) tahmin eden bir model eğitmek istiyor. Bünyelerinde hiç makine öğrenmesi mühendisi yok ve hiç eğitim kodu yazmak istemiyorlar. Hangi seçenek uygun?

A. Önceden eğitilmiş bir API, çünkü hiç ML bilgisi gerektirmiyor
B. TensorFlow ya da PyTorch ile inşa edilmiş özel bir model
C. Büyük dil modelini (LLM) kendi tablo verileri üzerinde ince ayar (fine-tune) yaparak
D. Agent Platform AutoML, kendi verileriyle hiç kod yazmadan özel bir model eğiterek

**8.** Bir robotik şirketinin, hiçbir standart Google Cloud API'sinin kapsamadığı, sensör füzyonuna dayalı çok özel bir problem için son derece uzmanlaşmış bir modele ihtiyacı var. Model mimarisi üzerinde tam kontrol isteyen deneyimli bir ML mühendisleri ekibi çalıştırıyorlar. Hangi seçenek en uygun?

A. Agent Platform AutoML
B. TensorFlow ya da PyTorch kullanarak özel bir model inşa edip eğitmek
C. Önceden eğitilmiş bir API
D. Generative AI ile içerik oluşturma

**9.** Bir geliştirici şu açık mantığı yazıyor: `if legs == 4 and ears == 2 and has_fur: classify as cat`. Bu hangi yaklaşımı temsil ediyor, ve makine öğrenmesi ile generative AI ile ilişkisi nedir?

A. Geleneksel programlama — kuralı bir insan elle yazdı; bu, kuralları veriden öğrenen ML modellerinden farklı, daha eski bir yaklaşımdır
B. Dar makine öğrenmesi — makine bu kuralı etiketlenmiş kedi fotoğraflarından öğrendi
C. Generative AI — sistem talep üzerine yeni sınıflandırma kuralları üretiyor
D. Agent Platform AutoML — kural, hiç kod yazılmadan otomatik olarak çıkarıldı

**10.** Bir startup, kullanıcının "golden retriever'lar hakkında bildiğin her şeyi anlat" yazdığında zengin, serbestçe kurgulanmış bir paragraf yanıtı aldığı bir özellik istiyor — sadece "köpek: evet" gibi bir etiket değil. Hangi paradigma buna uyuyor, ve dar, önceden eğitilmiş bir sınıflandırma API'si burada neden yetersiz kalıyor?

A. Vision AI — yüklenen bir fotoğraftan "köpek" kavramını etiketleyebilir
B. Natural Language AI — sorgu metninden yapılandırılmış varlıklar (entities) çıkarır
C. Generative AI (LLM) — devasa, çok modlu veriyle eğitilmiş, dar bir sınıflandırma yerine yeni, açık uçlu içerik üretir
D. Agent Platform AutoML — golden retriever görselleri üzerinde özel bir sınıflandırıcı eğit

**11.** "Foundation model" ile "büyük dil modeli (LLM)" arasındaki farkı doğru biçimde ifade eden seçenek hangisidir?

A. İkisi aynı şeydir; terimler her bağlamda birbirinin yerine kullanılabilir
B. Foundation model her zaman dar-amaçlıdır, LLM ise her zaman genel amaçlıdır
C. Foundation model kullanılmadan önce her zaman ince ayar (fine-tune) yapılır, LLM ise her zaman hiç ince ayar yapılmadan doğrudan kullanılır
D. LLM, foundation model'in en popüler türüdür ve yalnızca metin verisiyle eğitilir; foundation model'ler genel olarak görüntü ya da kod gibi başka veri tipleriyle de eğitilebilir

**12.** Bir hukuk teknolojisi şirketi, genel amaçlı bir LLM'in kendi firmalarının spesifik stiline ve madde kütüphanesine uygun sözleşme dili üretmesini istiyor. Var olan modeli, geçmiş sözleşmelerden oluşan küçük bir iç veri kümesi üzerinde daha fazla eğitiyorlar. Bu, hangi süreçtir?

A. Pre-training (ön eğitim) — kendi sözleşmelerini kullanarak sıfırdan yepyeni bir foundation model inşa etmek
B. Fine-tuning (ince ayar) — var olan önceden eğitilmiş modeli, daha küçük, alana özel bir veri kümesi üzerinde daha fazla eğitmek
C. Agent Platform AutoML — kodsuz bir görüntü sınıflandırıcısı eğitmek
D. Sadece prompt mühendisliği, ek bir model eğitimi olmadan

**13.** Bir ürün yöneticisi, bir LLM'in "büyük (large)" olarak adlandırılmasının sadece sahip olduğu parametre sayısından kaynaklandığını iddia ediyor. Bu doğru mu?

A. Hayır — "büyük", hem eğitim veri kümesinin devasa boyutunu (bazen petabayt ölçeğinde) hem de model eğitim sırasında öğrendiği milyarlarca-trilyonlarca parametreyi birlikte ifade eder
B. Evet — "büyük" yalnızca parametre sayısını ifade eder
C. Evet — "büyük" yalnızca eğitim veri kümesinin boyutunu ifade eder
D. Hayır — "büyük", modelin aynı anda karşılayabildiği API istek sayısını ifade eder

**14.** Google'ın kendi toplantı odası doluluk sistemi şöyle çalışır: her 30 saniyede bir, hareket algılanıp algılanmadığını belirten bir Pub/Sub bildirimi gönderilir; ayrıca bir toplantı başladığında ya da bittiğinde de bildirim gönderilir. Eğer toplantının planlanan başlangıcından 6 ile 8 dakika sonrası arasında hareket algılanırsa oda dolu sayılır; aksi halde oda serbest bırakılır. Bu örnek öncelikle neyi göstermektedir?

A. ML destekli her özelliğin büyük, karmaşık, özel eğitilmiş bir model gerektirdiğini
B. Konferans odası kamera görüntüsünün kare kare analiz edilmesi için Video AI kullanılması gerektiğini
C. Basit bir ML sinyalinin (hareket algılandı mı, algılanmadı mı), doğrudan bir zamanlama/iş mantığı kuralıyla ve olay güdümlü mesajlaşmayla birleştirildiğinde, karmaşık bir model olmadan gerçek bir problemi çözebileceğini
D. Bu senaryonun, odanın durumunu yorumlamak için ince ayarlı bir LLM gerektirdiğini

**15.** Büyük, eski bir kod tabanına yeni katılan bir geliştirici, herhangi bir değişiklik yapmadan önce, tanımadığı bir fonksiyonun ne yaptığını ve nasıl çalıştığını bir yapay zekâ asistanının açıklamasını istiyor. Bu, Gemini destekli hangi kod asistanı yeteneğidir?

A. Kod tamamlama (code completion) — yazarken bir sonraki satırı önerme
B. Kod çevirisi (code translation) — fonksiyonu başka bir programlama diline dönüştürme
C. Dokümantasyon üretimi — son değişiklikler için sürüm notları yazma
D. Kod açıklama (code explanation) — var olan kodun ne yaptığını ve nasıl yaptığını açıklama

---

## Cevap Anahtarı ve Açıklamalar

**1. Doğru cevap: A — Vision AI, yüz tespiti (face detection) yeteneğini kullanarak**
Vision AI'ın yüz tespiti, tespit edilen yüzler hakkında bilgi döndürür — duygusal ifadeler (mutlu, şaşkın) ve baş giyimi dahil. Natural Language AI cazip görünebilir çünkü "duygu (sentiment)" kelimesi benzer geliyor, ama duygu analizi metin üzerinde çalışır, görseldeki yüzler üzerinde değil.

**2. Doğru cevap: C — Vision AI'ın landmark (nirengi noktası) tespiti**
Deep dive metni tam olarak bu örneği veriyor: Vision AI'ın landmark tespiti, Las Vegas'taki kopyayı orijinal Mısır Sfenks'inden hiçbir ek eğitim olmadan ayırt edebiliyor. Özel bir AutoML sınıflandırıcısı eğitmek cazip görünse de gereksizdir — önceden eğitilmiş model bu bağlamsal ayrıntıyı zaten hiç özel eğitim olmadan yakalıyor.

**3. Doğru cevap: D — Cloud Translation API, metni istek anında dinamik olarak çevirerek**
Modül, Cloud Translation API'nin "yüksek yanıt verme hızına" sahip olduğunu ve gerektiği anda hızlı, dinamik çeviri sağladığını vurguluyor. Statik çevrilmiş dosyaları önceden üretip saklamak (A seçeneği), API'nin yerini almayı hedeflediği eski, geleneksel lokalizasyon yaklaşımıdır.

**4. Doğru cevap: B — Hayır, Natural Language AI bu dar, iyi tanımlı görev için özel olarak tasarlanmıştır**
Bu, modülün temel sınav tuzağıdır: önceden eğitilmiş API'ler dar, belirli sorulara (burada duygu ve niyet çıkarma) cevap verir; generative AI ise genel, açık uçlu problemler içindir. Genel amaçlı bir LLM kullanmak teknik olarak mümkündür ama Natural Language AI zaten tam olarak bunu çözerken en uygun, amaca özel çözüm değildir.

**5. Doğru cevap: A — Video AI (Video Intelligence API)**
Video AI, Vision AI'da olmayan zaman boyutunu ekler — sadece bir logonun göründüğünü değil, tam olarak ne zaman ve ne kadar süre göründüğünü, çekim/kare/video düzeyinde bildirebilir. Vision AI cazip görünüyor çünkü o da logo tespiti yapıyor, ama sadece tek tek görseller üzerinde, bir video arşivinde zaman boyunca değil.

**6. Doğru cevap: C — Document AI**
Document AI, düzeni ne olursa olsun, yapılandırılmamış belgeleri yapılandırılmış, sorgulanabilir verilere (örneğin "tarih", "tutar", "tedarikçi" gibi spesifik alanlara) dönüştürmek için özel olarak tasarlanmıştır. Vision AI'ın OCR'ı cazip görünüyor çünkü gerçekten metin çıkarıyor, ama sadece ham metin döndürür — farklı fatura formatları arasında tutarlı yapılandırılmış alanlar üretmez.

**7. Doğru cevap: D — Agent Platform AutoML**
AutoML, hiç ML mühendisi olmayan bir şirketin, kendi özel verisiyle hiç kod yazmadan özel bir model eğitmesini sağlar. Genel bir önceden eğitilmiş API (A seçeneği) de hiç ML bilgisi gerektirmez, ama bu perakendecinin kendi satış verisiyle eğitilemez — sadece Google'ın genel amaçlı modelini sunar.

**8. Doğru cevap: B — TensorFlow ya da PyTorch kullanarak özel bir model inşa edip eğitmek**
Bu senaryo, deep dive metnindeki "spektrumun en ucu" durumudur: hiçbir standart API'nin kapsamadığı çok özel bir problem, artı tam mimari kontrol isteyen gerçek ML uzmanlığına sahip bir ekip. AutoML cazip görünüyor çünkü o da özel veriyle eğitim yapıyor, ama ciddi ML uzmanlığı olmayan ekipler için tasarlanmıştır ve aynı düzeyde mimari kontrol sunmaz.

**9. Doğru cevap: A — Geleneksel programlama**
Kural (`legs == 4 and ears == 2 and has_fur`) bir insan tarafından elle yazıldı — bu, modülün üçlü karşılaştırmasında geleneksel programlamanın tanımıdır. Dar ML cazip görünüyor çünkü o da sınıflandırma yapıyor, ama ML kuralları etiketlenmiş veriden öğrenilir, elle yazılmaz — ve modülün sınav tuzağı, bu üç yaklaşımın (geleneksel programlama, dar ML, generative AI) birbirinin evrimi olduğunu, birbirinin yerine geçen alternatifler olmadığını açıkça vurguluyor.

**10. Doğru cevap: C — Generative AI (LLM)**
Senaryo, dar bir evet/hayır ya da etiket tabanlı cevap değil, açık uçlu, serbestçe kurgulanmış yeni içerik istiyor — tam olarak generative AI'ın doldurmak için var olduğu boşluk. Natural Language AI cazip görünüyor çünkü o da metin işliyor, ama var olan metinden yapılandırılmış anlam (varlıklar, duygu) çıkarır, yeni açık uçlu içerik üretmez.

**11. Doğru cevap: D**
LLM, foundation model'in en popüler türüdür ve yalnızca metinle eğitilir; foundation model'ler daha geniş bir kategori olarak görüntü ya da kod gibi başka veri tipleriyle de eğitilebilir. B seçeneği yaygın bir yanlış okumadır — modül, dar-vs-genel ayrımını foundation model'in kendi bir özelliği olarak değil, önceden eğitilmiş API-vs-generative AI ayrımı olarak çerçeveliyor.

**12. Doğru cevap: B — Fine-tuning (ince ayar)**
Fine-tuning, zaten önceden eğitilmiş bir modelin, daha küçük, alana özel bir veri kümesi (burada firmanın geçmiş sözleşmeleri) üzerinde spesifik bir amaca uyarlanmak için daha fazla eğitilmesidir. Pre-training cazip görünüyor çünkü o da "eğitim"dir, ama pre-training genel amaçlı foundation model'i ilk baştan yaratan büyük ölçekli süreçtir.

**13. Doğru cevap: A**
Modül, LLM bağlamında "büyük" kelimesini aynı anda iki şeyi kapsayacak şekilde tanımlıyor: eğitim veri kümesinin devasa boyutu (bazen petabayt ölçeğinde) ve modelin eğitim sırasında öğrendiği milyarlarca-trilyonlarca parametre. B ve C seçenekleri, doğru tanımın yarısını yakaladıkları için cazip görünüyor, ama hiçbiri tek başına eksiksiz değil.

**14. Doğru cevap: C**
Bu örnek, modülde açıkça "her ML problemi devasa bir model gerektirmez" hatırlatıcısı olarak çerçeveleniyor — basit bir hareket algılandı/algılanmadı sinyali, bir zamanlama kuralıyla (6-8 dakika) ve Pub/Sub olay mesajlaşmasıyla birleştiğinde, gerçek bir iş problemini çözmeye yetiyor. A seçeneği, "ML destekli" ifadesinin her zaman büyük, karmaşık bir model gerektirdiği yönündeki cazip ama yanlış varsayımdır.

**15. Doğru cevap: D — Kod açıklama (code explanation)**
Kod açıklama, tanımadığı var olan kodun ne yaptığını ve nasıl yaptığını açıklamakla ilgilidir — tanımadığı bir kod tabanına yeni katılırken tam olarak değerli olan şey budur. Kod tamamlama cazip görünüyor çünkü o da bir Gemini kodlama yeteneğidir, ama tamamlama yazarken yeni kod tahmin eder — var olan kodu açıklamaz.
