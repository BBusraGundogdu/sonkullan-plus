// SonKullan+ — Vercel sunucu fonksiyonu.
// Tarayıcıdan gelen promptu Google Gemini API'ye iletir. API anahtarı yalnızca
// Vercel'deki GEMINI_API_KEY ortam değişkeninde durur; koda ve tarayıcıya girmez.
const MODEL = process.env.GEMINI_MODEL || "gemini-2.5-flash";
const bekle = ms => new Promise(r => setTimeout(r, ms));

module.exports = async (req, res) => {
  if (req.method !== "POST") return res.status(405).json({ error: "Yalnızca POST kabul edilir." });
  const key = process.env.GEMINI_API_KEY;
  if (!key) return res.status(500).json({ error: "Sunucuda GEMINI_API_KEY tanımlı değil (Vercel > Settings > Environment Variables)." });

  const { system = "", prompt = "", image = null } = req.body || {};
  if (typeof prompt !== "string" || !prompt || prompt.length > 60000) return res.status(400).json({ error: "Geçersiz istek." });
  if (image && (typeof image.data !== "string" || image.data.length > 4000000)) return res.status(400).json({ error: "Fotoğraf çok büyük ya da geçersiz." });

  const parts = [{ text: prompt }];
  if (image) parts.push({ inline_data: { mime_type: image.mime || "image/jpeg", data: image.data } });
  const body = {
    systemInstruction: { parts: [{ text: String(system) }] },
    contents: [{ role: "user", parts }],
    generationConfig: { responseMimeType: "application/json", temperature: 0.4 }
  };

  for (let deneme = 0; deneme < 2; deneme++) {
    let r, data;
    try {
      r = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${MODEL}:generateContent`, {
        method: "POST", headers: { "Content-Type": "application/json", "x-goog-api-key": key }, body: JSON.stringify(body)
      });
      data = await r.json().catch(() => ({}));
    } catch (e) {
      return res.status(502).json({ error: "Gemini'ye ulaşılamadı: " + e.message });
    }
    if (r.status === 429 && deneme === 0) { await bekle(5000); continue; }   // ücretsiz katman sınırı: bir kez bekleyip dene
    if (!r.ok) {
      return res.status(r.status).json({ error: r.status === 429
        ? "Ücretsiz kullanım sınırına ulaşıldı. Bir dakika bekleyip tekrar deneyin."
        : (data.error && data.error.message) || ("Gemini hatası " + r.status) });
    }
    const text = ((data.candidates && data.candidates[0] && data.candidates[0].content && data.candidates[0].content.parts) || [])
      .map(p => p.text || "").join("");
    if (!text) return res.status(502).json({ error: "Model boş yanıt döndürdü" + (data.promptFeedback && data.promptFeedback.blockReason ? " (" + data.promptFeedback.blockReason + ")" : "") + "." });
    return res.status(200).json({ text, model: MODEL });
  }
};
module.exports.config = { maxDuration: 60 };
