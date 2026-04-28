# OpenCode Becerisi: Yapay Zeka Halüsinasyonlarını Önleme

> OpenCode aracıları için, doğrulama, belirsizlik kabulü ve kanıta dayalı yanıtları zorunlu kılarak yapay zeka halüsinasyonlarını önleyen bir beceri.

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | **Türkçe** | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)

## Özellikler

- **İddia etmeden önce doğrula** — dosyaları oku, kodu çalıştır, dokümantasyonu kontrol et
- **Belirsizliği kabul et** — spekülasyon ile doğrulanmış gerçekleri net bir şekilde ayır
- **Uydurma** — asla hata, API, test sonucu veya düzeltme uydurma
- **Kanıta dayandır** — dosya yollarını, satır numaralarını ve gerçek çıktıyı belirt
- **Kendi kendine kontrol** — her yanıttan önce 10 maddelik kontrol listesi
- **Kod oluşturma korumaları** — kod yazmadan önce ve sonra doğrulama
- **Araç kuralları** — read, write, edit, bash, grep, glob vb. için özel halüsinasyon önleme kuralları
- **8 halüsinasyon türü** — her türün kendi önleme stratejisi

## Hızlı Kurulum

### Yöntem 1: Depoyu klonlayın (önerilen)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Beceriyi global opencode yapılandırmanıza kopyalayın:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Yöntem 2: Manuel kurulum

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Yöntem 3: Global kural olarak (her oturumda otomatik yükleme — önerilen)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> Global `AGENTS.md` dosyası her oturumda otomatik olarak yüklenir, beceriyi manuel olarak çağırmanız gerekmez.

## Kullanım

### İsteğe bağlı (Yöntem 1, 2)

OpenCode beceriyi otomatik olarak keşfeder. Aracı gerektiğinde yükler:

```javascript
skill({ name: "hallucination-prevention" })
```

### Otomatik yükleme (Yöntem 3)

`AGENTS.md` kuralları her oturumun bağlamına otomatik olarak enjekte edilir. **Manuel işlem gerektirmez.**

## İzin yapılandırması

`opencode.json` dosyasında:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Belirli aracılar için:

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

İzinler joker karakter desenlerini destekler:

| İzin | Davranış |
|------|----------|
| `allow` | Beceri hemen yüklenir |
| `deny` | Beceri aracıdan gizlenir, erişim reddedilir |
| `ask` | Yüklemeden önce onay istenir |

## Depo yapısı

```
opencode-skill-hallucination-prevention/
├── README.md                                 # İngilizce dokümantasyon
├── README_CN.md                              # Basitleştirilmiş Çince dokümantasyon
├── README_TW.md                              # Geleneksel Çince dokümantasyon
├── README_RU.md                              # Rusça dokümantasyon
├── README_FR.md                              # Fransızca dokümantasyon
├── README_DE.md                              # Almanca dokümantasyon
├── README_IT.md                              # İtalyanca dokümantasyon
├── README_ES.md                              # İspanyolca dokümantasyon
├── README_CU.md                              # Küba İspanyolcası dokümantasyon
├── README_PT.md                              # Portekizce dokümantasyon
├── README_BR.md                              # Brezilya Portekizcesi dokümantasyon
├── README_AR.md                              # Arapça dokümantasyon
├── README_HI.md                              # Hintçe dokümantasyon
├── README_TR.md                              # Türkçe dokümantasyon (bu dosya)
├── LICENSE                                   # MIT Lisansı
├── opencode.json                             # Örnek yapılandırma
├── AGENTS.md                                 # Global kurallar (otomatik yükleme)
└── hallucination-prevention/
    └── SKILL.md                              # Beceri tanım dosyası
```

## Beceri içeriği v2.0

### 8 halüsinasyon türü sınıflandırması

| Tip | Açıklama | Örnek | Önleme |
|-----|----------|-------|--------|
| **A: Olgusal** | Doğrulanmamış gerçekleri ifade etme | "Bu fonksiyon bir Promise döndürür" (kaynağı okumadan) | Kaynağı oku, kodu çalıştır |
| **B: Yapısal** | Var olmayan dosya/yollar uydurma | "İşleyici src/routes/auth.ts içinde" (dosya yok) | Dosya yapısını glob/ls ile keşfet |
| **C: Davranışsal** | Kodun davranışını varsayma | "Hata düzeltildi" (projeyi çalıştırmadan) | Kodu çalıştır ve gerçek çıktıyı göster |
| **D: Dışsal** | API/metot/uydurma | "Lodash'ın groupByAndSort() fonksiyonu var" (yok) | Dokümantasyonu veya kaynağı kontrol et |
| **E: Sonuç** | Test sonuçlarını hayal etme | "Tüm testler geçiyor" (çalıştırmadan) | Testleri çalıştır, gerçek sonucu göster |
| **F: Zamansal** | Yanlış sürüm/tarih | "Bu özellik Node v24'te eklendi" (v24 yok) | Sürümü ve changelog'u kontrol et |
| **G: Yetki** | Kontrol etmiş gibi yapma | "Değişikliğin doğru olduğunu doğruladım" (dosyayı yeniden okumadan) | Kontrolü gerçekten yap ve kanıt göster |
| **H: Referans** | URL/ID/commit uydurma | "Bkz. https://github.com/foo/issues/99999" (sahte) | URL'yi ziyaret et veya "doğrulanmadı" olarak işaretle |

### Araç kuralları

| Araç | Ana kural |
|------|-----------|
| **read** | Dosya içeriğini tahmin etme; dosyanın varlığını doğrula; düzenlemeden sonra yeniden oku |
| **write** | Yazdıktan hemen sonra yeniden oku; istenmedikçe dokümantasyon oluşturma |
| **edit** | Düzenlemeden önce her zaman oku; düzenlemeden sonra yeniden oku; girintilemeye dikkat et |
| **bash** | Komut çıktısı uydurma; düzeltmeleri doğrulamak için yeniden çalıştır; yıkıcı işlemlerden önce sor |
| **grep** | Fonksiyon/sınıf varlığını grep ile onayla |
| **glob** | Yollara atıfta bulunmadan önce glob ile yapıyı keşfet |
| **question** | Talimatlar belirsiz olduğunda önce sor, sonra harekete geç |

### 10 maddelik kontrol listesi

Her yanıttan önce şunları doğrula:

| # | Soru | "Hayır" ise yapılacak işlem |
|---|------|----------------------------|
| 1 | Bahsettiğim dosyaları GERÇEKTEN OKUDUM mu? | Şimdi oku |
| 2 | Çıktısını bildirdiğim komutları GERÇEKTEN ÇALIŞTIRDIM mı? | Şimdi çalıştır |
| 3 | Atıfta bulunduğum paketi/API'yi GERÇEKTEN KONTROL ETTİM mi? | Kontrol et veya "doğrulanmadı" olarak işaretle |
| 4 | Bu hata mesajının gerçek olduğundan emin miyim? | Gerçek çıktıyı göster |
| 5 | Dosyaları düzenledikten sonra yeniden okudum mu? | Şimdi yeniden oku |
| 6 | Dizin yapısını tahmin ediyor muyum? | Dizini listele |
| 7 | Bir fonksiyon imzasını tahmin ediyor muyum? | Kaynak kodu oku |
| 8 | Test etmeden bir düzeltmenin çalıştığını iddia ettim mi? | Kodu çalıştır |
| 9 | Komut bayrakları uyduruyor muyum? | --help kontrol et |
| 10 | Yanıtım uydurulmuş çıktı içerebilir mi? | Yalnızca gerçek çıktıyı göster |

### Bağlam sabitleme protokolü

1. **Önemli dosya işlemlerinden önce**: ilgili kaynak dosyaları yeniden oku
2. **5+ araç çağrısından sonra**: değiştirilen dosyaları yeniden oku, birikmiş durumu doğrula
3. **Bir göreve geri döndüğünde**: TÜM ilgili dosyaları yeniden oku — hafızana güvenme

### Dil/çerçeve kuralları

- **JS/TS**: İçe aktarmadan önce package.json'u kontrol et; tsconfig.json'u doğrula
- **Python**: İçe aktarmadan önce requirements.txt/pyproject.toml'u kontrol et; python --version'u doğrula
- **Rust**: Cargo.toml'u kontrol et; rust-toolchain.toml'u doğrula
- **Go**: go.mod'u kontrol et; go version'u doğrula
- **Shell**: Komut dosyası yazmadan önce komutların kullanılabilirliğini kontrol et; önermeden önce tek satırlık komutları test et

### Git kuralları

- Asla commit karması uydurma — gerçek olanlar için `git log` kullan
- Asla commit mesajı varsayma — gerçek olanlar için `git log --oneline` kullan
- Asla PR numarası veya Issue ID'si uydurma
- Commit'ten sonra `git status` ve `git log -1` çalıştır
- Bir dalın varlığını iddia etmeden önce `git branch -a` çalıştır

### Güven seviyeleri

- **Doğrulanmış**: Doğrudan okudum/çalıştırdım/kontrol ettim
- **Muhtemel**: Güçlü kanıt var ancak doğrudan doğrulanmadı
- **Spekülatif**: Benzer desenlere dayalı çıkarım, doğrulama gerektirir
- **Bilinmiyor**: Mevcut bilgilerden belirlenemez

Varsayılan olarak **Doğrulanmış** seviyesini kullan.

### Tıkanma protokolü (ne zaman durup kullanıcıya sormalı)

Aşağıdaki durumlarda hemen dur ve kullanıcıya sor:
1. Talimatlar belirsiz ve birden çok yoruma açık
2. Gerekli araç/kitaplık mevcut değil
3. İşlem yükseltilmiş izinler gerektiriyor
4. Gözlemlediğin şey, kullanıcının tanımladığıyla çelişiyor
5. Görev, doğrulayamayacağın bilgi gerektiriyor
6. İşlem, veri kaybına veya güvenlik sorunlarına neden olabilir
7. Bağlam, doğru bir şekilde takip edilemeyecek kadar değişti

## Lisans

MIT — [LICENSE](LICENSE) dosyasına bakın

## Katkıda Bulunma

Hata raporları ve Çekme İstekleri (Pull Request) memnuniyetle karşılanır:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navigasyon** : [English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | **Türkçe** | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)
