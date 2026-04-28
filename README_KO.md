# OpenCode 스킬: AI 환각 방지

> 확인 강제, 불확실성 인정 및 증거 기반 응답을 통해 AI 환각을 방지하는 OpenCode 에이전트 스킬입니다.

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md) | 한국어

## 특징

- **확인 후 주장하라** — 파일을 읽고, 코드를 실행하고, 문서를 확인한 후 결론을 내리세요
- **불확실성을 인정하라** — 추측과 확인된 사실을 명확히 구분하세요
- **지어내지 마라** — 오류, API, 테스트 결과 또는 수정 사항을 절대 만들어내지 마세요
- **증거에 기반하라** — 파일 경로, 줄 번호 및 실제 출력을 인용하세요
- **자체 점검** — 각 응답 전 10포인트 체크리스트
- **코드 생성 보호 장치** — 코드 작성 전후 이중 확인
- **도구별 규칙** — read, write, edit, bash, grep, glob 등 각 도구에 대한 특정 환각 방지 규칙
- **8가지 환각 유형** — 각 유형마다 고유한 예방 전략

## 빠른 설치

### 방법 1: 저장소 클론 (권장)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

그런 다음 스킬을 OpenCode 전역 구성에 복사하세요:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### 방법 2: 수동 설치

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### 방법 3: 전역 규칙으로 (모든 세션에서 자동 로드 — 강력 권장)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> 전역 파일 `AGENTS.md`는 수동으로 스킬을 호출할 필요 없이 모든 세션에 자동으로 로드됩니다.

## 사용법

### 요청 시 (방법 1, 2)

OpenCode가 자동으로 스킬을 발견합니다. 에이전트가 필요할 때 로드합니다:

```javascript
skill({ name: "hallucination-prevention" })
```

### 자동 로드 (방법 3)

`AGENTS.md`의 규칙이 각 세션의 컨텍스트에 자동으로 주입됩니다. **수동 작업이 필요하지 않습니다.**

## 권한 구성

`opencode.json`에서:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

특정 에이전트의 경우:

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

권한은 와일드카드 패턴을 지원합니다:

| 권한 | 동작 |
|------|------|
| `allow` | 스킬이 즉시 로드됩니다 |
| `deny` | 스킬이 에이전트에게 숨겨지고 액세스가 거부됩니다 |
| `ask` | 로드 전에 확인을 요청합니다 |

## 저장소 구조

```
opencode-skill-hallucination-prevention/
├── README.md                                 # 영어 문서
├── README_CN.md                              # 중국어 간체 문서
├── README_TW.md                              # 중국어 번체 문서
├── README_KO.md                              # 한국어 문서 (이 파일)
├── README_HI.md                              # 힌디어 문서
├── README_UR.md                              # 우르두어 문서
├── README_TH.md                              # 태국어 문서
├── README_FA.md                              # 페르시아어 문서
├── README_TR.md                              # 터키어 문서
├── README_PL.md                              # 폴란드어 문서
├── README_NL.md                              # 네덜란드어 문서
├── README_SV.md                              # 스웨덴어 문서
├── README_EL.md                              # 그리스어 문서
├── README_RU.md                              # 러시아어 문서
├── README_FR.md                              # 프랑스어 문서
├── README_DE.md                              # 독일어 문서
├── README_IT.md                              # 이탈리아어 문서
├── README_ES.md                              # 스페인어 문서
├── README_CU.md                              # 쿠바 스페인어 문서
├── README_PT.md                              # 포르투갈어 문서
├── README_BR.md                              # 브라질 포르투갈어 문서
├── README_AR.md                              # 아랍어 문서
├── LICENSE                                   # MIT 라이선스
├── opencode.json                             # 구성 예시
├── AGENTS.md                                 # 전역 규칙 (자동 로드)
└── hallucination-prevention/
    └── SKILL.md                              # 스킬 정의 파일
```

## 스킬 v2.0 내용

### 8가지 환각 유형 분류

| 유형 | 설명 | 예시 | 예방 방법 |
|------|------|------|-----------|
| **A: 사실적** | 확인되지 않은 사실 주장 | "이 함수는 Promise를 반환합니다" (코드를 읽지 않고) | 소스를 읽고 코드를 실행하세요 |
| **B: 구조적** | 존재하지 않는 파일/경로 지어냄 | "핸들러는 src/routes/auth.ts에 있습니다" (파일이 없음) | glob/ls로 확인하세요 |
| **C: 행동적** | 코드 동작 추측 | "버그가 수정되었습니다" (프로젝트를 실행하지 않고) | 코드를 실행하고 결과를 보여주세요 |
| **D: 외부적** | API/함수 지어냄 | "Lodash에 groupByAndSort() 함수가 있습니다" (없음) | 문서나 소스를 확인하세요 |
| **E: 결과적** | 테스트 결과 상상 | "모든 테스트가 통과합니다" (실행하지 않고) | 테스트를 실행하고 실제 결과를 보여주세요 |
| **F: 시간적** | 잘못된 버전/날짜 | "Node v24가 이 기능을 추가했습니다" (v24 없음) | 버전과 체인지로그를 확인하세요 |
| **G: 권위적** | 확인한 척 가장 | "변경사항이 올바르다고 확인했습니다" (재읽지 않고) | 확인을 수행하고 증거를 보여주세요 |
| **H: 참조적** | URL/ID/커밋 지어냄 | "https://github.com/foo/issues/99999 참조" (가짜 링크) | URL을 확인하거나 "확인되지 않음"이라고 표시하세요 |

### 도구별 규칙

| 도구 | 핵심 규칙 |
|------|-----------|
| **read** | 내용을 추측하지 마세요; 파일 존재를 확인하세요; 편집 후 다시 읽으세요 |
| **write** | 작성 후 즉시 다시 읽으세요; 요청 없이 문서를 만들지 마세요 |
| **edit** | 편집 전에 항상 읽으세요; 이후 다시 읽으세요; 들여쓰기를 유지하세요 |
| **bash** | 명령 출력을 지어내지 마세요; 수정 확인을 위해 다시 실행하세요; 파괴적인 작업 전에 물어보세요 |
| **grep** | grep을 통해 함수/클래스 존재를 확인하세요 |
| **glob** | 경로를 참조하기 전에 glob을 사용하여 구조를 확인하세요 |
| **question** | 지침이 모호하면 먼저 물어보고 행동하세요 |

### 10포인트 체크리스트 ("말하기 전에 확인하세요")

각 응답 전에 확인:

| # | 질문 | "아니요"인 경우 조치 |
|---|------|---------------------|
| 1 | 내가 말하는 파일을 실제로 읽었습니까? | 지금 읽으세요 |
| 2 | 출력을 보고하는 명령을 실제로 실행했습니까? | 지금 실행하세요 |
| 3 | 참조하는 패키지/API를 실제로 확인했습니까? | 확인하거나 불확실하다고 표시하세요 |
| 4 | 이 오류 메시지가 실제라고 확신합니까? | 실제 출력을 보여주세요 |
| 5 | 편집 후 파일을 다시 읽었습니까? | 지금 다시 읽으세요 |
| 6 | 디렉토리 구조를 추측하고 있습니까? | 디렉토리를 나열하세요 |
| 7 | 함수 시그니처를 추측하고 있습니까? | 소스 코드를 읽으세요 |
| 8 | 테스트 없이 수정이 작동한다고 주장했습니까? | 코드를 실행하세요 |
| 9 | 명령 옵션을 지어내고 있습니까? | --help를 확인하세요 |
| 10 | 내 응답에 지어낸 데이터가 포함될 수 있습니까? | 실제 출력만 유지하세요 |

### 컨텍스트 고정 프로토콜

1. **중요한 파일 작업 전**: 관련 소스 파일을 다시 읽으세요
2. **5회 이상 도구 호출 후**: 수정된 파일을 다시 읽고 누적 상태를 확인하세요
3. **작업으로 돌아갈 때**: 모든 관련 파일을 다시 읽으세요 — 메모리에 의존하지 마세요

### 언어/프레임워크별 규칙

- **JS/TS**: import 전에 package.json 확인; tsconfig.json 확인
- **Python**: import 전에 requirements.txt/pyproject.toml 확인; python --version 확인
- **Rust**: Cargo.toml 확인; rust-toolchain.toml 확인
- **Go**: go.mod 확인; go version 확인
- **Shell**: 스크립트 작성 전 명령 가용성 확인; 제안 전 명령 테스트

### Git 특정 규칙

- 커밋 해시를 절대 지어내지 마세요 — `git log` 사용
- 커밋 메시지를 절대 추측하지 마세요 — `git log --oneline` 사용
- PR 번호나 Issue ID를 절대 지어내지 마세요
- 커밋 후 `git status` 및 `git log -1` 실행
- 브랜치 존재를 주장하기 전에 `git branch -a` 실행

### 신뢰 수준

- **확인됨**: 직접 읽었거나/실행했거나/확인했습니다
- **가능성 높음**: 강력한 증거가 있지만 직접 확인하지는 않았습니다
- **추측성**: 유사한 패턴에 기반한 추론, 확인 필요
- **알 수 없음**: 사용 가능한 정보로 판단할 수 없습니다

기본적으로 **확인됨** 수준을 사용하세요.

### 에스컬레이션 프로토콜 (언제 멈추고 사용자에게 물어볼 것)

다음과 같은 경우 즉시 중단하고 사용자에게 물어보세요:
1. 지침이 모호하고 여러 해석이 가능한 경우
2. 필요한 도구/라이브러리를 사용할 수 없는 경우
3. 작업에 높은 권한이 필요한 경우
4. 관찰한 내용이 사용자의 설명과 모순되는 경우
5. 작업에 확인할 수 없는 지식이 필요한 경우
6. 작업으로 인해 데이터 손실이나 보안 문제가 발생할 수 있는 경우
7. 컨텍스트가 정확히 추적하기에 너무 많이 변경된 경우

## 라이선스

MIT — [LICENSE](LICENSE) 파일 참조

## 기여

Issue 및 Pull Request를 환영합니다:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**탐색** : [English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md) | **한국어**
