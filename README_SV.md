# OpenCode Färdighet: Förebygga AI-Hallucinationer

> En OpenCode-agentfärdighet som förhindrar AI-hallucinationer genom att upprätthålla verifiering, erkännande av osäkerhet och evidensbaserade svar.

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | **Svenska** | [Ελληνικά](./README_EL.md)

## Funktioner

- **Verifiera innan du påstår** — läs filer, kör kod, kontrollera dokumentation innan du gör påståenden
- **Erkänn osäkerhet** — märk tydligt spekulation vs. verifierade fakta
- **Hitta inte på** — fabricera aldrig fel, API:er, testresultat eller fixar
- **Förankra i bevis** — citera filsökvägar, radnummer och faktisk utdata
- **Självkontrollflöde** — 5-stegs verifieringschecklista före varje svar
- **Kodgenereringsskydd** — före/efter skriv-verifiering
- **Felhanteringsprotokoll** — läs verkliga fel, visa ordagrant, verifiera fixar

## Installation

### Alternativ 1: Klona detta repo

```bash
git clone https://github.com/<your-org>/opencode-skill-hallucination-prevention.git
```

Kopiera sedan färdigheten till din globala opencode-konfiguration:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Alternativ 2: Manuell installation

Skapa färdighetskatalogen och kopiera `SKILL.md`:

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
```

Placera sedan [`hallucination-prevention/SKILL.md`](hallucination-prevention/SKILL.md) i den katalogen.

### Alternativ 3: Som en global regel (auto-ladda varje session)

Kopiera de utökade reglerna till din globala AGENTS.md:

```bash
# macOS / Linux
cat hallucination-prevention/SKILL.md >> ~/.config/opencode/AGENTS.md

# Windows PowerShell
Add-Content -Path "$env:USERPROFILE\.config\opencode\AGENTS.md" -Value (Get-Content "hallucination-prevention\SKILL.md")
```

Eller använd den fristående [`AGENTS.md`](AGENTS.md) som medföljer detta repo.

## Användning

Färdigheten upptäcks automatiskt av OpenCode. Agenten kommer att se den listad bland tillgängliga färdigheter och ladda den när det är relevant.

För att ladda den explicit under en session:

```
Agenten kan använda: skill({ name: "hallucination-prevention" })
```

### Behörighetskonfiguration

I din `opencode.json` kan du styra åtkomsten:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

För överskrivning per agent:

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

## Struktur

```
opencode-skill-hallucination-prevention/
├── README.md
├── README_SV.md
├── LICENSE
├── opencode.json              # Exempel på global konfiguration
├── AGENTS.md                  # Fristående globala regler (auto-laddningsversion)
└── hallucination-prevention/
    └── SKILL.md               # Färdighetsdefinitionen
```

## Färdighetsinnehåll

| Modul | Beskrivning |
|--------|-------------|
| **Kärnprinciper** | 5 principer: verifiera, erkänn osäkerhet, hitta inte på, bevis, validera |
| **Antimönster** | Vanliga hallucinationsmönster med ❌/✅-exempel |
| **Kodgenereringsskydd** | Checklistor för verifiering före och efter skrivning |
| **Faktakontrollprotokoll** | Tabell över vad som ska verifieras och hur |
| **Felhanteringsprotokoll** | 8-stegs ärligt felhanteringsarbetsflöde |
| **Konfidensnivåmärkning** | 4-nivåers konfidenssystem (Verifierad → Okänd) |
| **Självkontrollflöde** | 10-punkts checklista före svar |
| **Nödvändiga beteenden** | Läs först, kör för att bekräfta, kontrollera existens, visa diffar |

## Taxonomi för hallucinationstyper

### Typ A — Faktamässig hallucination
Att påstå ett faktum utan att verifiera det.
**Exempel**: "Denna funktion returnerar en Promise" (utan att läsa källkoden)
**Förebyggande**: Läs källkod, kör kod, kontrollera dokumentation först.

### Typ B — Strukturell hallucination
Att hitta på filer, sökvägar, kataloger eller kodstrukturer som inte finns.
**Exempel**: "Hanteraren finns i `src/routes/auth.ts`" (filen finns inte)
**Förebyggande**: Använd glob/ls för att upptäcka faktisk struktur innan du refererar till filer.

### Typ C — Beteendemässig hallucination
Att anta att kod beter sig på ett visst sätt utan att köra den.
**Exempel**: "Buggen är nu fixad" (utan att köra projektet)
**Förebyggande**: Kör koden och fånga verklig utdata.

### Typ D — Extern kunskapshallucination
Att hitta på API-metoder, biblioteksfunktioner, konfigurationsalternativ, versionsnummer.
**Exempel**: "Lodash har en `groupByAndSort()`-funktion" (det har den inte)
**Förebyggande**: Kontrollera dokumentation, paketkälla eller typedefinitioner.

### Typ E — Resultathallucination
Att föreställa sig testresultat, kommandoutdata, felmeddelanden.
**Exempel**: "Alla test går igenom" (utan att köra dem)
**Förebyggande**: Kör det faktiska kommandot och visa verklig utdata.

### Typ F — Temporal hallucination
Att påstå föråldrade eller framtida versioner, datum eller funktioner.
**Exempel**: "Denna funktion lades till i Node v24" (v24 finns inte än)
**Förebyggande**: Kontrollera aktuell version, changelog eller release notes.

### Typ G — Auktoritetshallucination
Att låtsas ha utfört en kontroll som inte gjordes.
**Exempel**: "Jag har verifierat att ändringen är korrekt" (utan att läsa om filen)
**Förebyggande**: Utför faktiskt kontrollen och visa bevis.

### Typ H — Referenshallucination
Att hitta på URL:er, commit-hashar, PR-nummer, ärende-ID:n.
**Exempel**: "Se https://github.com/foo/bar/issues/99999" (påhittat ärende)
**Förebyggande**: Referera endast till URL:er/ID:n du faktiskt har besökt eller verifierat.

## Verktygsspecifika anti-hallucinationsregler

### read-verktyget
- Gissa ALDRIG vad en fil innehåller baserat enbart på dess namn
- Använd alltid read innan du gör påståenden om filinnehåll
- Efter en redigering, läs om filen för att bekräfta att ändringarna tillämpades
- Anta ALDRIG att en fil finns — verifiera med glob/ls först

### write-verktyget
- Skapa ALDRIG filer på sökvägar du inte har verifierat finns (kontrollera med ls först)
- Läs alltid om den skrivna filen för att bekräfta att innehållet matchar avsikten
- Skriv aldrig dokumentation eller README-filer om det inte uttryckligen efterfrågas

### edit-verktyget
- Läs ALLTID filen innan du redigerar den (inga undantag)
- Matcha den exakta indragsstilen (tabbar vs mellanslag) från omgivande kod
- Efter redigering, läs ALLTID om filen för att bekräfta ändringen
- Om redigering misslyckas med "oldString not found", läs om för att hitta den faktiska texten

### bash-verktyget
- Hitta ALDRIG på kommandoutdata — kör alltid och fånga verklig utdata
- Innan du påstår att en fix fungerar, kör relevant kommando igen
- Kör aldrig destruktiva kommandon (rm -rf, force push, etc.) utan att fråga
- Verifiera att verktyg finns (node, python, git) innan du kör dem

### grep-verktyget
- Använd grep för att bekräfta funktions-/klass-/variabelexistens innan du refererar till dem
- Använd grep för att hitta alla förekomster innan du byter namn
- Anta aldrig att ett mönster inte matchar utan att köra grep

### glob-verktyget
- Använd glob för att upptäcka faktisk filstruktur innan du refererar till sökvägar
- Använd före skapande av nya filer för att undvika dubbletter
- Använd efter ändringar för att verifiera att filer skapades/flyttades korrekt

### question-verktyget
- När användarens begäran är tvetydig, ställ förtydligande frågor INNAN du agerar
- Gissa inte användarens avsikt — fråga
- Erbjud konkreta alternativ istället för öppna frågor

### task-verktyget
- Tala tydligt om för delagenter om de ska skriva kod eller bara göra research
- Specificera exakt vilken information delagenten ska returnera
- Tillhandahåll verifieringsinstruktioner för delagenter

## Kontextförankringsprotokoll

### Före varje större åtgärd (skapa, redigera, ta bort filer)
1. Läs om relevanta källfiler för att förankra dig i nuvarande verklighet
2. Lista målkatalogen för att bekräfta strukturen
3. Läs eventuellt grannfiler för att förstå konventioner

### När uppgiften sträcker sig över flera verktygsanrop
1. Efter 5+ verktygsanrop, läs om modifierade filer för att bekräfta ackumulerat tillstånd
2. Innan du gör nästa ändring, verifiera att föregående ändring reflekteras
3. Förlita dig inte på minnet av vad du gjorde tidigare — läs om

### När du återvänder till en uppgift efter annat arbete
1. Läs om ALLA filer som är relevanta för den aktuella uppgiften
2. Anta INTE att du kommer ihåg deras innehåll korrekt

## Självgranskningschecklista före svar

Innan du levererar något svar som påstår ett resultat, verifiera:

| # | Kontroll | Åtgärd om "Nej" |
|---|----------|-----------------|
| 1 | Har jag faktiskt LÄST filerna jag diskuterar? | Läs dem nu |
| 2 | Har jag faktiskt KÖRT kommandona jag rapporterar utdata för? | Kör dem nu |
| 3 | Har jag faktiskt KONTROLLERAT paketet/API:et jag refererar till? | Kontrollera nu eller säg "Jag har inte verifierat" |
| 4 | Är jag 100% säker på att detta felmeddelande är verkligt? | Visa faktisk utdata eller säg "Jag behöver återskapa" |
| 5 | Läste jag om filen efter att ha redigerat den? | Läs om nu |
| 6 | Gör jag ett antagande om katalogstruktur? | Lista katalogen |
| 7 | Gissar jag en funktionssignatur? | Läs källkoden |
| 8 | Påstod jag att en fix fungerar utan testning? | Kör koden |
| 9 | Hittar jag på en kommandoväxel/flagga? | Kontrollera `--help` eller manualsidan |
| 10 | Kan mitt svar innehålla påhittad utdata? | Om ja, visa endast verklig utdata |

## Språk-/ramverksspecifik anti-hallucination

### JavaScript/TypeScript
- Kontrollera `package.json` för faktiska beroenden innan du importerar
- Verifiera TypeScript-konfiguration (`tsconfig.json`) innan du påstår strict mode-inställningar
- Kontrollera `.eslintrc` eller `eslint.config` innan du påstår lint-regler
- Anta aldrig att ett bibliotek finns för att det liknar ett annat projekt

### Python
- Kontrollera `requirements.txt` / `pyproject.toml` innan du importerar paket
- Verifiera att virtuell miljö är aktiv innan du kör Python
- Kontrollera `ruff.toml` / `.flake8` / `pyproject.toml` för lint-regler
- Anta aldrig Python-version utan att kontrollera `python --version`

### Rust
- Kontrollera `Cargo.toml` för beroenden och funktioner
- Verifiera `rust-toolchain.toml` för verktygskedjeversion
- Anta aldrig att Cargo-dellkommandon finns utan verifiering

### Go
- Kontrollera `go.mod` för modulnamn och beroenden
- Verifiera `go version` innan du använder versionsspecifika funktioner
- Anta aldrig att paketsökvägar följer en specifik konvention

### Shell/Bash
- Verifiera skal-miljön (bash vs zsh vs powershell)
- Kontrollera tillgängliga kommandon innan du skriptar
- Testa alltid one-liners innan du föreslår dem

## Git-specifika anti-hallucinationsregler

### Commits
- Fabricera ALDRIG commit-hashar — kör `git log` för att se verkliga hashar
- Anta ALDRIG commit-meddelanden — kör `git log --oneline` för att se dem
- Anta ALDRIG vilka filer som ändrades i en commit — kör `git show` eller `git diff`
- Efter en commit, kör `git status` och `git log -1` för att verifiera

### Branches
- Kör `git branch -a` innan du påstår vilka branches som finns
- Kör `git status` innan du påstår nuvarande branch-status
- Anta ALDRIG fjärrspårningsstatus utan att kontrollera

### PRs / Ärenden
- Hitta ALDRIG på PR-nummer eller ärende-ID:n
- Anta ALDRIG PR-status utan att kontrollera GitHub/API
- Fabricera ALDRIG PR-beskrivningar eller granskningkommentarer

## URL- och referenshallucinationsförebyggande

- Generera ALDRIG en URL du inte har besökt eller verifierat
- Anta ALDRIG att en GitHub-URL är giltig utan att kontrollera
- För dokumentationsreferenser, föredra faktiska filsökvägar i repot
- Om en URL behövs, antingen:
  a) Besök den först för att bekräfta att den finns
  b) Be användaren att tillhandahålla korrekt URL
  c) Uppge "Jag har inte verifierat att denna URL finns"

## Eskaleringsprotokoll

Stanna omedelbart och fråga användaren när:

1. **Tvetydiga instruktioner**: Begäran är öppen för flera tolkningar
2. **Saknade beroenden**: Verktyg/bibliotek krävs men är inte installerat
3. **Behörighetsproblem**: Operation kräver förhöjda privilegier
4. **Motsägelsefullt tillstånd**: Vad du observerar motsäger vad användaren beskrev
5. **Kunskapsgräns**: Uppgiften kräver kunskap du inte kan verifiera
6. **Säkerhetsrisk**: Operation kan orsaka dataförlust eller säkerhetsproblem
7. **Kontextutmattning**: För mycket har hänt för att spåra korrekt
8. **Externt beroende**: Kräver åtkomst till extern tjänst eller inloggningsuppgifter

Eskalera INTE för triviala bekräftelser. Använd självgranskningschecklistan först.

## Kärnprinciper

### 1. Verifiera innan du påstår
- Läs alltid faktiskt filinnehåll innan du gör påståenden om kod
- Kör kod för att verifiera att den fungerar innan du rapporterar framgång
- Använd grep/sökning för att bekräfta existens av funktioner, klasser och variabler
- Kontrollera dokumentation innan du hävdar API-beteende
- Anta aldrig att filsökvägar, funktionssignaturer eller importsökvägar finns

### 2. Erkänn osäkerhet
- Säg "Jag vet inte" eller "Jag är inte säker" när information inte är tillgänglig
- Märk tydligt spekulativa uttalanden med fraser som "troligen", "förmodligen", "det verkar"
- Skilj mellan vad du observerade och vad du drog slutsatser om
- Om du inte kan verifiera ett påstående, säg det uttryckligen

### 3. Hitta inte på
- Fabricera aldrig felmeddelanden, loggutdata eller kommandoresultat
- Hitta aldrig på API-endpoints, biblioteksfunktioner eller konfigurationsalternativ
- Anta aldrig innehållet i filer du inte har läst
- Hallucinera aldrig testutdata — kör test för att se verkliga resultat
- Påstå aldrig att en fix fungerar utan att utföra den

### 4. Förankra i bevis
- Citera specifika filsökvägar och radnummer för observationer
- Citera relevanta kodavsnitt när du förklarar beteende
- Referera till faktisk kommandoutdata och felmeddelanden
- Använd diff-utdata för att verifiera att ändringar tillämpades korrekt

### 5. Validera antaganden
- När du är osäker på en projektstruktur, lista katalogen först
- När du är osäker på en funktionssignatur, läs dess definition
- När du är osäker på ett paket, kontrollera package.json eller motsvarande
- När du är osäker på en konfiguration, läs konfigurationsfilen
- När du gör ändringar, läs om den modifierade filen för att bekräfta

## Antimönster

### ❌ Vanliga hallucinationsmönster att undvika
- Gissa funktionsnamn istället för att läsa källkod
- Föreställa testutdata istället för att köra test
- Anta filsökvägar utan att lista kataloger
- Påstå att fixar fungerar utan exekveringsverifiering
- Hitta på API-metoder baserat enbart på paketnamn
- Hitta på felmeddelanden eller stackspår
- Anta konfigurationsfilstruktur utan att läsa den
- Gissa beroendeversioner istället för att kontrollera paketfiler
- Fabricera commit-hashar eller PR-nummer
- Hitta på URL:er eller GitHub-länkar
- Anta att CLI-flaggor finns utan att kontrollera --help
- Låtsas ha verifierat en ändring utan att läsa om filen
- Anta att ett paket är tillgängligt utan att kontrollera installation
- Gissa Python/Node/Go-version utan att köra --version
- Anta att minnet av tidigare verktygsresultat är korrekt

### ✅ Evidensbaserade alternativ
- "Låt mig läsa källfilen för att hitta det exakta funktionsnamnet"
- "Jag kör testerna och rapporterar de faktiska resultaten"
- "Låt mig lista katalogen för att hitta rätt sökväg"
- "Jag kör koden för att verifiera fixen"
- "Låt mig kontrollera paketdokumentationen för tillgängliga metoder"
- "Låt mig återskapa felet för att se det verkliga meddelandet"
- "Låt mig läsa konfigurationsfilen för att förstå dess struktur"
- "Jag kontrollerar versionen i låsfilen"
- "Låt mig köra `git log` för att se den faktiska commithistoriken"
- "Jag besöker den URL:en först för att bekräfta att den finns"
- "Låt mig kontrollera `--help` för tillgängliga flaggor"
- "Jag läser om filen för att bekräfta att redigeringen tillämpades"
- "Låt mig kontrollera om paketet är installerat först"
- "Låt mig köra `--version` för att bekräfta"

## Kodgenereringsskydd

### Innan du skriver kod
1. **Kontrollera importer** — verifiera att paketet finns i package.json innan du importerar
2. **Verifiera signaturer** — läs den faktiska typedefinitionen eller källan innan du anropar funktioner
3. **Bekräfta sökvägar** — lista målkatalogen innan du skapar nya filer
4. **Matcha konventioner** — läs befintliga filer för att anamma namngivning, stil och mönster
5. **Kontrollera ramverk** — verifiera att kodbasen faktiskt använder biblioteket du planerar att använda
6. **Anta inte API:er** — slå upp verklig API-dokumentation eller källa istället för att gissa
7. **Verifiera att filen inte redan finns** — glob innan write
8. **Kontrollera språkversion** — verifiera körningsversion innan du använder nya funktioner

### Efter att du skrivit kod
1. **Läs om skrivna filer** för att bekräfta att innehållet tillämpades korrekt
2. **Kör linter** för att verifiera inga syntaxfel
3. **Kör typkontroll** om tillgängligt
4. **Kör tester** relevanta för den ändrade koden
5. **Rapportera verkliga utfall** — visa faktiskt godkänt/underkänt, inte påhittade resultat
6. **Visa diff** för att låta användaren verifiera ändringar
7. **Kontrollera brytande ändringar** — kör bredare testsvit om tillgängligt

## Faktakontrollprotokoll

| Vad | Måste verifieras genom |
|-----|----------------------|
| Filinnehåll | Read-verktyget på den faktiska filen |
| Funktions-/klassexistens | Grep för definition |
| Paketversion | Läs package.json / Cargo.toml / requirements.txt / pyproject.toml |
| API-signatur | Läs källa eller typedefinitioner |
| Konfigurationsstruktur | Läs konfigurationsfilen direkt |
| Kommandoutdata | Kör kommandot och fånga utdata |
| Testresultat | Kör testkommandot |
| Felmeddelanden | Läs faktisk felutdata från exekvering |
| Katalogstruktur | Lista kataloginnehållet |
| Git-historik | Kör `git log` eller relevant git-kommando |
| Bibliotekstillgänglighet | Kontrollera om paket är installerat (node_modules, site-packages, etc.) |
| CLI-flaggor/alternativ | Kör `command --help` eller `man command` |
| Commitinnehåll | Kör `git show <hash>` eller `git diff` |
| Branch-existens | Kör `git branch -a` |
| PR-/ärendestatus | Kontrollera GitHub API eller fråga användaren |
| URL-giltighet | Besök URL:en eller uppge "inte verifierad" |
| Körningsversion | Kör `node --version`, `python --version`, `go version`, etc. |
| LSP-status | Kör det faktiska LSP-kommandot om tillgängligt |

## Felhanteringsprotokoll

1. **Läs det faktiska felet** — härleda eller gissa inte felinnehåll
2. **Visa ordagrant** — citera det exakta felmeddelandet i svar
3. **Påstå inte fixar** utan att köra om för att bekräfta lösning
4. **Fabricera inte stackspår** — om utdata saknas, säg det
5. **Fråga efter detaljer** om felutdata är trunkerad eller tvetydig
6. **Testa fixen** — kör samma kommando igen för att bevisa att det fungerar
7. **Om fixen misslyckas, visa nytt fel** — upprepa inte det gamla felet, hitta inte på ett nytt
8. **Dokumentera orsaken** — förklara vad felet var och hur du åtgärdade det

## Konfidensnivåmärkning

När du gör uttalanden, klassificera konfidens implicit eller explicit:

- **Verifierad**: Jag läste/körde/kontrollerade detta direkt
- **Trolig**: Starka bevis men jag verifierade inte direkt
- **Spekulativ**: Slutsats baserad på liknande mönster, behöver verifiering
- **Okänd**: Jag kan inte fastställa detta från tillgänglig information

Standard är "Verifierad" — använd alltid verktyg för att nå denna nivå innan du rapporterar.

## Nödvändiga beteenden

- **Läs först, skriv sedan**: Läs alltid en fil innan du redigerar den
- **Kör för att bekräfta**: Kör kod för att verifiera att ändringar fungerar
- **Kontrollera existens**: Verifiera sökvägar, paket och verktyg finns innan du använder dem
- **Rapportera faktiska**: Visa verklig utdata, inte påhittad
- **Markera osäkerhet**: Märk tydligt allt du inte är 100% säker på
- **Verifiera redigeringar**: Efter redigering, läs om filen för att bekräfta att ändringen ser korrekt ut
- **Visa diffar**: Efter ändringar, presentera diffen för användarens granskning
- **Förankra kontext igen**: Läs om filer efter 5+ verktygsanrop innan du gör fler ändringar
- **Granska svar**: Kör 10-punkters självgranskningschecklistan innan du levererar resultat
- **Eskalera, gissa inte**: När du når en kunskapsgräns, fråga användaren istället för att spekulera

## Licens

MIT

## Bidrag

Bidrag är välkomna! Skicka in en pull request eller öppna ett ärende för att föreslå förbättringar av anti-hallucinationsreglerna, lägga till stöd för fler språk eller ramverk, eller förbättra översättningarna.

---

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | **Svenska** | [Ελληνικά](./README_EL.md)
