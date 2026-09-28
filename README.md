# LinkedIn Agent Skill — Türkçe

**Türkçe** · [English](README.en.md)

Claude Code için Türkçe LinkedIn yapay zekâ asistanı: 11 skill ile gönderi
yazar, yorum ve cevap hazırlar, profilini 100 üzerinden puanlar, haftanı
planlar. Ücretsiz, MIT lisanslı; kayıt, API anahtarı ya da hesap bağlama
gerektirmez.

İçlerinden biri **humanizer**: taslaktaki uzun tireleri, kurumsal klişeleri
("sinerji", "ekosistem", "günümüzün hızla değişen dünyasında") ve görünmez
karakterleri temizler, sonra metni beş kontrollü bir panelle puanlar. Türkçe
metni doğru puanlayacak şekilde uyarlanmıştır.

**Sen "evet" demeden hiçbir şey paylaşılmaz.** Bu skill'ler yazar, sen
paylaşırsın.

## Kurulum

Claude'a şunu yapıştır:

```
https://github.com/furkanbilalakyuz/linkedin-agent-skill-tr

Bu skill paketini kur, sonra /li-post'un çalıştığını doğrula.
```

Ya da kendin kur (macOS / Linux / Windows Git Bash):

```bash
git clone https://github.com/furkanbilalakyuz/linkedin-agent-skill-tr.git
cd linkedin-agent-skill-tr
cp -r skills/li-* ~/.claude/skills/
mkdir -p ~/.claude/linkedin
cp templates/voice.md ~/.claude/linkedin/voice.md
```

Ya da plugin olarak:

```
/plugin marketplace add furkanbilalakyuz/linkedin-agent-skill-tr
/plugin install linkedin-agent-tr
```

**Gereksinimler:** [Claude Code](https://claude.com/claude-code). Python 3
sadece `/li-human` puanlaması için gerekir, ek paket istemez.

## İlk iş: voice.md

`~/.claude/linkedin/voice.md` dosyasını aç ve köşeli parantezli yerleri
doldur. Her skill bu dosyayı okur; boş bırakırsan her şey herkesinki gibi
çıkar. Kısa yol: Claude'a kendi 3-4 eski gönderini verip "bunlardan
voice.md'mi yaz" de.

**"Sınırlar" bölümünü mutlaka doldur.** İşverenine ait gizli bilgiler,
müşteri adları, NDA kapsamındaki konular gibi asla yazılmaması gerekenleri
buraya yaz. Skill'ler her taslaktan önce bu listeyi kontrol eder ve bir şey
sızmışsa yazmadan önce durup sorar.

## 11 skill

| Komut | Ne yapar | Sen ne verirsin |
|---|---|---|
| `/li-post` | Ham fikirden gönderi: [21 kalıptan](skills/li-post/hooks.json) 3 açılış seçeneği, tam taslak, humanizer'dan geçmiş son metin | Fikir, varsa yazının linki/metni |
| `/li-comment` | Başkalarının gönderilerine "Harika paylaşım!" olmayan yorumlar | Gönderinin metni |
| `/li-reply` | Kendi gönderinin altındaki yorumları önceliğe göre sıralar ve cevaplar | Yorumlar |
| `/li-profile` | Profilini [12 maddelik ölçüte](skills/li-profile/rubric.json) göre 100 üzerinden puanlar, puan kaybettiren yerleri yeniden yazar | Başlık, Hakkında, Deneyim metni |
| `/li-plan` | Haftalık plan: ne paylaşılacak, ne zaman, kimlerle etkileşime girilecek | — |
| `/li-human` | Humanizer: temizler ve puanlar (aşağıda) | Metin |
| `/li-carousel` | Kaydırmalı doküman gönderisi: slayt slayt metin ve kapak | Liste şeklinde bir fikir |
| `/li-repurpose` | Tek bir uzun içerikten (blog, video, transkript) bir haftalık gönderi | Uzun içerik |
| `/li-dm` | 200 karakterlik bağlantı notu, ilk mesaj ve iki takip mesajı | Kişi ve bağlam |
| `/li-inbox` | Gelen kutusunu ayıklar: fırsat, işe alımcı, meslektaş, spam | Mesajlar |
| `/li-audit` | Yayınladıklarının analizi: ne işe yaradı, neyi bırakmalı | Eski gönderiler ve istatistikler |

Komut yazmak zorunda değilsin, "şu yazım için bir LinkedIn gönderisi hazırla"
demen de yeterli. Örnek:

```
/li-post Medium'da SPI protokolü üzerine bir yazı yayınladım, bunu duyuran bir gönderi yaz
```

## Humanizer (`/li-human`)

İki bağımlılıksız Python scripti. Bilgisayarında, senin metninle çalışır;
hiçbir yere bir şey yüklenmez.

```bash
python3 humanize.py taslak.txt --report      # temizle, her değişikliği göster
python3 detect.py taslak.txt                  # beş kontrolle puanla
python3 detect.py once.txt sonra.txt          # farkı karşılaştır
```

**Otomatik düzeltilenler:** görünmez karakterler (sıfır genişlikli boşluk,
BOM vb.), tipografi (uzun tire, kıvrık tırnak, üç nokta karakteri) ve 160
maddelik klişe sözlüğü. Bunların 47'si Türkçe kurumsal klişe ve kalıp açılış
cümlesidir: sinerji, ekosistem, katma değer, "büyük bir gururla paylaşmak
isterim", "değerli takipçilerim"... Sözlük
[`slop.json`](skills/li-human/slop.json) içinde, düzenlemen için orada.

**Düzeltilmeyip işaretlenenler:** "sadece X değil, aynı zamanda Y" yapısı,
"X, Y ve Z" üçlüleri, "Sizce?" / "Katılıyor musunuz?" gibi refleks
etkileşim çağrıları, "Özetle / Sonuç olarak" kapanışları, tek kelimelik
retorik sorular. Cümlenin yapısını değiştirmek muhakeme ister, o yüzden
bunlar yeniden yazılmak üzere sana geri verilir.

**Beş kontrol** (0-100, yüksek puan daha insani):

| Kontrol | Neyi ölçer |
|---|---|
| BURSTINESS | Cümle uzunluğu çeşitliliği. Modeller düzgün yazar. |
| SPECIFICITY | 100 kelimedeki sayı, isim ve somut ifade yoğunluğu |
| SLOP DENSITY | 100 kelimedeki klişe sayısı |
| FINGERPRINT | Görünmez karakter, uzun tire, kıvrık tırnak yoğunluğu |
| VOICE | Birinci şahıs dili ve yapısal işaretler |

**Türkçe uyarlamaları:** kelime sayımı Türkçe harfleri (ç, ğ, ı, ö, ş, ü)
kapsar; özel isim tespiti Türkçe büyük harfleri (İ, Ş, Ğ...) tanır; VOICE
kontrolü İngilizce kısaltmalar yerine Türkçe 1. şahıs çekim eklerini
(-yorum, -dım, -dik...) sayar ve Türkçede doğal olan zamir düşürmeyi
("ben yaptım" yerine "yaptım") cezalandırmaz.

Test: gerçek, insan yazımı bir Türkçe teknik gönderi `HUMAN SCORE 76.9 PASS`
aldı. Kasıtlı olarak klişelerle yazılmış bir taslak ise `24.3 FLAGGED` aldı
ve doğru maddeleri işaretledi.

## Bilmen gerekenler

- **Bu skill'ler LinkedIn'e paylaşım yapmaz, yapmamalı da.** Kişisel profile
  otomatik paylaşım için resmî bir API yok; tarayıcı otomasyonu ya da üçüncü
  parti araçlar [LinkedIn Kullanıcı Sözleşmesi](https://www.linkedin.com/legal/user-agreement)'ni
  ihlal eder ve hesabın kısıtlanmasına yol açar. Her skill kopyalanmaya hazır
  bir metinle biter, sen yapıştırırsın.
- **Beş kontrol yerel sezgisel ölçümlerdir, dedektör API'si değildir.**
  GPTZero, Originality ya da Turnitin'e bağlanmaz, onların sonucunu garanti
  etmez. "Tespit edilemez" vaadi veren herkes sana bir şey satıyordur.
- **Hiçbir şey uydurulmaz.** Senin adına sahte rakam, müşteri ya da sonuç
  yazılmaz. Taslak senin vermediğin bir rakama ihtiyaç duyarsa
  `{{senin sayın}}` boşluğuyla ve bir uyarıyla gelir.

## Dosyalar

```
skills/li-post/hooks.json        21 açılış kalıbı: şablon, örnek, ne işe yarar, nasıl bozulur
skills/li-human/slop.json        klişe sözlüğü (160 madde, 47'si Türkçe) ve yapısal işaretler
skills/li-human/humanize.py      temizleme
skills/li-human/detect.py        beş kontrollü puanlama
skills/li-profile/rubric.json    100 puanlık profil ölçütü
templates/voice.md               ses profilin — önce bunu doldur
```

## Lisans

MIT — bkz. [LICENSE](LICENSE). Jake Schincariol'un
[linkedin-agent-skill](https://github.com/Jakeschincariol/linkedin-agent-skill)
paketinin Türkçe uyarlamasıdır.
