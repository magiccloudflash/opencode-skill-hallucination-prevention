# OpenCode Vaardigheid: AI-Hallucinatiepreventie

> Een OpenCode-agentvaardigheid die AI-hallucinaties voorkomt door verificatie, het erkennen van onzekerheid en op bewijs gebaseerde antwoorden af te dwingen.

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | **Nederlands** | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)

## Functies

- **Verifieer voordat je beweert** — lees bestanden, voer code uit, raadpleeg documentatie voordat je conclusies trekt
- **Erken onzekerheid** — maak duidelijk onderscheid tussen speculatie en geverifieerde feiten
- **Verzin niets** — verzin nooit fouten, API's, testresultaten of oplossingen
- **Baseer je op bewijs** — citeer bestandspaden, regelnummers en daadwerkelijke uitvoer
- **Zelfcontrole** — 10-punts checklist vóór elk antwoord
- **Codegeneratie-beveiligingen** — dubbele controle vóór en na het schrijven van code
- **Gereedschapsspecifieke regels** — anti-hallucinatieregels voor read, write, edit, bash, grep, glob en meer
- **8 hallucinatietypen** — elk type heeft zijn eigen preventiestrategie

## Snelle installatie

### Methode 1: Repository klonen (aanbevolen)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Kopieer de vaardigheid vervolgens naar je globale OpenCode-configuratie:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Methode 2: Handmatige installatie

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Methode 3: Als globale regel (automatisch laden bij elke sessie — aanbevolen)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> Het globale `AGENTS.md`-bestand wordt automatisch in elke sessie geladen, zonder dat de vaardigheid handmatig hoeft te worden aangeroepen.

## Gebruik

### Op aanvraag (Methode 1, 2)

De vaardigheid wordt automatisch herkend door OpenCode. De agent laadt deze indien nodig:

```javascript
skill({ name: "hallucination-prevention" })
```

### Automatisch laden (Methode 3)

De regels uit `AGENTS.md` worden automatisch in de context van elke sessie ingevoegd. **Geen handmatige actie vereist.**

## Machtigingsconfiguratie

In `opencode.json`:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Voor specifieke agents:

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

Machtigingen ondersteunen jokertekens:

| Machtiging | Gedrag |
|------------|--------|
| `allow` | Vaardigheid wordt onmiddellijk geladen |
| `deny` | Vaardigheid wordt verborgen, toegang geweigerd |
| `ask` | Bevestiging vereist vóór laden |

## Repository-structuur

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentatie in het Engels
├── README_CN.md                              # Documentatie in het Chinees
├── README_TW.md                              # Documentatie in het Chinees (Traditioneel)
├── README_RU.md                              # Documentatie in het Russisch
├── README_FR.md                              # Documentatie in het Frans
├── README_DE.md                              # Documentatie in het Duits
├── README_IT.md                              # Documentatie in het Italiaans
├── README_ES.md                              # Documentatie in het Spaans
├── README_CU.md                              # Documentatie in het Spaans (Cuba)
├── README_PT.md                              # Documentatie in het Portugees
├── README_BR.md                              # Documentatie in het Portugees (Brazilië)
├── README_AR.md                              # Documentatie in het Arabisch
├── README_HI.md                              # Documentatie in het Hindi
├── README_UR.md                              # Documentatie in het Urdu
├── README_TH.md                              # Documentatie in het Thai
├── README_FA.md                              # Documentatie in het Perzisch
├── README_TR.md                              # Documentatie in het Turks
├── README_PL.md                              # Documentatie in het Pools
├── README_NL.md                              # Documentatie in het Nederlands (dit bestand)
├── README_SV.md                              # Documentatie in het Zweeds
├── README_EL.md                              # Documentatie in het Grieks
├── LICENSE                                   # MIT-licentie
├── opencode.json                             # Voorbeeldconfiguratie
├── AGENTS.md                                 # Globale regels (automatisch laden)
└── hallucination-prevention/
    └── SKILL.md                              # Vaardigheidsdefinitiebestand
```

## Inhoud van de vaardigheid v2.0

### Classificatie van de 8 hallucinatietypen

| Type | Beschrijving | Voorbeeld | Preventie |
|------|-------------|-----------|-----------|
| **A: Feitelijk** | Ongeverifieerde feiten beweren | "Deze functie retourneert een Promise" (zonder code te lezen) | Lees bronnen, voer code uit |
| **B: Structureel** | Bestanden/paden verzinnen | "De handler staat in src/routes/auth.ts" (bestand bestaat niet) | Gebruik glob/ls om te verifiëren |
| **C: Gedragsmatig** | Aannemen van codegedrag | "De fout is opgelost" (zonder project uit te voeren) | Voer code uit en toon resultaat |
| **D: Extern** | API's/functies verzinnen | "Lodash heeft een groupByAndSort()-functie" (bestaat niet) | Controleer documentatie of bronnen |
| **E: Resultaat** | Testresultaten verbeelden | "Alle tests slagen" (zonder ze uit te voeren) | Voer tests uit, toon werkelijk resultaat |
| **F: Temporeel** | Verkeerde versies/data | "Node v24 heeft deze functie toegevoegd" (v24 bestaat niet) | Controleer versie en changelog |
| **G: Autoriteit** | Doen alsof je hebt gecontroleerd | "Ik heb bevestigd dat de wijziging correct is" (zonder opnieuw te lezen) | Voer de controle uit en toon bewijs |
| **H: Referentie** | URL's/ID's/commits verzinnen | "Zie https://github.com/foo/issues/99999" (valse link) | Controleer URL of geef "niet geverifieerd" aan |

### Gereedschapsspecifieke regels

| Gereedschap | Hoofdregel |
|-------------|------------|
| **read** | Raad geen inhoud; controleer bestandsexistentie; lees opnieuw na bewerking |
| **write** | Lees geschreven bestand direct opnieuw; maak geen documentatie zonder verzoek |
| **edit** | Lees altijd vóór bewerken; lees opnieuw erna; let op inspringing |
| **bash** | Verzin geen opdrachtuitvoer; voer opnieuw uit om oplossingen te verifiëren; vraag vóór destructieve acties |
| **grep** | Bevestig bestaan van functies/klassen via grep |
| **glob** | Gebruik glob om structuur te controleren voordat je paden noemt |
| **question** | Bij dubbelzinnigheid, vraag eerst, handel daarna |

### 10-punts zelfcontrole-checklist

Controleer vóór elk antwoord:

| # | Vraag | Actie bij "Nee" |
|---|-------|-----------------|
| 1 | Heb ik de bestanden waar ik over praat echt GELEZEN? | Lees ze nu |
| 2 | Heb ik de opdrachten waarvan ik de uitvoer rapporteer echt UITGEVOERD? | Voer ze nu uit |
| 3 | Heb ik het pakket/de API waar ik naar verwijs echt GECONTROLEERD? | Controleer of markeer als onzeker |
| 4 | Weet ik zeker dat deze foutmelding echt is? | Toon de werkelijke uitvoer |
| 5 | Heb ik bestanden opnieuw gelezen na het bewerken? | Lees nu opnieuw |
| 6 | Gok ik de mapstructuur? | Toon de mappenstructuur |
| 7 | Gok ik een functiehandtekening? | Lees de broncode |
| 8 | Heb ik beweerd dat een oplossing werkt zonder deze te testen? | Voer de code uit |
| 9 | Verzin ik opdrachtopties? | Controleer --help |
| 10 | Zou mijn antwoord verzonnen gegevens kunnen bevatten? | Houd alleen de werkelijke uitvoer |

### Contextverankeringsprotocol

1. **Vóór belangrijke bestandsbewerkingen**: lees de relevante bronbestanden opnieuw
2. **Na 5+ gereedschapsaanroepen**: lees gewijzigde bestanden opnieuw, controleer de geaccumuleerde status
3. **Bij terugkeer naar een taak**: lees ALLE relevante bestanden opnieuw — vertrouw niet op het geheugen

### Taal-/frameworkspecifieke regels

- **JS/TS**: Controleer package.json vóór importeren; controleer tsconfig.json
- **Python**: Controleer requirements.txt/pyproject.toml; controleer python --version
- **Rust**: Controleer Cargo.toml; controleer rust-toolchain.toml
- **Go**: Controleer go.mod; controleer go version
- **Shell**: Controleer beschikbaarheid van opdrachten vóór het schrijven van scripts; test opdrachten vóór suggestie

### Git-specifieke regels

- Verzin nooit commit-hashes — gebruik `git log`
- Neem nooit commit-berichten aan — gebruik `git log --oneline`
- Verzin nooit PR-nummers of issue-ID's
- Voer na een commit `git status` en `git log -1` uit
- Voer `git branch -a` uit voordat je beweert dat een branch bestaat

### Vertrouwensniveaus

- **Geverifieerd**: Ik heb direct gelezen/uitgevoerd/gecontroleerd
- **Waarschijnlijk**: Sterk bewijs maar zonder directe verificatie
- **Speculatief**: Afleiding op basis van vergelijkbare patronen, vereist verificatie
- **Onbekend**: Kan niet worden bepaald uit beschikbare informatie

Standaard wordt het niveau **Geverifieerd** gebruikt.

### Escalatieprotocol (wanneer stoppen en de gebruiker vragen)

Stop onmiddellijk en vraag de gebruiker wanneer:
1. Instructies dubbelzinnig zijn, meerdere interpretaties mogelijk
2. Het vereiste gereedschap/de bibliotheek niet beschikbaar is
3. De bewerking verhoogde privileges vereist
4. Wat je waarneemt in tegenspraak is met de beschrijving van de gebruiker
5. De taak kennis vereist die je niet kunt verifiëren
6. De bewerking gegevensverlies of beveiligingsproblemen kan veroorzaken
7. De context te veel is veranderd om nauwkeurig te kunnen volgen

## Licentie

MIT — zie het bestand [LICENSE](LICENSE)

## Bijdragen

Issues en Pull Requests zijn welkom:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navigatie** : [English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | **Nederlands** | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)
