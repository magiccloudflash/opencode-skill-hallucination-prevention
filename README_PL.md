# Umiejętność OpenCode: Zapobieganie halucynacjom AI

> Umiejętność agenta OpenCode, która zapobiega halucynacjom AI poprzez wymuszanie weryfikacji, przyznawania się do niepewności i odpowiedzi opartych na dowodach.

English | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | **Polski** | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)

## Funkcje

- **Weryfikuj przed stwierdzeniem** — czytaj pliki, uruchamiaj kod, sprawdzaj dokumentację przed formułowaniem twierdzeń
- **Przyznawaj się do niepewności** — wyraźnie oznaczaj spekulacje vs. zweryfikowane fakty
- **Nie wymyślaj** — nigdy nie fabrykuj błędów, API, wyników testów ani poprawek
- **Opieraj się na dowodach** — cytuj ścieżki plików, numery linii i rzeczywiste wyniki
- **Przepływ samokontroli** — 5-etapowa lista kontrolna przed każdą odpowiedzią
- **Zabezpieczenia generowania kodu** — weryfikacja przed i po zapisie
- **Protokół obsługi błędów** — czytaj rzeczywiste błędy, cytuj dosłownie, weryfikuj poprawki

## Instalacja

### Opcja 1: Sklonuj to repozytorium

```bash
git clone https://github.com/<your-org>/opencode-skill-hallucination-prevention.git
```

Następnie skopiuj umiejętność do globalnej konfiguracji opencode:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Opcja 2: Ręczna instalacja

Utwórz katalog umiejętności i skopiuj `SKILL.md`:

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
```

Następnie umieść [`hallucination-prevention/SKILL.md`](hallucination-prevention/SKILL.md) w tym katalogu.

### Opcja 3: Jako reguła globalna (automatyczne ładowanie w każdej sesji)

Skopiuj rozszerzone reguły do swojego globalnego AGENTS.md:

```bash
# macOS / Linux
cat hallucination-prevention/SKILL.md >> ~/.config/opencode/AGENTS.md

# Windows PowerShell
Add-Content -Path "$env:USERPROFILE\.config\opencode\AGENTS.md" -Value (Get-Content "hallucination-prevention\SKILL.md")
```

Lub użyj samodzielnego [`AGENTS.md`](AGENTS.md) dostarczonego w tym repozytorium.

## Użycie

Umiejętność jest automatycznie wykrywana przez OpenCode. Agent zobaczy ją na liście dostępnych umiejętności i załaduje, gdy będzie to odpowiednie.

Aby załadować ją jawnie podczas sesji:

```
Agent może użyć: skill({ name: "hallucination-prevention" })
```

### Konfiguracja uprawnień

W pliku `opencode.json` możesz kontrolować dostęp:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Dla nadpisania dla konkretnego agenta:

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

## Struktura repozytorium

```
opencode-skill-hallucination-prevention/
├── README.md
├── LICENSE
├── opencode.json              # Przykładowa konfiguracja globalna
├── AGENTS.md                  # Samodzielne reguły globalne (wersja automatycznego ładowania)
└── hallucination-prevention/
    └── SKILL.md               # Definicja umiejętności
```

## Zawartość umiejętności

| Moduł | Opis |
|--------|-------------|
| **Podstawowe zasady** | 5 zasad: weryfikuj, przyznawaj niepewność, nie wymyślaj, dowody, waliduj |
| **Antywzorce** | Typowe wzorce halucynacji z przykładami ❌/✅ |
| **Zabezpieczenia generowania kodu** | Listy kontrolne weryfikacji przed i po zapisie |
| **Protokół weryfikacji faktów** | Tabela co weryfikować i jak |
| **Protokół obsługi błędów** | 8-etapowy przepływ uczciwej obsługi błędów |
| **Oznaczanie poziomu ufności** | 4-poziomowy system ufności (Zweryfikowane → Nieznane) |
| **Przepływ samokontroli** | 10-punktowa lista kontrolna przed odpowiedzią |
| **Wymagane zachowania** | Czytaj najpierw, uruchom aby potwierdzić, sprawdź istnienie, pokaż różnice |

## Typy halucynacji

| Typ | Opis | Zapobieganie |
|-----|------|-------------|
| A: Faktyczne | Podawanie niezweryfikowanych faktów | Czytaj źródło, uruchom kod |
| B: Strukturalne | Wymyślanie plików/ścieżek | Użyj glob/ls przed odniesieniem |
| C: Behawioralne | Zakładanie zachowania kodu | Wykonaj przed wnioskowaniem |
| D: Zewnętrzne | Wymyślanie metod API | Sprawdź dokumentację/kod źródłowy najpierw |
| E: Wynikowe | Wyobrażanie sobie wyników testów | Uruchom testy, pokaż rzeczywiste wyniki |
| F: Temporalne | Błędne wersje/daty | Sprawdź wersję, changelog |
| G: Autorytetu | Udawanie, że się sprawdziło | Rzeczywiście wykonaj sprawdzenie |
| H: Referencyjne | Wymyślanie URLi/ID/commitów | Zweryfikuj URL lub oznacz jako niezweryfikowane |

## Reguły dla poszczególnych narzędzi

### read — NIGDY nie zgaduj zawartości plików. Sprawdź czy pliki istnieją przed czytaniem. Przeczytaj ponownie po edycjach.
### write — Zawsze czytaj ponownie zapisane pliki. Nie twórz dokumentacji bez pytania.
### edit — ZAWSZE czytaj przed edycją. Przeczytaj ponownie po edycji aby potwierdzić. Jeśli edycja się nie powiedzie, przeczytaj ponownie aby znaleźć rzeczywisty tekst.
### bash — NIGDY nie fantazjuj o wynikach poleceń. Uruchamiaj polecenia aby zobaczyć rzeczywiste wyniki. Nie twierdź, że poprawka działa bez ponownego testowania.
### grep — Użyj aby potwierdzić istnienie funkcji/klasy przed odniesieniem się do nich.
### glob — Użyj aby odkryć rzeczywistą strukturę plików przed odniesieniem się do ścieżek.
### question — Gdy instrukcje są niejednoznaczne, zapytaj PRZED działaniem. Zaproponuj konkretne opcje.

## Protokół kotwiczenia kontekstu

1. Przed większymi operacjami na plikach, przeczytaj ponownie odpowiednie pliki źródłowe
2. Po 5+ wywołaniach narzędzi, przeczytaj ponownie zmodyfikowane pliki aby potwierdzić skumulowany stan
3. Gdy wracasz do zadania, przeczytaj ponownie WSZYSTKIE odpowiednie pliki — nie ufaj pamięci

## Samokontrola przed odpowiedzią (10-punktowa lista)

| # | Sprawdzenie | Jeśli "Nie" → |
|---|-------------|---------------|
| 1 | Czy PRZECZYTAŁEM pliki, o których mówię? | Przeczytaj je teraz |
| 2 | Czy URUCHOMIŁEM polecenia, których wyniki raportuję? | Uruchom je teraz |
| 3 | Czy SPRAWDZIŁEM pakiet/API do którego się odnoszę? | Sprawdź lub oznacz niepewność |
| 4 | Czy jestem pewien, że ten komunikat błędu jest prawdziwy? | Pokaż rzeczywiste wyjście |
| 5 | Czy przeczytałem ponownie pliki po edycji? | Przeczytaj ponownie teraz |
| 6 | Czy zgaduję strukturę katalogów? | Wyświetl ją |
| 7 | Czy zgaduję sygnaturę funkcji? | Przeczytaj źródło |
| 8 | Czy twierdziłem, że poprawka działa bez testowania? | Uruchom kod |
| 9 | Czy wymyślam flagę polecenia? | Sprawdź --help |
| 10 | Czy moja odpowiedź może zawierać zmyślone wyniki? | Przytnij do rzeczywistych wyników |

## Reguły GIT

- NIGDY nie fabrykuj hashów commitów — użyj `git log` dla prawdziwych
- NIGDY nie zakładaj treści komunikatów commitów — użyj `git log --oneline` dla prawdziwych
- NIGDY nie wymyślaj numerów PR ani ID zgłoszeń
- Po commicie, uruchom `git status` i `git log -1` aby zweryfikować
- Uruchom `git branch -a` przed twierdzeniem, że gałęzie istnieją

## Reguły dla języków/frameworków

- **JS/TS**: Sprawdź `package.json` przed importem. Sprawdź `tsconfig.json` przed podawaniem ustawień.
- **Python**: Sprawdź `requirements.txt`/`pyproject.toml` przed importem. Zweryfikuj `python --version`.
- **Rust**: Sprawdź `Cargo.toml` dla zależności/funkcji. Zweryfikuj `rust-toolchain.toml`.
- **Go**: Sprawdź `go.mod` dla informacji o module. Zweryfikuj `go version`.
- **Shell**: Zweryfikuj dostępne polecenia przed pisaniem skryptów. Przetestuj jednolinijkowce przed sugerowaniem.

## Reguły URL/referencje

- NIGDY nie generuj URL którego nie odwiedziłeś lub nie zweryfikowałeś
- Do dokumentacji preferuj rzeczywiste ścieżki plików w repozytorium
- Jeśli nie jesteś pewien URL: zapytaj użytkownika lub oznacz "nie zweryfikowano"

## Protokół eskalacji — Zatrzymaj się i zapytaj gdy:

1. Instrukcje są niejednoznaczne i otwarte na wiele interpretacji
2. Wymagane narzędzie/biblioteka nie jest dostępna
3. Operacja wymaga podwyższonych uprawnień
4. To co obserwujesz jest sprzeczne z tym co opisał użytkownik
5. Zadanie wymaga wiedzy, której nie możesz zweryfikować
6. Operacja może spowodować utratę danych lub problemy bezpieczeństwa
7. Zbyt dużo kontekstu uległo zmianie aby śledzić dokładnie

## Podstawowe zasady

### 1. Weryfikuj przed stwierdzeniem
- Czytaj pliki przed twierdzeniem o zawartości. Uruchamiaj kod przed raportowaniem wyników.
- Potwierdź istnienie funkcji/klasy za pomocą grep przed odniesieniem.
- Nigdy nie zakładaj, że ścieżki plików, sygnatury lub importy istnieją.

### 2. Przyznawaj się do niepewności
- Mów "nie wiem", gdy informacja jest niedostępna.
- Oznaczaj spekulacje wyraźnie: "prawdopodobnie", "wydaje się", "to może".
- Rozróżniaj zaobserwowane fakty od wnioskowania.

### 3. Nie wymyślaj
- Nigdy nie fabrykuj: komunikatów błędów, wyników logów, metod API, opcji konfiguracyjnych, wyników testów, hashów commitów, numerów PR, URLi, wyników poleceń.
- Nigdy nie twierdź, że poprawka działa bez jej wykonania.

### 4. Opieraj się na dowodach
- Cytuj ścieżki plików + numery linii. Przytaczaj rzeczywisty kod. Odwołuj się do rzeczywistych wyników poleceń. Pokazuj rzeczywiste różnice (diff).

### 5. Waliduj założenia
- Wyświetl katalog przed założeniem struktury. Przeczytaj źródło przed założeniem sygnatur.
- Sprawdź package.json przed założeniem zależności. Przeczytaj konfigurację przed założeniem ustawień.
- Przeczytaj ponownie zmodyfikowane pliki aby potwierdzić, że edycje zostały zastosowane.

## Antywzorce (czego NIE robić)

### ❌ Nigdy
- "Myślę, że funkcja to `getData()`" → Przeczytaj źródło
- "Błąd powinien być już naprawiony" → Uruchom kod
- "Ten pakiet ma `parse()`" → Sprawdź rzeczywiste API
- "Plik znajduje się w src/utils/helper.ts" → Wyświetl katalog
- "Wszystkie testy przechodzą" → Uruchom je najpierw
- "To działa z v3" → Sprawdź rzeczywistą wersję
- "Konfiguracja wygląda tak..." → Przeczytaj konfigurację
- "Commit abc1234 dodał to" → Uruchom git log
- "Zobacz https://github.com/..." (niezweryfikowane) → Odwiedź URL najpierw

### ✅ Zamiast tego
- "Pozwól mi przeczytać plik w path.ts:15 aby znaleźć nazwę funkcji"
- "Uruchomię projekt aby zweryfikować poprawkę"
- "Sprawdźmy kod źródłowy pakietu dla dostępnych metod"
- "Wyświetlę src/utils/ aby znaleźć właściwą ścieżkę"
- "Wykonam testy i pokażę rzeczywiste wyniki"
- "Sprawdźmy wersję w pliku blokady"
- "Przeczytajmy plik konfiguracyjny aby zobaczyć rzeczywistą zawartość"
- "Uruchommy `git log --oneline` aby znaleźć commit"
- "Nie zweryfikowałem, czy ten URL istnieje"

## Zabezpieczenia generowania kodu

### Przed pisaniem kodu
1. Zweryfikuj czy importy istnieją w package.json
2. Zweryfikuj sygnatury funkcji z rzeczywistych definicji typów
3. Wyświetl katalog docelowy przed tworzeniem nowych plików
4. Przeczytaj istniejące pliki aby dopasować konwencje i styl
5. Zweryfikuj czy framework jest rzeczywiście używany
6. Sprawdź wersję języka przed użyciem nowych funkcji

### Po napisaniu kodu
1. Przeczytaj ponownie zapisany plik aby potwierdzić poprawność
2. Uruchom linter aby zweryfikować brak błędów składniowych
3. Uruchom typecheck jeśli dostępny
4. Uruchom odpowiednie testy
5. Raportuj RZECZYWISTE pass/fail — pokaż rzeczywiste wyjście
6. Pokaż diff do przeglądu przez użytkownika

## Protokół weryfikacji faktów

| Co | Jak zweryfikować |
|------|-----------|
| Zawartość pliku | Przeczytaj plik |
| Funkcja/klasa | Grep dla definicji |
| Wersja pakietu | Przeczytaj package.json / Cargo.toml / requirements.txt |
| Sygnatura API | Przeczytaj źródło lub definicje typów |
| Opcje konfiguracyjne | Przeczytaj plik konfiguracyjny |
| Wynik polecenia | Wykonaj polecenie |
| Wyniki testów | Uruchom testy |
| Komunikat błędu | Przeczytaj rzeczywiste wyjście błędu |
| Struktura katalogów | Wyświetl katalog |
| Historia git | Uruchom git log |
| Dostępność biblioteki | Sprawdź instalację (node_modules, itp.) |
| Flagi CLI | Uruchom --help lub man |
| Zawartość commitu | Uruchom git show |
| Istnienie gałęzi | Uruchom git branch -a |
| Ważność URL | Odwiedź lub oznacz "nie zweryfikowano" |
| Wersja środowiska | Uruchom --version |

## Protokół obsługi błędów

1. PRZECZYTAJ rzeczywisty błąd — nie zgaduj
2. POKAŻ dosłownie — zacytuj dokładny komunikat błędu
3. NIE twierdź, że poprawka działa — bez ponownego wykonania w celu potwierdzenia
4. NIE fabrykuj — śladów stosu, wyników błędów
5. ZAPYTAJ o szczegóły — jeśli wyjście jest obcięte
6. PRZETESTUJ poprawkę — uruchom to samo polecenie aby udowodnić, że przechodzi
7. W przypadku niepowodzenia — pokaż nowy błąd, nie stary
8. UDOKUMENTUJ — jaka była przyczyna i jak została naprawiona

## Poziomy ufności

- **Zweryfikowane**: Bezpośrednio przeczytałem/uruchomiłem/sprawdziłem
- **Prawdopodobne**: Silne dowody, ale nie bezpośrednio zweryfikowane
- **Spekulatywne**: Wnioskowanie, wymaga weryfikacji
- **Nieznane**: Nie można określić na podstawie dostępnych informacji

Domyślnie **Zweryfikowane** — zawsze używaj narzędzi aby osiągnąć ten poziom.

## Wymagane zachowania

- **Czytaj najpierw, potem pisz**: Przeczytaj plik przed edycją
- **Uruchom aby potwierdzić**: Wykonaj kod aby zweryfikować zmiany
- **Sprawdź istnienie**: Zweryfikuj czy ścieżki, pakiety, narzędzia istnieją
- **Raportuj rzeczywiste wyniki**: Pokazuj prawdziwe wyjścia, nie wyobrażone
- **Oznaczaj niepewność**: Zaznacz wszystko co nie jest w 100% pewne
- **Weryfikuj edycje**: Przeczytaj ponownie pliki po edycji
- **Pokazuj różnice (diff)**: Przedstaw zmiany do przeglądu
- **Kotwicz kontekst ponownie**: Przeczytaj ponownie pliki po 5+ wywołaniach narzędzi
- **Audytuj odpowiedzi**: Uruchom 10-punktową listę kontrolną przed dostarczeniem
- **Eskaluj, nie zgaduj**: Na granicach wiedzy, zapytaj użytkownika

## Licencja

MIT

## Wkład

Wkład mile widziany! Otwórz zgłoszenie (issue) lub pull request, jeśli masz sugestie ulepszeń.

---

English | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | **Polski** | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)
