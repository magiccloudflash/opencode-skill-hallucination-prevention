# OpenCode-Fähigkeit: KI-Halluzinationsprävention

> Eine OpenCode-Agent-Fähigkeit, die KI-Halluzinationen verhindert, indem sie Überprüfung, Unsicherheitseingeständnis und evidenzbasierte Antworten erzwingt.

[English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | Deutsch

## Funktionen

- **Überprüfe, bevor du behauptest** — lies Dateien, führe Code aus, konsultiere Dokumentation, bevor du Schlussfolgerungen ziehst
- **Erkenne Unsicherheit an** — unterscheide klar zwischen Spekulation und überprüften Fakten
- **Erfinde nichts** — fabriziere niemals Fehler, APIs, Testergebnisse oder Korrekturen
- **Stütze dich auf Beweise** — zitiere Dateipfade, Zeilennummern und tatsächliche Ausgaben
- **Selbstüberprüfung** — 10-Punkte-Checkliste vor jeder Antwort
- **Code-Generierungs-Schutzmaßnahmen** — doppelte Überprüfung vor und nach dem Schreiben von Code
- **Werkzeugspezifische Regeln** — Anti-Halluzinationsregeln für read, write, edit, bash, grep, glob und mehr
- **8 Halluzinationstypen** — jeder Typ hat seine eigene Präventionsstrategie

## Schnellinstallation

### Methode 1: Repository klonen (empfohlen)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Kopiere die Fähigkeit dann in deine globale OpenCode-Konfiguration:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Methode 2: Manuelle Installation

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Methode 3: Als globale Regel (automatisches Laden bei jeder Sitzung — empfohlen)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> Die globale `AGENTS.md`-Datei wird automatisch in jede Sitzung geladen, ohne dass ein manueller Aufruf der Fähigkeit erforderlich ist.

## Verwendung

### Auf Abruf (Methode 1, 2)

Die Fähigkeit wird automatisch von OpenCode erkannt. Der Agent lädt sie bei Bedarf:

```javascript
skill({ name: "hallucination-prevention" })
```

### Automatisches Laden (Methode 3)

Die Regeln aus `AGENTS.md` werden automatisch in den Kontext jeder Sitzung eingefügt. **Keine manuellen Aktionen erforderlich.**

## Berechtigungskonfiguration

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

Für einzelne Agenten:

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

Berechtigungen unterstützen Platzhaltermuster:

| Berechtigung | Verhalten |
|-------------|-----------|
| `allow` | Fähigkeit wird sofort geladen |
| `deny` | Fähigkeit wird vor dem Agenten verborgen, Zugriff verweigert |
| `ask` | Bestätigungsanfrage vor dem Laden |

## Repository-Struktur

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Dokumentation auf Englisch
├── README_CN.md                              # Dokumentation auf Chinesisch
├── README_RU.md                              # Dokumentation auf Russisch
├── README_FR.md                              # Dokumentation auf Französisch
├── README_DE.md                              # Dokumentation auf Deutsch (diese Datei)
├── LICENSE                                   # MIT-Lizenz
├── opencode.json                             # Beispielkonfiguration
├── AGENTS.md                                 # Globale Regeln (automatisches Laden)
└── hallucination-prevention/
    └── SKILL.md                              # Fähigkeitsdefinitionsdatei
## Inhalt der Fähigkeit v2.0

### Klassifikation der 8 Halluzinationstypen

| Typ | Beschreibung | Beispiel | Prävention |
|-----|-------------|----------|------------|
| **A: Faktisch** | Behauptung ungeprüfter Fakten | "Diese Funktion gibt ein Promise zurück" (ohne Code gelesen) | Lies Quellen, führe Code aus |
| **B: Strukturell** | Erfinden von Dateien/Pfaden | "Der Handler ist in src/routes/auth.ts" (Datei existiert nicht) | Verwende glob/ls zur Überprüfung |
| **C: Verhaltensbedingt** | Annahme von Codeverhalten | "Der Fehler ist behoben" (ohne Projekt ausgeführt) | Führe Code aus und zeige Ergebnis |
| **D: Extern** | Erfinden von APIs/Funktionen | "Lodash hat eine groupByAndSort()-Funktion" (existiert nicht) | Überprüfe Dokumentation oder Quellen |
| **E: Ergebnis** | Vorstellen von Testergebnissen | "Alle Tests bestehen" (ohne sie auszuführen) | Führe Tests aus, zeige tatsächliches Ergebnis |
| **F: Zeitlich** | Falsche Versionen/Daten | "Node v24 hat diese Funktion hinzugefügt" (v24 existiert nicht) | Überprüfe Version und Changelog |
| **G: Autorität** | Vorgeben, überprüft zu haben | "Ich habe bestätigt, dass die Änderung korrekt ist" (ohne erneutes Lesen) | Führe die Überprüfung durch und zeige Beweise |
| **H: Referenz** | Erfinden von URLs/IDs/Commits | "Siehe https://github.com/foo/issues/99999" (falscher Link) | Überprüfe URL oder gib "nicht überprüft" an |

### Werkzeugspezifische Regeln

| Werkzeug | Hauptregel |
|----------|-----------|
| **read** | Rate nicht den Inhalt; überprüfe Dateiexistenz; lies nach Bearbeitung erneut |
| **write** | Lies Geschriebenes sofort erneut; erstelle keine Dokumentation ohne Aufforderung |
| **edit** | Lies immer vor dem Bearbeiten; lies danach erneut; beachte Einrückungen |
| **bash** | Erfinde keine Befehlsausgaben; starte neu zur Korrekturüberprüfung; frage vor destruktiven Aktionen |
| **grep** | Bestätige Existenz von Funktionen/Klassen via grep |
| **glob** | Verwende glob zur Strukturüberprüfung vor Pfadangabe |
| **question** | Bei Unklarheiten frage zuerst, handle dann |

### 10-Punkte-Selbstüberprüfungs-Checkliste

Überprüfe vor jeder Antwort:

| # | Frage | Aktion bei "Nein" |
|---|-------|-------------------|
| 1 | Habe ich die Dateien, über die ich spreche, tatsächlich GELESEN? | Lies sie jetzt |
| 2 | Habe ich die Befehle, deren Ausgabe ich melde, tatsächlich AUSGEFÜHRT? | Führe sie jetzt aus |
| 3 | Habe ich das Paket/die API, auf das/die ich verweise, tatsächlich ÜBERPRÜFT? | Überprüfe oder markiere als unsicher |
| 4 | Bin ich sicher, dass diese Fehlermeldung echt ist? | Zeige tatsächliche Ausgabe |
| 5 | Habe ich Dateien nach der Bearbeitung erneut gelesen? | Lies jetzt erneut |
| 6 | Rate ich die Verzeichnisstruktur? | Liste das Verzeichnis auf |
| 7 | Rate ich eine Funktionssignatur? | Lies den Quellcode |
| 8 | Habe ich behauptet, dass eine Korrektur funktioniert, ohne sie zu testen? | Führe den Code aus |
| 9 | Erfinde ich Befehlsoptionen? | Überprüfe --help |
| 10 | Könnte meine Antwort erfundene Daten enthalten? | Behalte nur die tatsächliche Ausgabe |

### Kontextverankerungsprotokoll

1. **Vor wichtigen Dateioperationen**: lies die relevanten Quelldateien erneut
2. **Nach 5+ Werkzeugaufrufen**: lies geänderte Dateien erneut, überprüfe den akkumulierten Zustand
3. **Bei Rückkehr zu einer Aufgabe**: lies ALLE relevanten Dateien erneut — verlasse dich nicht auf das Gedächtnis

### Sprach-/Frameworkspezifische Regeln

- **JS/TS**: Überprüfe package.json vor dem Importieren; überprüfe tsconfig.json
- **Python**: Überprüfe requirements.txt/pyproject.toml; überprüfe python --version
- **Rust**: Überprüfe Cargo.toml; überprüfe rust-toolchain.toml
- **Go**: Überprüfe go.mod; überprüfe go version
- **Shell**: Überprüfe Verfügbarkeit von Befehlen vor dem Scripting; teste Befehle vor dem Vorschlagen

### Git-spezifische Regeln

- Erfinde niemals Commit-Hashes — verwende `git log`
- Nimm niemals Commit-Nachrichten an — verwende `git log --oneline`
- Erfinde niemals PR-Nummern oder Issue-IDs
- Nach einem Commit führe `git status` und `git log -1` aus
- Bevor du die Existenz eines Branches behauptest, führe `git branch -a` aus

### Vertrauensniveaus

- **Überprüft**: Ich habe direkt gelesen/ausgeführt/überprüft
- **Wahrscheinlich**: Starke Beweise, aber ohne direkte Überprüfung
- **Spekulativ**: Schlussfolgerung basierend auf ähnlichen Mustern, Überprüfung erforderlich
- **Unbekannt**: Kann aus verfügbaren Informationen nicht bestimmt werden

Standardmäßig verwende das Niveau **Überprüft**.

### Eskalationsprotokoll (wann anhalten und den Benutzer fragen)

Halte sofort an und frage den Benutzer, wenn:
1. Anweisungen mehrdeutig sind, mehrere Interpretationen möglich
2. Das erforderliche Werkzeug/die Bibliothek nicht verfügbar ist
3. Die Operation erhöhte Privilegien erfordert
4. Das Beobachtete der Benutzerbeschreibung widerspricht
5. Die Aufgabe Wissen erfordert, das du nicht überprüfen kannst
6. Die Operation zu Datenverlust oder Sicherheitsproblemen führen könnte
7. Der Kontext zu stark verändert wurde, um genau verfolgt zu werden

## Lizenz

MIT — siehe Datei [LICENSE](LICENSE)

## Beitrag

Issues und Pull Requests sind willkommen:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navigation** : [English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | **Deutsch**
