# SonKullan+

Evdeki gıdalardan hangisinin önce bozulacağını belirleyen ve o ürünleri kurtaran tarifler öneren üretken yapay zekâ destekli mutfak asistanı.
Samsung Innovation Campus — SDG'ler için Üretken Yapay Zekâ Hackathonu. **SDG 12.3 (gıda israfı).**

**Canlı uygulama:** https://BURAYA-VERCEL-ADRESİ.vercel.app (giriş gerekmez)
**Demo videosu:** BURAYA-VİDEO-BAĞLANTISI

## Nasıl çalışır?
1. Ürünler market fişi fotoğrafı, ambalaj fotoğrafı, konuşma (telefon diktesi) veya elle eklenir. Okunan bilgiler kullanıcı onayından geçer; düşük güvenli satırlar doğrulanmadan eklenemez.
2. Kod, her ürünün kalan süresini hesaplar ve renkle gösterir. Etiket tarihi ile referans tablodan hesaplanan tahmini tarih ayrı gösterilir.
3. "Elimdekileri Kurtar" ile üç farklı yöntemle tarif üretilir. Tarifler önce kod kontrolünden, sonra ayrı bir yapay zekâ denetçisinden geçer; sorunlu tarif bir kez düzeltilerek yeniden yazılır.

Gıda güvenliği kuralları (STT'si geçmiş ürünü dışlama, alerjen filtresi, onaysız tarih kullanmama) modelde değil, kodda uygulanır.

## Teknik yapı
- `index.html` — arayüz, kurallar ve tüm promptlar (`SYS_CIKARIM`, `FIS_PROMPT`, `AMBALAJ_PROMPT`, `SES_PROMPT`, `SYS_TARIF`, `tarifPrompt`, `SYS_DENETCI`, `denetciPrompt`).
- `api/ai.js` — Vercel sunucu fonksiyonu; promptu Google Gemini API'ye iletir. API anahtarı yalnızca sunucuda durur.
- Model: Google Gemini (ücretsiz katman). Model adı `GEMINI_MODEL` ortam değişkeniyle değiştirilebilir.
- Uygulamadaki **Promptlar** sekmesi her çağrının prompt metnini, tekniğini ve gerçek çıktısını kaydeder ve JSON olarak dışa aktarır.

## Kendi kopyanızı yayınlama
1. https://aistudio.google.com adresinden ücretsiz API anahtarı alın (faturalandırmayı açmayın).
2. Bu repoyu Vercel'e aktarın (vercel.com > Add New > Project).
3. Environment Variables bölümüne `GEMINI_API_KEY` ekleyin, isterseniz `GEMINI_MODEL` de ekleyin.
4. Deploy.

## Sınırlar ve etik notlar
- Referans raf ömrü tablosundaki değerler doğrulanmış kaynakla güncellenmelidir (`index.html` içindeki `KAT` nesnesi).
- Ücretsiz katmanda Google, istekleri ürünlerini geliştirmek için kullanabilir. Bu nedenle kullanıcı, fişteki ad ve kart bilgisini kapatarak fotoğraf çekmesi konusunda uyarılır.
- Veriler kullanıcının kendi tarayıcısında tutulur; hesap ve veritabanı yoktur.
- SonKullan+ profesyonel gıda güvenliği değerlendirmesinin yerini tutmaz.
