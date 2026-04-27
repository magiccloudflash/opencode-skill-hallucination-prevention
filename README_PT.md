# Habilidade OpenCode: Prevenção de alucinações de IA

> Uma habilidade de agente OpenCode que previne alucinações de IA aplicando verificação, reconhecimento de incerteza e respostas baseadas em evidências.

[English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | Português

## Funcionalidades

- **Verifique antes de afirmar** — leia arquivos, execute código, consulte documentação antes de concluir
- **Reconheça a incerteza** — distinga claramente entre especulação e factos verificados
- **Não invente** — nunca fabrique erros, APIs, resultados de testes ou correções
- **Baseie-se em evidências** — cite caminhos de ficheiros, números de linha e resultados reais
- **Autoverificação** — lista de verificação de 10 pontos antes de cada resposta
- **Guardas de geração de código** — dupla verificação antes e depois de escrever código
- **Regras por ferramenta** — regras anti-alucinação específicas para read, write, edit, bash, grep, glob, etc.
- **8 tipos de alucinações** — cada tipo tem a sua própria estratégia de prevenção

## Instalação rápida

### Método 1: Clonar o repositório (recomendado)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Copie a habilidade para a sua configuração global do OpenCode:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Método 2: Instalação manual

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Método 3: Como regra global (carregamento automático em cada sessão — recomendado)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> O ficheiro global `AGENTS.md` é carregado automaticamente em cada sessão, sem necessidade de invocar a habilidade manualmente.

## Utilização

### Sob demanda (Método 1, 2)

O OpenCode descobre automaticamente a habilidade. O agente carrega-a quando necessário:

```javascript
skill({ name: "hallucination-prevention" })
```

### Carregamento automático (Método 3)

As regras do `AGENTS.md` são injetadas automaticamente no contexto de cada sessão. **Nenhuma ação manual necessária.**

## Configuração de permissões

Em `opencode.json`:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Para agentes específicos:

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

As permissões suportam padrões curinga:

| Permissão | Comportamento |
|-----------|---------------|
| `allow` | A habilidade é carregada imediatamente |
| `deny` | A habilidade fica oculta do agente, acesso negado |
| `ask` | Solicita confirmação antes de carregar |

## Estrutura do repositório

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentação em inglês
├── README_CN.md                              # Documentação em chinês
├── README_RU.md                              # Documentação em russo
├── README_FR.md                              # Documentação em francês
├── README_DE.md                              # Documentação em alemão
├── README_ES.md                              # Documentação em espanhol
├── README_PT.md                              # Documentação em português (este ficheiro)
├── LICENSE                                   # Licença MIT
├── opencode.json                             # Exemplo de configuração
├── AGENTS.md                                 # Regras globais (carregamento automático)
└── hallucination-prevention/
    └── SKILL.md                              # Ficheiro de definição da habilidade
```

## Conteúdo da habilidade v2.0

### Classificação dos 8 tipos de alucinações

| Tipo | Descrição | Exemplo | Prevenção |
|------|-----------|---------|-----------|
| **A: Factual** | Afirmar factos não verificados | "Esta função retorna uma Promise" (sem ler o código) | Leia fontes, execute código |
| **B: Estrutural** | Inventar ficheiros/caminhos | "O handler está em src/routes/auth.ts" (ficheiro não existe) | Use glob/ls para verificar |
| **C: Comportamental** | Supor comportamento do código | "O erro está corrigido" (sem executar o projeto) | Execute o código e mostre o resultado |
| **D: Externa** | Inventar APIs/funções | "Lodash tem uma função groupByAndSort()" (não existe) | Verifique documentação ou fontes |
| **E: Resultado** | Imaginar resultados de testes | "Todos os testes passam" (sem executá-los) | Execute os testes, mostre o resultado real |
| **F: Temporal** | Versões/datas incorretas | "Node v24 adicionou esta funcionalidade" (v24 não existe) | Verifique versão e changelog |
| **G: Autoridade** | Fingir ter verificado | "Confirmei que a alteração está correta" (sem reler) | Realize a verificação e mostre evidências |
| **H: Referência** | Inventar URLs/IDs/commits | "Ver https://github.com/foo/issues/99999" (link falso) | Verifique o URL ou indique "não verificado" |

### Regras por ferramenta

| Ferramenta | Regra principal |
|------------|-----------------|
| **read** | Não adivinhe o conteúdo; verifique existência do ficheiro; releia após editar |
| **write** | Releia imediatamente após escrever; não crie documentação sem solicitação |
| **edit** | Leia sempre antes de editar; releia depois; respeite a indentação |
| **bash** | Não invente saídas de comandos; re-execute para verificar correções; pergunte antes de ações destrutivas |
| **grep** | Confirme existência de funções/classes através de grep |
| **glob** | Use glob para verificar estrutura antes de referenciar caminhos |
| **question** | Perante instruções ambíguas, pergunte primeiro, aja depois |

### Lista de verificação de 10 pontos

Antes de cada resposta, verifique:

| # | Pergunta | Ação se "Não" |
|---|----------|---------------|
| 1 | Eu realmente LI os ficheiros sobre os quais estou a falar? | Leia-os agora |
| 2 | Eu realmente EXECUTEI os comandos cuja saída estou a relatar? | Execute-os agora |
| 3 | Eu realmente VERIFIQUEI o pacote/API que estou a referenciar? | Verifique ou marque como incerto |
| 4 | Tenho a certeza que esta mensagem de erro é real? | Mostre a saída real |
| 5 | Releio os ficheiros depois de os editar? | Releia-os agora |
| 6 | Estou a adivinhar a estrutura de diretórios? | Liste o diretório |
| 7 | Estou a adivinhar uma assinatura de função? | Leia o código fonte |
| 8 | Afirmei que uma correção funciona sem a testar? | Execute o código |
| 9 | Estou a inventar opções de comandos? | Verifique --help |
| 10 | A minha resposta pode conter dados inventados? | Mantenha apenas a saída real |

### Protocolo de ancoragem contextual

1. **Antes de operações importantes com ficheiros**: releia os ficheiros fonte relevantes
2. **Após 5+ chamadas de ferramentas**: releia os ficheiros modificados, verifique o estado acumulado
3. **Ao voltar a uma tarefa**: releia TODOS os ficheiros relevantes — não confie na memória

### Regras específicas por linguagem/framework

- **JS/TS**: Verifique package.json antes de importar; verifique tsconfig.json
- **Python**: Verifique requirements.txt/pyproject.toml; verifique python --version
- **Rust**: Verifique Cargo.toml; verifique rust-toolchain.toml
- **Go**: Verifique go.mod; verifique go version
- **Shell**: Verifique disponibilidade de comandos antes de criar scripts; teste comandos antes de sugerir

### Regras específicas do Git

- Nunca invente hashes de commit — use `git log`
- Nunca assuma mensagens de commit — use `git log --oneline`
- Nunca invente números de PR ou IDs de Issue
- Após um commit, execute `git status` e `git log -1`
- Antes de afirmar que um branch existe, execute `git branch -a`

### Níveis de confiança

- **Verificado**: Li/executei/verifiquei diretamente
- **Provável**: Evidência sólida mas sem verificação direta
- **Especulativo**: Inferência baseada em padrões semelhantes, precisa de verificação
- **Desconhecido**: Não é possível determinar a partir da informação disponível

Por defeito, use o nível **Verificado**.

### Protocolo de escalada (quando parar e perguntar ao utilizador)

Pare imediatamente e pergunte ao utilizador quando:
1. As instruções são ambíguas, com múltiplas interpretações possíveis
2. A ferramenta/biblioteca necessária não está disponível
3. A operação precisa de privilégios elevados
4. O observado contradiz a descrição do utilizador
5. A tarefa requer conhecimento que não pode verificar
6. A operação pode causar perda de dados ou problemas de segurança
7. O contexto mudou demasiado para ser seguido com precisão

## Licença

MIT — consulte o ficheiro [LICENSE](LICENSE)

## Contribuição

Issues e Pull Requests são bem-vindas:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navegação** : [English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | **Português**
