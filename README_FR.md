# Compétence OpenCode : Prévention des hallucinations IA

> Une compétence d'agent OpenCode qui empêche les hallucinations de l'IA en imposant la vérification, la reconnaissance de l'incertitude et des réponses fondées sur des preuves.

[English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | Français

## Fonctionnalités

- **Vérifie avant d'affirmer** — lis les fichiers, exécute le code, consulte la documentation avant de tirer des conclusions
- **Reconnais l'incertitude** — distingue clairement les spéculations des faits vérifiés
- **N'invente pas** — ne fabrique jamais d'erreurs, d'API, de résultats de tests ou de correctifs
- **Fonde-toi sur des preuves** — cite les chemins de fichiers, les numéros de lignes et les résultats réels
- **Auto-vérification** — liste de contrôle en 10 points avant chaque réponse
- **Gardes de génération de code** — double vérification avant et après l'écriture de code
- **Règles par outil** — règles anti-hallucination spécifiques pour read, write, edit, bash, grep, glob, etc.
- **8 types d'hallucinations** — chaque type a sa propre stratégie de prévention

## Installation rapide

### Méthode 1 : Cloner le dépôt (recommandé)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Copiez ensuite la compétence dans votre configuration globale OpenCode :

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Méthode 2 : Installation manuelle

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Méthode 3 : Règle globale (chargement automatique à chaque session — recommandé)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> Le fichier global `AGENTS.md` est automatiquement chargé dans chaque session, sans nécessiter d'appel manuel à la compétence.

## Utilisation

### À la demande (Méthode 1, 2)

La compétence est automatiquement découverte par OpenCode. L'agent la chargera si nécessaire :

```javascript
skill({ name: "hallucination-prevention" })
```

### Chargement automatique (Méthode 3)

Les règles de `AGENTS.md` sont automatiquement injectées dans le contexte de chaque session. **Aucune action manuelle requise.**

## Configuration des permissions

Dans `opencode.json` :

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Pour des agents spécifiques :

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

Les permissions supportent les motifs génériques :

| Permission | Comportement |
|------------|---------------|
| `allow` | La compétence se charge immédiatement |
| `deny` | La compétence est cachée, accès refusé |
| `ask` | Demande de confirmation avant chargement |

## Structure du dépôt

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentation en anglais
├── README_CN.md                              # Documentation en chinois
├── README_RU.md                              # Documentation en russe
├── README_FR.md                              # Documentation en français (ce fichier)
├── LICENSE                                   # Licence MIT
├── opencode.json                             # Exemple de configuration
├── AGENTS.md                                 # Règles globales (auto-chargement)
└── hallucination-prevention/
    └── SKILL.md                              # Fichier de définition de la compétence
```

## Contenu de la compétence v2.0

### Classification des 8 types d'hallucinations

| Type | Description | Exemple | Prévention |
|------|-------------|---------|------------|
| **A: Factuelle** | Affirmer des faits non vérifiés | "Cette fonction retourne une Promise" (sans lire le code) | Lis les sources, exécute le code |
| **B: Structurelle** | Inventer des fichiers/chemins | "Le handler est dans src/routes/auth.ts" (le fichier n'existe pas) | Utilise glob/ls pour vérifier |
| **C: Comportementale** | Présumer du comportement du code | "Le bug est corrigé" (sans exécuter le projet) | Exécute le code et montre le résultat |
| **D: Externe** | Inventer des API/fonctions | "Lodash a une fonction groupByAndSort()" (n'existe pas) | Vérifie la documentation ou les sources |
| **E: Résultat** | Imaginer des résultats de tests | "Tous les tests passent" (sans les exécuter) | Lance les tests, montre le résultat réel |
| **F: Temporelle** | Mauvaises versions/dates | "Node v24 a ajouté cette fonctionnalité" (v24 n'existe pas) | Vérifie la version et le changelog |
| **G: Autorité** | Prétendre avoir vérifié | "J'ai confirmé que la modification est correcte" (sans relecture) | Effectue la vérification et montre les preuves |
| **H: Référence** | Inventer des URL/ID/commits | "Voir https://github.com/foo/issues/99999" (lien faux) | Vérifie l'URL ou indique "non vérifié" |

### Règles par outil

| Outil | Règle principale |
|-------|-----------------|
| **read** | Ne devine pas le contenu ; vérifie l'existence du fichier ; relis après édition |
| **write** | Relis immédiatement après écriture ; ne crée pas de documentation sans demande |
| **edit** | Lis toujours avant d'éditer ; relis après ; respecte l'indentation |
| **bash** | N'invente pas la sortie des commandes ; relance pour vérifier les correctifs ; demande avant actions destructrices |
| **grep** | Confirme l'existence des fonctions/classes via grep |
| **glob** | Utilise glob pour vérifier la structure avant de référencer des chemins |
| **question** | En cas d'ambiguïté, demande d'abord, agis ensuite |

### Liste de contrôle en 10 points

Avant chaque réponse, vérifie :

| # | Question | Action si "Non" |
|---|----------|-----------------|
| 1 | Ai-je réellement LU les fichiers dont je parle ? | Lis-les maintenant |
| 2 | Ai-je réellement EXÉCUTÉ les commandes dont je rapporte les résultats ? | Exécute-les maintenant |
| 3 | Ai-je réellement VÉRIFIÉ le paquet/API que je référence ? | Vérifie ou signale l'incertitude |
| 4 | Suis-je certain que ce message d'erreur est réel ? | Montre la sortie réelle |
| 5 | Ai-je relu les fichiers après les avoir modifiés ? | Relis maintenant |
| 6 | Suis-je en train de deviner la structure des répertoires ? | Liste le répertoire |
| 7 | Suis-je en train de deviner une signature de fonction ? | Lis le code source |
| 8 | Ai-je prétendu qu'un correctif fonctionne sans le tester ? | Exécute le code |
| 9 | Suis-je en train d'inventer des options de commande ? | Vérifie --help |
| 10 | Ma réponse pourrait-elle contenir des données fabriquées ? | Ne garde que le résultat réel |

### Protocole d'ancrage contextuel

1. **Avant les opérations majeures sur les fichiers** : relis les fichiers source concernés
2. **Après 5+ appels d'outils** : relis les fichiers modifiés, vérifie l'état accumulé
3. **En revenant à une tâche** : relis TOUS les fichiers pertinents — ne te fie pas à la mémoire

### Règles par langage/framework

- **JS/TS** : Vérifie package.json avant d'importer ; vérifie tsconfig.json
- **Python** : Vérifie requirements.txt/pyproject.toml ; vérifie python --version
- **Rust** : Vérifie Cargo.toml ; vérifie rust-toolchain.toml
- **Go** : Vérifie go.mod ; vérifie go version
- **Shell** : Vérifie la disponibilité des commandes avant d'écrire des scripts ; teste les commandes avant de les suggérer

### Règles Git spécifiques

- N'invente jamais de hachages de commit — utilise `git log`
- Ne suppose jamais les messages de commit — utilise `git log --oneline`
- N'invente jamais de numéros de PR ou d'ID d'issue
- Après un commit, exécute `git status` et `git log -1`
- Avant d'affirmer l'existence d'une branche, exécute `git branch -a`

### Niveaux de confiance

- **Vérifié** : J'ai lu/exécuté/vérifié directement
- **Probable** : Fortes preuves mais sans vérification directe
- **Spéculatif** : Déduction basée sur des motifs similaires, nécessite vérification
- **Inconnu** : Impossible de déterminer à partir des informations disponibles

Par défaut, utilise le niveau **Vérifié**.

### Protocole d'escalade (quand s'arrêter et demander à l'utilisateur)

Arrête-toi immédiatement et demande à l'utilisateur quand :
1. Les instructions sont ambiguës, plusieurs interprétations possibles
2. L'outil/bibliothèque requis n'est pas disponible
3. L'opération nécessite des privilèges élevés
4. Ce que tu observes contredit la description de l'utilisateur
5. La tâche nécessite des connaissances que tu ne peux pas vérifier
6. L'opération pourrait entraîner une perte de données ou des problèmes de sécurité
7. Le contexte a trop changé pour être suivi avec précision

## Licence

MIT — voir le fichier [LICENSE](LICENSE)

## Contribution

Les Issues et Pull Requests sont les bienvenues :

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navigation** : [English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | **Français**
