# 📄 ATS Puan Ölçer

CV'nizi (Word veya PDF) iş ilanıyla karşılaştırıp **ATS (Aday Takip Sistemi) uyum puanı** veren, eksik anahtar kelimeleri gösteren ve **STAR** yöntemine göre madde önerileri sunan, tek dosyalık bir web uygulaması.

- **Kurulum yok:** Tek bir `.html` dosyası, tarayıcıda açmanız yeterli.
- **İki katmanlı analiz:**
  1. **Hızlı tarama:** Tarayıcının içinde, kurallara dayalı puanlama. Her zaman çalışır.
  2. **Claude değerlendirmesi:** Yapay zeka ile ayrıntılı analiz. Yalnızca Claude artifact ortamında çalışır.
- **Türkçe odaklı:** Türkçe karakterler, ekler (bankacılık/bankacılığı) ve Türk telefon biçimi tanınır.

---

## 📌 Program türü

| Özellik | Açıklama |
|---|---|
| Tür | Tarayıcıda çalışan, tek sayfalık web uygulaması (Single-file HTML App) |
| Dil | HTML + CSS + saf JavaScript (framework yok) |
| Word okuma | [mammoth.js](https://github.com/mwilliamson/mammoth.js) 1.6.0 |
| PDF okuma | [PDF.js](https://mozilla.github.io/pdf.js/) 3.11.174 (Mozilla) |
| Yapay zeka | Claude (artifact ortamındaki `window.claude` arayüzü üzerinden) |
| Sunucu | Gerekmez (backend yok) |

---

## 🚀 Kullanım

1. `ATS_Puan_Olcer.html` dosyasını tarayıcıda açın.
2. **CV dosyası:** `.docx` veya `.pdf` dosyanızı sürükleyip bırakın ya da tıklayıp seçin.
3. **İş ilanı:** Başvuracağınız ilanın metnini yapıştırın (önerilir). Boş bırakırsanız seçtiğiniz sektörün tipik anahtar kelimeleri kullanılır:
   - Bankacılık / Katılım bankacılığı
   - Finans / Bütçe / Raporlama
   - Genel
4. **CV'yi tara**'ya basın.
5. Sonuçları inceleyin, **Raporu kopyala** ile metin olarak alın.

---

## ⚙️ Hızlı tarama: puanlama mantığı (100 puan)

| Kategori | Puan | Ne ölçülür |
|---|---|---|
| Anahtar kelime uyumu | 40 | İlandan çıkarılan kelimelerin CV'de geçme oranı (%75 ve üzeri tam puan) |
| Standart bölüm başlıkları | 15 | Özet, Deneyim, Eğitim, Yetkinlik, Sertifika, Dil |
| İletişim bilgileri | 10 | E-posta (4), telefon (4), LinkedIn (2) |
| Ölçülebilir başarılar | 10 | Yüzde, tutar (TL, milyon), kişi sayısı içeren satırlar |
| STAR uyumu | 10 | Durum/Görev + Eylem + Rakamlı Sonuç içeren maddelerin oranı |
| Biçim ve uzunluk | 10 | Kelime sayısı, yer tutucular `[...]`, tarih varlığı, özel simgeler |
| Eylem fiilleri | 5 | yönetti, artırdı, geliştirdi, kurdu vb. |

**Sonuç:** 80+ Güçlü · 60–79 Orta · 60 altı Zayıf

### Teknik ayrıntılar
- **Türkçe normalleştirme (`fold`):** ç→c, ğ→g, ı→i, ö→o, ş→s, ü→u. Böylece yazım farkları eşleşmeyi bozmaz.
- **Kök bulma (`stem`):** Kelimenin ilk ~%70'i alınır. Bu sayede "bankacılık" ile "bankacılığı" eşleşir.
- **İlandan anahtar kelime çıkarma:** Durdurma kelimeleri (ve, için, deneyim vb.) atılır, en sık geçen 28 kelime ve 2 kez geçen ikili ifadeler (bigram) seçilir. En fazla 32 kelime alınır.
- **STAR tespiti:**
    - *Durum/Görev:* "için, amacıyla, kapsamında, sürecinde…" gibi ifadeler
    - *Eylem:* Geçmiş zaman fiil ekleri (-dı, -di, -du, -tı…)
    - *Sonuç:* Yıl dışındaki rakamlar
- **Dosya kontrolleri:**
    - Word: tablo ve görsel sayısı (ATS bunları iyi okuyamaz)
    - PDF: sayfa sayısı; metin okunamıyorsa "taranmış görsel olabilir" uyarısı

---

## 🤖 Claude değerlendirmesi

Claude artifact ortamında açıldığında CV ve ilan Claude'a gönderilir ve şu sonuçlar alınır (JSON biçiminde):

- **Eşleşme oranı** (%) ve kısa özet
- **Hatalar:** Kritik / orta / düşük önem; yazım, tarih tutarsızlığı, yer tutucu vb.
- **Tavsiyeler:** 5–8 somut öneri
- **Aranabilirlik kontrolleri:** İletişim, özet, unvan uyumu, başlıklar, tarih biçimi
- **Teknik ve kişisel beceri tabloları:** CV ve ilandaki geçme sayıları
- **İşe alımcı ipuçları**
- **STAR yeniden yazımları:** En zayıf 6 madde, yüzdeli sonuçlarla. Gerçek rakam yoksa uydurulmaz, `%X` yazılır.

> ⚠️ Dosya GitHub Pages veya bilgisayardan doğrudan açıldığında `window.claude` bulunmaz. Bu durumda Claude bölümü gizlenir ve yalnızca **hızlı tarama** çalışır.

---

## 💻 Gereksinimler

- Güncel bir tarayıcı (Chrome, Edge, Firefox, Safari)
- İnternet bağlantısı (kütüphaneler CDN'den yüklenir)
- Eski `.doc` dosyaları desteklenmez. Word'de **Farklı kaydet → .docx** yapın.

### Dış bağlantılar
| Adres | Ne için |
|---|---|
| `cdnjs.cloudflare.com` | mammoth.js ve PDF.js |
| `fonts.googleapis.com` | Inter yazı tipi |

---

## ⚠️ Bilinen sınırlamalar

- Hızlı tarama bir **tahmindir**. Gerçek ATS sistemleri (Workday, SAP SuccessFactors, Kariyer.net vb.) farklı kurallarla puanlar.
- Word dosyalarında üstbilgi/altbilgi ve metin kutularındaki yazılar okunmayabilir.
- Taranmış (görsel) PDF'lerden metin çıkarılamaz (OCR yok).
- Kök bulma basit bir kesme yöntemidir; nadiren yanlış eşleşme yapabilir.

---

## 🔒 Gizlilik

- **Hızlı tarama:** CV bilgisayarınızdan çıkmaz, her şey tarayıcıda işlenir.
- **Claude değerlendirmesi:** CV metni ve ilan analiz için Claude'a gönderilir.

---

## 📁 Dosya yapısı

```
ATS_Puan_Olcer.html   ← Uygulamanın tamamı (arayüz + stil + kod)
README.md             ← Bu dosya
```

> GitHub Pages ile yayınlamak isterseniz dosyayı `index.html` olarak yeniden adlandırmanız yeterli.

---

## 🛠️ Yapımcı

**Ahmet Mücahid Göktaş** — Bütçe ve Raporlama Uzmanı
Yapay zeka destekli raporlama ve otomasyon çalışmaları kapsamında geliştirilmiştir.
