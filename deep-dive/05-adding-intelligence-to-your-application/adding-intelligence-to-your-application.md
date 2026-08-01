# Uygulamana Zekâ Ekleme (Adding Intelligence to Your Application) — Baştan Sona Öğretici

> Bu metin, "Developing Applications with Google Cloud: Foundations" kursunun **Modül 5 — Adding Intelligence to Your Application** bölümünde anlatılan **her şeyi** kavratmak için yazıldı. Modül, iki katmanlı bir yolculuk sunuyor: önce Google'ın **önceden eğitilmiş (pre-trained)** makine öğrenmesi API'lerini — görüntü, ses, video, metin üzerinde çalışan hazır zekâ parçalarını — tanıyorsun; sonra bir adım geri çekilip **generative AI (üretken yapay zekâ)** denen daha genel, daha güçlü paradigmayı, onun nasıl çalıştığını ve bir geliştirici olarak seni neden ilgilendirdiğini öğreniyorsun. Amaç, "şu API şunu yapar" listesini ezberletmek değil; **"bu zekâyı neden kendim sıfırdan yazmıyorum, Google'ın hazır modeli bana ne kazandırıyor, ne zaman hangisini seçerim"** sorularının hepsine cevap verebilmeni sağlamak. Sınav notları ve tuzaklar konuların içine yerleştirildi.

---

## Bu modül neyi öğretiyor ve neden önemli?

Bir düşünce deneyiyle başlayalım: İki yaşındaki bir çocuğa bir elma ile bir portakal göster. Çocuk, hiçbir açık talimat almadan, ikisini birbirinden ayırt eder. Bunu nasıl yapar, kendi de bilmez — sadece "bilir." Şimdi aynı işi bir bilgisayara yaptırmaya çalış: Elmanın "kırmızı, yuvarlak, saplı" olduğunu, portakalın "turuncu, pürüzlü kabuklu" olduğunu kurallarla tarif etmeye kalkarsan, çok geçmeden fark edersin ki dünya bu kadar temiz kurallara sığmıyor. Işık değişir, açı değişir, çürük bir elma yeşile döner, bazı portakallar da kırmızıya çalar. **Kural yazarak örüntü (pattern) tanımak, göründüğünden çok daha zordur.**

İşte makine öğrenmesi (machine learning) tam olarak bu boşluğu doldurmak için var: Makinelere, insanların yaptığı gibi **örüntüleri tanımayı öğretmek.** Google, yıllar boyunca devasa veri kümeleri üzerinde eğittiği modelleri — görüntü tanıma, konuşma tanıma, dil anlama gibi alanlarda — **senin kullanımına hazır API'ler olarak** sunuyor. Bunun anlamı şu: Sen bir makine öğrenmesi uzmanı olmak zorunda kalmadan, sadece birkaç satır kodla uygulamana "görme", "duyma", "anlama" yeteneği ekleyebilirsin.

Bu modül iki ana parçadan oluşuyor:

1. **Önceden eğitilmiş makine öğrenmesi API'leri** — Vision AI, Speech-to-Text/Text-to-Speech, Translation AI, Natural Language AI, Video AI, Document AI ve AutoML gibi hazır zekâ parçaları. Bunlar dar, belirli bir problemi (görüntüde nesne tanıma, sesi metne çevirme gibi) son derece iyi çözen modellerdir.
2. **Generative AI (üretken yapay zekâ)** — daha genel, daha esnek bir paradigma. Var olan içerikten öğrenip **yeni** içerik üreten modeller; LLM'ler (Large Language Model) bunun en tanıdık örneğidir. Bu bölümde generative AI'ın geleneksel programlamadan ve dar makine öğrenmesinden nasıl farklı olduğunu, hangi kullanım senaryolarına uygun olduğunu ve bir geliştirici olarak kod yazma sürecini nasıl dönüştürdüğünü öğreneceksin.

Bu iki parça aslında aynı sorunun iki farklı cevabıdır: **"Uygulamama zekâ eklemem gerekiyor, ama ben bir ML araştırmacısı değilim — ne yapmalıyım?"** Cevap, ihtiyacına göre değişir: Belirli, dar bir görevin varsa (örneğin bir görseldeki nesneleri etiketlemek) önceden eğitilmiş bir API yeterlidir. Daha genel, yaratıcı ya da açık uçlu bir iş yapman gerekiyorsa (metin üretmek, kod yazmak, özetlemek) generative AI'a yönelirsin. Şimdi baştan başlayalım.

> **Önceki modüllerle bağ:** İkinci modülde Cloud Code'un IDE entegrasyonlarından ve geliştirici verimliliğinden bahsetmiştik. Bu modülün son bölümü — generative AI'ın kod yazma sürecini nasıl dönüştürdüğü — tam olarak o verimlilik hikâyesinin devamıdır: Artık sadece editörün içinde otomatik tamamlama değil, senin adına kod üreten, açıklayan, hata bulan bir asistan var.

---

# PARÇA 1 — Önceden Eğitilmiş Makine Öğrenmesi API'leri

## Neden hazır model kullanırım, neden kendi modelimi eğitmiyorum?

Makine öğrenmesi modeli eğitmek; devasa miktarda etiketli veri, ciddi hesaplama gücü, ve alanında uzman mühendislik eforu gerektirir. Çoğu şirket ve geliştirici için bu üç kaynağın hiçbiri kolayca bulunmaz. Google ise yıllardır kendi ürünleri (Google Fotoğraflar, Google Arama, Google Asistan gibi) için bu modelleri zaten eğitiyor ve inceltiyor — devasa veri kümeleri üzerinde, dünya çapında milyarlarca kullanıcının geri bildirimiyle.

Google Cloud, bu **zaten eğitilmiş** modelleri, kolay kullanılan API'ler olarak dışarı açıyor. Sonuç: **Hiçbir ML bilgisi gerekmeden**, sadece bir REST API çağrısıyla uygulamana güçlü bir zekâ özelliği ekleyebilirsin. Bu, "tekerleği yeniden icat etmemek" ilkesinin en somut örneklerinden biridir — Google zaten dünyanın en iyi görüntü tanıma modellerinden birini eğitmişken, sen neden sıfırdan kendi modelini eğitmeye kalkasın?

## Nasıl çalışır? REST API mantığı

Bu API'lerin hepsi aynı temel desene uyar: **JSON isteği gönderirsin, JSON yanıtı alırsın.** Örneğin Vision API'ye Cloud Storage'da duran bir görselin konumunu içeren bir JSON isteği gönderirsin; API görseli işler ve sana görseli tarif eden **öznitelikler (attributes)** içeren bir JSON yanıtı döndürür — etiketler, tespit edilen yüzler, okunan metin gibi.

> **Kritik nokta:** Bu API'leri çağırmak için makine öğrenmesi bilmen gerekmez. Senin işin sadece doğru isteği göndermek ve gelen yanıtı yorumlamaktır — modelin içinde ne olduğu, nasıl eğitildiği tamamen Google'ın sorumluluğundadır. Bu, tıpkı bir ödeme API'sini çağırırken kredi kartı işleme altyapısının nasıl çalıştığını bilmen gerekmemesi gibidir.

## Yedi hazır zekâ alanı

Google Cloud'un sunduğu önceden eğitilmiş modelleri, hangi veri tipi üzerinde çalıştıklarına göre gruplayarak öğrenmek en sağlıklısı: görüntü, ses, metin, video ve belge.

### Vision AI — Görüntüyü anlama

**Vision AI**, karmaşık görüntü tespiti (image detection) yapmanı sağlar. Bunu birkaç farklı yetenek altında düşünebilirsin:

- **Nesne etiketleme (labeling).** Bir görseldeki nesneleri kategorilere ayırıp etiketler — "köpek", "araba", "gökyüzü" gibi.
- **Optik karakter tanıma (OCR — optical character recognition).** Görsel içindeki yazılı metni okur ve dijital metne çevirir.
- **Nirengi noktası (landmark) tespiti.** Bir görseldeki tanınmış bir yapıyı ya da coğrafi noktayı tespit eder.
- **Logo tespiti.** Görseldeki marka logolarını tanır.
- **Yüz tespiti.** Görseldeki yüzleri bulur ve bu yüzler hakkında bilgi döndürür — örneğin duygusal ifadeler (mutlu, üzgün, şaşkın) ve baş giyimi (şapka, gözlük gibi).
- **Müstehcen içerik tespiti (explicit content detection).** Görselin uygunsuz içerik barındırıp barındırmadığını değerlendirir — moderasyon sistemlerinde kritik bir yapı taşıdır.

Transkriptteki iki somut örnek, bu yeteneklerin ne kadar ince taneli olduğunu gösteriyor:

- Bir düğün fotoğrafında, API yüzlerdeki duygusal ifadeleri **doğru biçimde** döndürür — kim gülümsüyor, kim heyecanlı, hepsini ayırt edebilir.
- Bir Sfenks (Sphinx) heykeli fotoğrafında, API görüntünün **Mısır'daki değil, Las Vegas'taki** Sfenks kopyası olduğunu doğru tespit eder. Bu, modelin sadece "bu bir Sfenks" demekle kalmayıp, **bağlamsal ayrıntıyı** (hangi Sfenks, nerede) yakalayabildiğini gösterir.

> **Neden bu ayrıntı önemli?** Çünkü bu örnekler, önceden eğitilmiş modellerin ne kadar **derin** bir anlama düzeyine ulaşabildiğini gösteriyor — sadece "bu bir yapı" demekle kalmıyor, hangi spesifik yapı olduğunu, dünyanın neresinde durduğunu ayırt edebiliyor. Bu seviyede bir modeli sıfırdan eğitmek, tek bir şirketin kolayca üstlenebileceği bir iş değildir.

### Speech-to-Text ve Text-to-Speech — Ses ile metin arasında köprü

**Speech-to-Text**, geliştiricilerin sesi (audio) metne çevirmesini sağlar; **Text-to-Speech** ise tam tersini yapar, metni sese çevirir. Speech-to-Text, **110 dil ve varyantını** destekler — bu da uygulamanı küresel bir kullanıcı kitlesine açman için kritik bir özelliktir.

Kullanım senaryoları çeşitlidir:

- Bir uygulamanın mikrofonuna dikte eden kullanıcıların konuşmasını **metne dönüştürmek**.
- Sesle komut-kontrol (**command-and-control**) özellikleri eklemek — kullanıcı konuşarak uygulamayı yönlendirebilir.
- Kayıtlı ses dosyalarını **yazıya dökmek (transcription)**.

> **Sezgi:** Speech-to-Text ve Text-to-Speech'i bir çift kapı gibi düşün — biri sesi metne, diğeri metni sese çeviriyor. İkisini birlikte kullanarak tam sesli bir arayüz (voice interface) kurabilirsin: kullanıcı konuşur, uygulama anlar (Speech-to-Text), bir cevap üretir, ve o cevabı sesli olarak geri okur (Text-to-Speech).

### Translation AI — Diller arası köprü

**Translation AI**, keyfi bir metin dizisini desteklenen herhangi bir dile çevirmeni sağlar. **Cloud Translation API**, yüksek yanıt verme hızına (highly responsive) sahiptir; web siteleri ve uygulamalar, bu API'yi kaynak dilden hedef dile **hızlı, dinamik** çeviri için kullanabilir. "Dinamik" kelimesi burada anahtar: Statik, önceden hazırlanmış çeviri dosyaları yerine, **anlık** olarak, kullanıcı istediği anda çeviri üretebilirsin.

### Natural Language AI — Metnin anlamını çıkarma

**Natural Language AI**, metin belgelerinde, haber makalelerinde ya da blog yazılarında bahsedilen **varlıklar (entities)** hakkında bilgi çıkarmanı sağlar. İki somut kullanım senaryosu öne çıkıyor:

- **Cloud Natural Language API** ile sosyal medyada ürünün hakkındaki **duyguyu (sentiment)** anlayabilirsin — insanlar ürününden memnun mu, şikayetçi mi?
- Müşteri konuşmalarından **niyet (intent) çıkarabilirsin** — kullanıcı bir şey satın almak mı istiyor, şikayet mi ediyor, destek mi arıyor?

> **Neden önemli?** Bu API, ham metni **yapılandırılmış anlam**a dönüştürür. "Bu ürün harika, ama kargo çok geç geldi" cümlesinden bir insan kolayca "olumlu ürün duygusu, olumsuz kargo deneyimi" çıkarır — Natural Language AI, aynı çıkarımı otomatikleştirir.

### Video AI — Video içinde arama ve etiketleme

**Video AI**, video dosyalarını arayarak varlıkları **çekim (shot), kare (frame) ya da video düzeyinde** çıkarmanı ve etiketlemeni sağlar. **Video Intelligence API**, Cloud Storage'da saklanan videoları **açıklar (annotate)** ve videonun içindeki kilit varlıkları — ve bunların videonun **hangi anında** göründüğünü — tespit etmene yardımcı olur.

> **Sezgi:** Vision API tek bir görüntüyü analiz ediyorsa, Video AI aynı analizi **zaman boyutu** ekleyerek yapıyor — "bu nesne videoda ne zaman, ne kadar süre göründü" sorusuna cevap verebiliyor. Bu, video arşivlerinde belirli bir anı aramak (örneğin "bu videoda araba görünen tüm anları bul") gibi işler için paha biçilmez.

### Document AI — Yapılandırılmamış belgeyi yapılandırılmış veriye çevirme

**Document AI**, yapılandırılmamış veriyi (unstructured data) belgelerden alıp **yapılandırılmış veriye (structured data)** dönüştürür — bu da veriyi anlamayı, analiz etmeyi ve tüketmeyi kolaylaştırır. Düşün: Elinde binlerce taranmış fatura, sözleşme ya da form var; her biri farklı düzende, farklı yazı tipinde. Document AI, bu karmaşadan tutarlı, sorgulanabilir alanlar (tarih, tutar, isim gibi) çıkarır.

### AutoML ve özel model eğitimi — Kodsuz makine öğrenmesi ve ötesi

**AutoML on Gemini Enterprise Agent Platform (Agent Platform AutoML)**, sınırlı ML uzmanlığına sahip kullanıcıların, kendi işlerine özel yüksek kaliteli modeller eğitmesini sağlar. Agent Platform AutoML ile **görüntüler, tablo verisi (tabular data) ya da videolar** üzerinde, **hiç kod yazmadan** model eğitebilirsin.

Peki senin verin, Google'ın önceden eğittiği genel modellerin kapsamadığı kadar özelse ne olur? İşte o zaman **kendi özel ML modellerini** kendi verinle inşa edip eğitebilirsin — **TensorFlow** ve **PyTorch** gibi çerçeveleri (framework) kullanarak. Bu, spektrumun en fazla kontrol ve en fazla eforun gerektiği ucudur.

> **Spektrum olarak düşün:** Bir uçta hazır, önceden eğitilmiş API'ler var (Vision AI, Natural Language AI gibi) — sıfır ML bilgisiyle kullanılır ama esnekliği sınırlıdır, çünkü Google'ın genel amaçlı eğittiği modeli kullanırsın. Ortada AutoML var — kod yazmadan **kendi verinle** model eğitirsin, esneklik artar ama hâlâ derin ML bilgisi gerekmez. Diğer uçta TensorFlow/PyTorch ile sıfırdan özel model inşa etmek var — tam kontrol, ama ciddi ML uzmanlığı ve efor gerektirir.

| Yaklaşım | ML bilgisi gerekir mi? | Esneklik | Ne zaman kullanılır |
| --- | --- | --- | --- |
| Önceden eğitilmiş API (Vision, Speech, NL, vb.) | Hayır | Düşük — genel amaçlı | Standart, yaygın bir görevin var (nesne tanıma, çeviri, transkripsiyon) |
| Agent Platform AutoML | Hayır (kodsuz) | Orta — kendi verinle özelleştirme | Kendi işine özel veri var ama ML mühendisin yok |
| TensorFlow / PyTorch ile özel model | Evet | Tam kontrol | Çok özel, standart API'lerin kapsamadığı bir problem |

## Somut bir örnek: Google'ın kendi kullanımı — toplantı odası doluluk tespiti

Modül, Google'ın kendi iç sistemlerinde makine öğrenmesini nasıl kullandığına dair güzel bir örnek veriyor. Google'ın konferans odası sistemleri, **hareket algılama (motion detection)** yaparak video konferans (VC) kamerasıyla **doluluk tespiti (occupancy detection)** yapar:

- Sistem, her **30 saniyede bir**, hareket algılanıp algılanmadığını belirten bir **Pub/Sub** bildirimi gönderir.
- Bir görüşme başladığında ya da bittiğinde de ayrıca bir Pub/Sub bildirimi gönderir.
- Eğer toplantı başlangıcından **6 ile 8 dakika sonrası arasında** hareket algılanırsa, oda **dolu (occupied)** sayılır.
- Aksi halde oda **boş** sayılır ve başka biri tarafından rezerve edilebilir hale gelir.

> **Neden bu örnek değerli?** Çünkü basit bir ML sinyalinin (hareket algılandı mı, algılanmadı mı) nasıl **iş mantığıyla birleştirilerek** gerçek bir problemi (kimse gelmeyen rezervasyonların odayı boşuna kilitlemesi) çözdüğünü gösteriyor. Burada karmaşık bir görüntü tanıma modeli yok — sadece hareket algılama gibi basit bir sinyal, zamanlama kuralıyla (6-8 dakika penceresi) ve olay güdümlü mesajlaşmayla (Pub/Sub) akıllıca bir sisteme dönüşüyor. Bu, "her ML problemi devasa bir model gerektirmez" dersinin de güzel bir hatırlatıcısıdır.

---

# PARÇA 2 — Generative AI (Üretken Yapay Zekâ)

## Generative AI nedir ve neden var?

Şimdiye kadar gördüğümüz API'lerin (Vision, Speech, Natural Language...) hepsi **dar (narrow)** bir görevi çözüyordu: bir görseli sınıflandırmak, bir sesi metne çevirmek, bir metindeki duyguyu tespit etmek. Bu modeller, belirli bir soruya belirli bir cevap veriyorlar — "bu görselde ne var?" ya da "bu ses ne diyor?"

**Generative AI**, bunun bir adım ötesine geçen bir yapay zekâ türüdür: Var olan içerikten öğrendiği şeylere dayanarak **yeni içerik yaratan** bir yapay zekâdır. Fark şuradadır — dar bir modelde makineye "bu bir kedi mi, değil mi?" diye sorarsın ve o sana evet/hayır cevabı verir. Generative AI'da ise makineye "bana bir kedi hakkında ne biliyorsan anlat" dersin, ve o **yepyeni** bir içerik (metin, görsel, ses) üreterek cevap verir.

Bu öğrenme türüne **"eğitim" (training)** denir. Var olan içerik kullanılarak **istatistiksel bir model** oluşturulur. Modele bir **girdi (prompt)** verirsin; model, beklenen bir yanıtı **tahmin eder**. Bu beklenen yanıta dayanarak yeni içerik üretilebilir.

## Nasıl çalışır? Foundation model'den LLM'e

Yapay zekâ, yeni içeriği nasıl üretir? Metin, görsel, ses gibi **devasa miktarda** var olan içerikten öğrenerek. Bu eğitim süreci, bir **"foundation model" (temel model)** ortaya çıkarır.

En popüler foundation model türü, **LLM (Large Language Model)**'dir. LLM'ler yalnızca **metin verisi** üzerinde eğitilir; ama diğer foundation model türleri görüntü ya da programlama kodu gibi başka veri tipleri üzerinde de eğitilebilir.

Foundation model'in iki kullanım biçimi vardır:

1. **Doğrudan kullanım.** Model, içerik üretmek ve genel problemleri çözmek (içerik çıkarma, belge özetleme gibi) için doğrudan kullanılabilir.
2. **İnce ayar (fine-tuning) ile özelleştirme.** Model, kendi alanına ait yeni veri kümeleriyle **daha fazla eğitilerek**, finansal model üretimi ya da sağlık danışmanlığı gibi **spesifik** problemleri çözecek şekilde uyarlanabilir. Bu eğitim, senin özel ihtiyaçlarına göre şekillenmiş **yeni bir model** ortaya çıkarır.

> **Sezgi:** Foundation model'i, çok geniş genel kültürü olan ama henüz hiçbir alanda uzmanlaşmamış bir üniversite mezunu gibi düşün. Fine-tuning, bu mezunu belirli bir şirkette, belirli bir işte staj yaptırıp uzmanlaştırmaya benzer — genel bilgi tabanı aynı kalır, ama artık senin spesifik ihtiyaçlarına göre daha isabetli cevaplar verir.

## Geleneksel programlama, dar makine öğrenmesi ve generative AI — üç farklı yaklaşım

Bu, modülün en kavramsal ve en önemli kısmı: generative AI'ın **neden var olduğunu** anlamak için, ondan önceki iki yaklaşımı ve her birinin nerede tıkandığını görmen gerekiyor.

**1. Geleneksel programlama.** Kuralları **sen** belirlersin, makine bu kurallara göre hareket eder ve cevapları döndürür. Örneğin bir kediyi tanımlamak için şu öznitelikleri (attribute) tanımlayabilirsin: `tür: hayvan`, `bacak: 4`, `kulak: 2`, `kürk: var`, `sevdiği şeyler: yün, kediotu`. Sorun şu: **Bu algoritmaları yazmak zordur, çünkü tüm olası kuralları uygulamak imkânsızdır.** Dünya, temiz kurallara sığmayacak kadar istisna doludur.

**2. Makine öğrenmesi (sinir ağlarıyla).** Bu tıkanıklığı aşmak için yeni bir yöntem gerekir: Makineye **veri ve cevaplar** beslersin, kuralları **kendisinin keşfetmesine** izin verirsin. Örneğin makineyi birçok kedi ve başka hayvan fotoğrafıyla eğitirsin; makine örüntüyü öğrenir ve yeni bir fotoğrafın kedi olup olmadığını tahmin eder. Ama bu tür öğrenme, tipik olarak **dar bir alanda, belirli bir görevi** çözmek için kullanılır — Vision API'nin "bu bir kedi mi" sorusuna cevap vermesi gibi.

**3. Generative AI.** Peki ya makinenin, genel problemleri çözebilecek **temel bir zekâ** geliştirmesini istiyorsan? İşte generative AI bu sorunu çözmeyi hedefler. Generative AI ile makineye **devasa miktarda çok modlu (multimodal) veri** beslersin. Makine, neredeyse sonsuz sayıda kavramı öğrenir ve LLM gibi foundation model'ler geliştirir. Böylece makineye "kedi nedir" diye sorduğunda, öğrendiği **her şeyi** sana verebilir — dar bir "evet/hayır" cevabı değil, zengin, üretken bir cevap.

| Yaklaşım | Kuralları kim belirler? | Kapsam | Örnek |
| --- | --- | --- | --- |
| Geleneksel programlama | Sen (elle) | Sadece tanımladığın kurallar kadar | `if legs == 4 and ears == 2: ...` |
| Makine öğrenmesi (dar) | Makine, veri+cevaplardan öğrenir | Dar, belirli bir görev | "Bu görsel kedi mi değil mi?" (Vision API) |
| Generative AI | Makine, devasa çok modlu veriden öğrenir | Genel, açık uçlu | "Kedi hakkında bana ne anlatabilirsin?" (LLM) |

> **Sınav tuzağı — üç yaklaşımı karıştırma:** Bu üçlü sırasıyla birbirinin **evrimidir**, birbirinin yerine geçen alternatifler değildir. Geleneksel programlama kuralları elle yazmanın imkânsızlığından tıkanır → makine öğrenmesi veri-tabanlı öğrenmeyle bunu çözer ama dar kalır → generative AI, devasa çok modlu veriyle **genel amaçlı** bir zekâ hedefler. Soruda "önceden tanımlı kurallar" geçiyorsa geleneksel programlama; "belirli bir görevi veri ile öğrenen dar model" geçiyorsa makine öğrenmesi; "genel, yaratıcı, yeni içerik üreten" geçiyorsa generative AI'dır.

## LLM (Large Language Model) nedir — "büyük" ve "genel amaçlı" ne demek?

LLM'ler, **önceden eğitilebilen (pre-trained)** ve sonra **belirli amaçlar için ince ayar (fine-tune) yapılabilen** büyük, genel amaçlı dil modelleridir. Peki "büyük" (large) tam olarak ne demek? İki anlamı vardır:

1. **Eğitim veri kümesinin devasa boyutu** — bazen **petabayt** ölçeğine ulaşır.
2. **Parametre sayısı** — artık milyarlara, hatta trilyonlara ulaşıyor. **Parametreler**, makinenin model eğitimi sırasında öğrendiği bellek ve bilgidir; bir modelin bir problemi (örneğin metin tahmin etmeyi) çözme yeteneğini belirler.

**"Genel amaçlı" (general-purpose)** ne demek? Bu modellerin **yaygın problemleri çözmeye yetecek kadar** yeterli olduğu anlamına gelir. Modeller, yapmaya çalıştığın spesifik görevden bağımsız olarak, **insan dilinde bulunan ortaklık (commonality)** sayesinde çalışır — yani dilin kendi yapısındaki örüntüleri öğrenirler, tek bir dar göreve özgü değil.

Bu bizi son noktaya getiriyor: **önceden eğitilmiş (pre-trained) ve ince ayarlı (fine-tuned) olma özelliği.** Bir LLM, geniş bir veri kümesiyle **genel amaçlı kullanım için önceden eğitilebilir.** Daha sonra, çok daha küçük bir veri kümesi kullanılarak **spesifik bir amaç için ince ayar yapılabilir.**

> **Önceki bölümle bağ:** Bu "pre-trained + fine-tuned" ikilisi, aslında Parça 1'deki foundation model → özelleştirilmiş model geçişinin **aynısıdır.** LLM, foundation model'in en tanıdık örneğidir; pre-training foundation model oluşturma sürecidir, fine-tuning ise onu senin ihtiyacına göre uyarlama sürecidir.

## Generative AI'ın potansiyel kullanım alanları

Generative AI, içerik üretmenin ötesinde dört geniş kategoride değer sağlar. Bunları tek tek görelim.

**1. İçerik oluşturma (content creation).** Generative AI, düşüncelerini ve vizyonlarını hayata geçirmene yardımcı olur:

- Verdiğin komutlara (prompt) dayanarak hikâyeler ya da şiirler üretebilir.
- Talimatlara göre görselleri iyileştirebilir (improve).

**2. Bilgiyi özetleme (summarize knowledge).** İçerik üretmek önemli bir fayda, ama iş burada bitmiyor:

- Video, ses ve paragrafları otomatik olarak özetleyebilir.
- İçeriğe dayanarak soru-cevap üretebilir.

**3. Arama ve keşfetme (search and discover).** Generative AI, senin adına arama ve keşif yapabilir:

- Bir belgeyi arayabilir.
- İstenen özelliklere göre ürün keşfedebilir.

**4. İş akışlarını otomatikleştirme (automate workflows).** Generative AI, iş akışlarını da otomatikleştirebilir:

- Sözleşmelerden bilgi çıkarıp etiketleyebilir.
- Geri bildirimi sınıflandırıp destek talepleri (support tickets) oluşturabilir.

> **Örüntüyü fark et:** Bu dört kategori, aslında tek bir yeteneğin (yeni içerik üretme, var olan içerikten anlam çıkarma) dört farklı uygulama alanıdır. "Yeni bir şey yarat" (içerik oluşturma), "var olanı sıkıştır" (özetleme), "var olan içinde bul" (arama/keşif) ve "bir eylemi tetikle" (iş akışı otomasyonu) — hepsi aynı temel yeteneğin farklı yönleridir.

## Generative AI ile güçlü ve etkileyici uygulamalar geliştirmek

Generative AI, sadece uygulamanın **kullanıcıya sunduğu** özellikleri değil, uygulamanın **nasıl geliştirildiğini** de değiştiriyor. Kendi generative AI destekli kod asistanınla kod yazmak, geliştirme sürecini kökten dönüştürüyor.

Bu dönüşüm altı somut özellik etrafında şekilleniyor:

**Kod üretimi (code generation).** İstediğin kodun doğal dil tanımına dayanarak kod üretebilirsin. Bir kod parçası için otomatik olarak birim testleri (unit tests) üretebilir ya da asistanından kodu optimize etmesini isteyebilirsin.

**Dokümantasyon (documentation).** Asistan, koduna yorumlar (comments) ekleyebilir ya da yaptığın değişikliklere dayanarak sürüm notları (release notes) üretebilir.

**Kod açıklama (code explanation).** Asistanından kodun **ne yaptığını** ve **nasıl yaptığını** açıklamasını isteyebilirsin — özellikle yabancı bir kod tabanına (codebase) girdiğinde paha biçilmezdir.

**Kodu düzeltme (fixing code).** AI asistanın, kodundaki hataları (bug) bulup düzeltebilir.

**Kod tamamlama (code completion).** Kodunu yazarken, kodunun **bağlamı (context)** kullanılarak yazmakta olduğun satırı tamamlayabilir. Kod editörün, tüm bir fonksiyon için kod önerebilir.

**Kod çevirisi (code translation).** Bir dilde yazılmış kodu alıp, yeni dilin kod kurallarına (coding conventions) uyarak başka bir dile çevirmesini asistanından isteyebilirsin.

Google tarafından geliştirilen modeller, kod üretimi, kod sohbeti (code chat) ve kod tamamlama konularında yardımcı olarak bu özellikleri sağlar. **Gemini**, kodunu daha hızlı ve daha verimli yazman için sana bu yardımı sunar.

> **Sezgi:** Bu altı özelliği, bir yazılım geliştiricisinin günlük iş akışının aşamaları olarak düşün: önce kod yazarsın (üretim, tamamlama), sonra belgelersin (dokümantasyon), bir hata çıkarsa düzeltirsin (fixing), bir başkasının kodunu anlaman gerekirse açıklama istersin (explanation), ve bazen bir dilden diğerine taşıman gerekir (translation). Generative AI, bu döngünün **her adımında** seni destekleyen bir yardımcı haline geliyor — kodlama sürecinin tek bir noktasında değil, baştan sona.

---

# Hangi Yaklaşımı Ne Zaman Seçerim? (Karar Bölümü)

Modülün tüm parçalarını, bir geliştiricinin karşılaşacağı somut senaryolar üzerinden toparlayalım:

| Senaryo | Doğru yaklaşım |
| --- | --- |
| Bir görseldeki nesneleri, yüzleri veya metni (OCR) tanımam gerekiyor | Vision AI |
| Kullanıcı sesini metne çevirmem ya da metni sesli okutmam gerekiyor | Speech-to-Text / Text-to-Speech |
| Uygulamamı birden fazla dile hızlıca çevirmem gerekiyor | Translation AI |
| Sosyal medyadaki ürün duygusunu ya da müşteri niyetini anlamam gerekiyor | Natural Language AI |
| Bir videoda belirli bir varlığın ne zaman göründüğünü bulmam gerekiyor | Video AI |
| Taranmış belgeleri (fatura, sözleşme) yapılandırılmış veriye çevirmem gerekiyor | Document AI |
| Kendi verimle, kod yazmadan model eğitmem gerekiyor | Agent Platform AutoML |
| Standart API'lerin kapsamadığı çok özel bir problem çözmem gerekiyor | TensorFlow / PyTorch ile özel model |
| Yeni, yaratıcı içerik (metin, görsel, kod) üretmem gerekiyor | Generative AI (LLM) |
| Kod yazarken üretim, açıklama, hata bulma, çeviri gibi yardıma ihtiyacım var | Gemini destekli kod asistanı |

---

# Toplu Özet (Hızlı Tekrar)

**Modülün iki katmanı:** Önceden eğitilmiş, dar-amaçlı ML API'leri (Vision, Speech, Translation, Natural Language, Video, Document AI) — belirli bir görevi kod yazmadan/ML bilmeden çözer. Generative AI — var olan içerikten öğrenip **yeni** içerik üreten, genel amaçlı bir paradigma.

**Neden hazır API kullanılır:** Model eğitmek devasa veri, hesaplama gücü ve uzmanlık gerektirir; Google bu modelleri zaten eğitmiş ve REST API olarak sunuyor — JSON isteği gönder, JSON yanıtı al, ML bilgisi gerekmez.

**Yedi hazır zekâ alanı:** Vision AI (etiketleme, OCR, landmark/logo/yüz/müstehcen içerik tespiti), Speech-to-Text/Text-to-Speech (110 dil), Translation AI (hızlı dinamik çeviri), Natural Language AI (varlık çıkarma, duygu analizi, niyet), Video AI (çekim/kare/video düzeyinde varlık tespiti ve zamanlama), Document AI (yapılandırılmamış → yapılandırılmış veri), AutoML/özel model (Agent Platform AutoML kodsuz, TensorFlow/PyTorch tam kontrol).

**Google'ın kendi örneği:** Toplantı odası doluluk tespiti — hareket algılama + Pub/Sub bildirimleri + zamanlama kuralı (6-8 dakika penceresi) = basit ama etkili bir ML tabanlı iş mantığı.

**Generative AI tanımı:** Var olan içerikten öğrenip yeni içerik üreten AI türü. Eğitim = training; prompt = girdi; model beklenen yanıtı tahmin eder; bu tahminden yeni içerik üretilir. Foundation model = eğitimin ürünü; LLM = en popüler foundation model türü (sadece metin verisiyle eğitilir, diğer foundation modeller görüntü/kod gibi başka veriyle eğitilebilir).

**Üç yaklaşımın evrimi:** Geleneksel programlama (kuralları sen yazarsın, tüm kuralları kapsamak imkânsız) → Makine öğrenmesi (veri+cevaptan makine kuralı öğrenir, ama dar bir göreve özgü kalır) → Generative AI (devasa çok modlu veriden genel amaçlı zekâ gelişir).

**LLM'de "büyük" ve "genel amaçlı":** Büyük = devasa eğitim verisi (petabayt ölçeği) + milyarlarca/trilyonlarca parametre (modelin öğrendiği bellek/bilgi). Genel amaçlı = insan dilindeki ortaklık sayesinde çeşitli görevlere yeter. Pre-trained (geniş veriyle genel eğitim) + fine-tuned (küçük veri kümesiyle spesifik amaca uyarlama).

**Dört generative AI kullanım kategorisi:** İçerik oluşturma (hikâye, görsel iyileştirme), bilgiyi özetleme (video/ses/metin özeti, soru-cevap üretimi), arama ve keşfetme (belge arama, ürün keşfi), iş akışı otomasyonu (sözleşme etiketleme, destek talebi oluşturma).

**Gemini ile kod geliştirme:** Kod üretimi (doğal dilden kod, birim test üretimi, optimizasyon), dokümantasyon (yorum, sürüm notu), kod açıklama, kod düzeltme (hata bulma+giderme), kod tamamlama (satır/fonksiyon önerisi), kod çevirisi (dilden dile, hedef dilin kurallarına uyarak).

---

# En Kritik Ayrımlar / Hızlı Tekrar (Cebinde Taşı)

- **Önceden eğitilmiş API vs generative AI:** Önceden eğitilmiş API'ler (Vision, Speech, Natural Language, Video, Document AI) **dar** bir soruya (bu görselde ne var? bu ses ne diyor?) belirli bir cevap verir. Generative AI **genel amaçlı**dır, var olan içerikten öğrenip **yeni** içerik üretir.
- **Vision AI'ın altı yeteneği:** Etiketleme (labeling), OCR, landmark tespiti, logo tespiti, yüz tespiti (duygu + baş giyimi), müstehcen içerik tespiti. Sınav örnekleri: düğün fotoğrafında duygu tanıma, Las Vegas Sfenks'i ile Mısır Sfenks'ini ayırt etme.
- **Speech-to-Text vs Text-to-Speech:** Biri sesi metne çevirir (transcription, sesli komut), diğeri metni sese çevirir — 110 dil desteklenir.
- **AutoML vs özel model (TensorFlow/PyTorch):** AutoML kodsuzdur, kendi verinle model eğitirsin ama ML uzmanlığı gerekmez. TensorFlow/PyTorch tam kontrol verir ama ciddi ML uzmanlığı ister.
- **Foundation model vs LLM:** Foundation model, eğitimin genel ürünüdür (metin, görsel, kod gibi çeşitli veri tipleriyle eğitilebilir). LLM, foundation model'in **en popüler** türüdür ve **sadece metin verisiyle** eğitilir.
- **Geleneksel programlama vs dar makine öğrenmesi vs generative AI:** Kuralları sen mi yazıyorsun (geleneksel), makine veri+cevaptan dar bir görevi mi öğreniyor (ML), yoksa makine devasa çok modlu veriden genel amaçlı zekâ mı geliştiriyor (generative AI)? Bu üçü birbirinin evrimidir, alternatif değil.
- **"Büyük" (large) ne demek:** Hem devasa eğitim verisi (petabayt ölçeği) hem milyarlarca/trilyonlarca parametre. Parametreler = modelin eğitim sırasında öğrendiği bellek/bilgi.
- **Pre-trained vs fine-tuned:** Pre-training, geniş veriyle genel amaçlı model oluşturur. Fine-tuning, çok daha küçük, alana özel bir veri kümesiyle bu modeli spesifik bir amaca uyarlar.
- **Dört generative AI kullanım kategorisi:** İçerik oluşturma, bilgi özetleme, arama/keşif, iş akışı otomasyonu — hepsi aynı temel yeteneğin (yeni içerik üretme / var olandan anlam çıkarma) farklı uygulamaları.
- **Gemini'nin altı kod özelliği:** Kod üretimi, dokümantasyon, kod açıklama, kod düzeltme, kod tamamlama, kod çevirisi — geliştirme döngüsünün baştan sona her adımını kapsar.

---

> **Kapanış:** Bu modül, "uygulamama nasıl zekâ eklerim" sorusuna iki katmanlı bir cevap verdi. Önce, belirli ve dar bir problemin varsa — bir görseli anlamak, bir sesi metne çevirmek, bir metindeki duyguyu tespit etmek — Google'ın önceden eğitilmiş API'lerinin bunu senin için zaten çözdüğünü, tek yapman gerekenin bir REST çağrısı olduğunu gördün. Sonra, daha genel ve açık uçlu bir ihtiyacın varsa — yeni içerik üretmek, özetlemek, keşfetmek, otomatikleştirmek — generative AI'ın ve LLM'lerin bu boşluğu nasıl doldurduğunu, ve bunun geleneksel programlama ile dar makine öğrenmesinden köklü biçimde nasıl farklı olduğunu öğrendin. Son olarak, bu gücün sadece uygulamanın **kullanıcıya sunduğu** özelliklerde değil, senin **geliştirme sürecinin kendisinde** de — Gemini destekli kod üretimi, açıklama, hata giderme ve çeviri ile — nasıl devreye girdiğini gördün. Sınav öncesi "En Kritik Ayrımlar" listesini tekrar oku; özellikle üç yaklaşımın (geleneksel programlama / dar ML / generative AI) birbirinin evrimi olduğunu ve "büyük" kelimesinin LLM bağlamında hem veri hem parametre boyutunu kapsadığını unutma. Bir konuda takılırsan ilgili parçaya dön ve "bu API/yaklaşım hangi somut sorunu çözmek için var" sorusunu yeniden kur.
