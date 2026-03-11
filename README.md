# Claude Code Init

Claude Code 프로젝트를 위한 초기 설정 템플릿.
**CLAUDE.md** (프로젝트 지침) · **MEMORY.md** (영속적 메모리) · **tasks/** (세션 운영) · **skill_graph/** (지식 그래프)를 한 번에 세팅합니다.

## Quick Start

```bash
# 기존 프로젝트에 적용
git clone https://github.com/crimama/claude_code_init.git /tmp/claude_code_init
bash /tmp/claude_code_init/setup.sh research /path/to/your/project

# 새 프로젝트 시작
mkdir my-project && cd my-project && git init
bash /tmp/claude_code_init/setup.sh industry-academia .
```

## Presets

| Preset | 용도 | 특화 기능 | Slash Commands |
|--------|------|----------|----------------|
| `base` | 범용 (기본값) | CLAUDE.md + MEMORY.md + tasks/ + skill_graph/ + orchestrator | `/todo` `/lessons` `/update-note` `/link-notes` `/verify` `/checkpoint` `/compact` `/learn` `/orchestrate` |
| `dev` | 소프트웨어 개발 | 멀티에이전트 협업 (파일 잠금), 개발 중심 skill_graph, Memory Management | + `/feature` `/bugfix` `/lock-file` `/unlock-file` `/quality-gate` |
| `research` | ML/DL 연구 | 6단계 실험 프로세스, Claim-Evidence 규율, 강한 baseline/ablation, 재현성 추적, literature/idea 템플릿 | + `/experiment` `/analyze` |
| `industry-academia` | 산학과제 | 마일스톤 추적, 납품물 관리, 회의록, 기업 데이터 보안, Demo-ready | + `/experiment` `/meeting` `/deliverable` |

```bash
bash setup.sh base              # 범용
bash setup.sh dev               # 소프트웨어 개발
bash setup.sh research          # ML/DL 연구
bash setup.sh industry-academia # 산학과제
```

---

## System Architecture

4개 레이어로 구성된 지속 가능한 AI 협업 시스템입니다.

```
┌─────────────────────────────────────────────────────────┐
│  Layer 1: CLAUDE.md  (프로젝트 지침)                      │
│  ─ 아키텍처, 커맨드, 워크플로우 규칙, 코딩 원칙              │
│  ─ git tracked · 매 세션 시스템 프롬프트에 자동 주입          │
│  ─ 갱신 주기: 드물게 (구조 변경 시)                         │
├─────────────────────────────────────────────────────────┤
│  Layer 2: MEMORY.md  (영속적 메모리)                      │
│  ─ 실험 결과 요약, 의사결정, 현재 진행 상태                  │
│  ─ ~/.claude/projects/{path}/memory/ 에 위치              │
│  ─ 세션 간 자동 지속 · 시스템 프롬프트에 자동 로드 (200줄)   │
│  ─ 갱신 주기: 매 세션                                      │
├─────────────────────────────────────────────────────────┤
│  Layer 3: tasks/  (세션 운영)                              │
│  ─ todo.md : 현재 세션 계획 · 체크리스트 · 결과              │
│  ─ lessons.md : 수정·지적에서 추출한 누적 교훈               │
│  ─ git tracked · 세션 시작 시 반드시 먼저 확인               │
│  ─ 갱신 주기: 매 작업                                       │
├─────────────────────────────────────────────────────────┤
│  Layer 4: skill_graph/  (지식 그래프)                     │
│  ─ experiments/ · analysis/ · bugfix/ · ideas/ · papers/  │
│  ─ 노트 간 "## 관련 노트"로 DAG 형태 양방향 링크             │
│  ─ tasks/lessons.md의 검증된 패턴과 연구 노트가 승격·축적되는 목적지 │
│  ─ git tracked · 갱신 주기: 작업마다 (상세 기록 축적)        │
└─────────────────────────────────────────────────────────┘
```

### Layer별 역할 비교

|  | CLAUDE.md | MEMORY.md | tasks/ | skill_graph/ |
|--|-----------|-----------|--------|---------------|
| **내용** | 규칙·구조 | 상태·요약 | 계획·교훈 | 상세 기록 |
| **비유** | 헌법 | 작업 일지 | 스프린트 보드 | 연구 아카이브 |
| **갱신** | 드물게 | 매 세션 | 매 작업 | 매 작업 |
| **로드** | 전체 자동 | 200줄 자동 | 수동 참조 | 수동 참조 |

---

## Workflow Orchestration

모든 프리셋에 포함된 Claude Code 핵심 운영 규칙입니다.

| # | 원칙 | 요약 |
|---|------|------|
| 1 | **Plan Node Default** | 3단계 이상 작업은 plan mode 먼저. 막히면 STOP → 재계획 |
| 2 | **Subagent Strategy** | 리서치·탐색·병렬 분석은 서브에이전트에 오프로드 |
| 3 | **Self-Improvement Loop** | 수정/지적 → `tasks/lessons.md` 즉시 기록 → 검증 후 `_lessons.md` 승격 |
| 4 | **Verification Before Done** | 작동 증명 없이 완료 처리 금지. "시니어가 승인할 코드인가?" 자문 |
| 5 | **Demand Elegance** | hacky하면 재구현. 단순 수정엔 생략 — 과잉 설계 금지 |
| 6 | **Autonomous Bug Fixing** | 버그 리포트 → 직접 분석·해결. 손잡아달라 하지 말 것 |

---

## Agents

`agents/` 디렉토리에 YAML frontmatter 기반으로 정의된 전문 에이전트입니다. Subagent Strategy에 따라 서브에이전트로 호출하거나 참조 문서로 활용합니다.

### Common (모든 프리셋)

| Agent | Model | 용도 | 활성화 시점 |
|-------|-------|------|-----------|
| `planner` | opus | 구현 계획 수립, 단계 분해, 리스크 식별 | 3단계+ 작업, 아키텍처 결정 |
| `code-reviewer` | sonnet | 코드 품질/보안 리뷰 (CRITICAL→LOW 체크리스트) | 코드 변경 후 |

### Dev 전용 (추가)

| Agent | Model | 용도 | 활성화 시점 |
|-------|-------|------|-----------|
| `security-reviewer` | sonnet | OWASP Top 10 보안 검토, 시크릿 탐지 | 인증/입력/API 코드 변경 후 |
| `build-error-resolver` | sonnet | 빌드/타입 에러 최소 변경 해결 | 빌드 실패 시 |

---

## Context Modes

`contexts/` 디렉토리의 모드 파일로 세션 중 작업 포커스를 전환합니다.

| 모드 | 파일 | 포커스 | 핵심 행동 |
|------|------|--------|----------|
| **dev** | `contexts/dev.md` | 구현 집중 | 코드 먼저, 설명 후. 작동 → 올바르게 → 깔끔하게 |
| **research** | `contexts/research.md` | 탐색/조사 | 이해 먼저, 코드 후. 가설 → 증거 → 요약 |
| **review** | `contexts/review.md` | PR 리뷰 | 품질/보안 집중. 심각도순 정렬, 수정안 제시 |

**활성화**: "이 세션은 research 모드로 진행합니다" 또는 해당 파일 참조 요청.

---

## Hooks

`.claude/settings.local.json`에 설정된 자동 실행 훅입니다. `hooks/` 디렉토리의 셸 스크립트로 구현됩니다.

| 훅 | 이벤트 | 동작 |
|----|--------|------|
| `suggest-compact` | PreToolUse (Edit/Write) | 도구 호출 50회+ 시 전략적 `/compact` 제안. 논리적 전환점 판단 가이드 제공 |
| `git-push-reminder` | PreToolUse (Bash) | `git push` 감지 시 변경사항 리뷰 리마인더 |
| `lessons-reminder` | Stop | `tasks/lessons.md` 미갱신 시 교훈 기록 리마인더 |

### 환경변수

- `COMPACT_THRESHOLD` — compact 제안 임계치 (기본: 50회)

---

## Slash Commands (Skills)

`setup.sh` 실행 시 `.claude/skills/` 에 자동 설치되는 워크플로우 커맨드입니다.
Claude Code에서 `/커맨드명` 으로 바로 사용할 수 있습니다.

### Common (모든 프리셋)

| Command | 설명 |
|---------|------|
| `/todo` | `tasks/todo.md` 조회, 계획 작성, 체크, 완료 관리 |
| `/lessons` | `tasks/lessons.md` 조회, 교훈 추가, 검증된 패턴 승격 |
| `/update-note` | `skill_graph/` 템플릿 기반 새 노트 생성 (키워드 자동 연결 포함) |
| `/link-notes` | 키워드 기반 skill_graph/ 노트 자동 연결 |
| `/verify` | 빌드/타입/린트/테스트 종합 검증 (`quick` `full` `pre-commit` `pre-pr`) |
| `/checkpoint` | git 기반 작업 체크포인트 생성/검증/목록 관리 |
| `/compact` | 전략적 컨텍스트 컴팩션 판단 가이드 (언제 /compact할지 안내) |
| `/learn` | 세션 학습 — 패턴 관찰(`observe`), 교훈 추출(`extract`), 지식 승격(`promote`), 현황(`status`) |
| `/orchestrate` | 멀티에이전트 오케스트레이터 — 3개+ 독립 파일 작업을 Codex CLI 에이전트에 병렬 분배 |

### Dev 전용

| Command | 설명 |
|---------|------|
| `/feature` | 기능 개발 워크플로우 (노트 생성 + todo 연동 + plan 규칙) |
| `/bugfix` | 버그 수정 워크플로우 (근본 원인 분석 + 검증) |
| `/lock-file` | `.locks/` 파일 잠금 생성 (멀티에이전트 충돌 방지) |
| `/unlock-file` | `.locks/` 파일 잠금 해제 + 좀비 정리 |
| `/quality-gate` | 포맷/린트/타입 체크 통합 품질 게이트 (`--fix` `--strict`) |

### Research 전용

| Command | 설명 |
|---------|------|
| `/experiment` | 6단계 실험 프로세스 (가설 → 실행 → 교훈 승격, single-claim test 지향) |
| `/analyze` | 분석 노트 생성 + `_lessons.md` 승격 관리 + 실패 패턴 축적 |

### Industry-Academia 전용

| Command | 설명 |
|---------|------|
| `/experiment` | 6단계 실험 프로세스 (마일스톤/납품물 연결 포함) |
| `/meeting` | 회의록 생성 (참석자, 안건, Action Items) |
| `/deliverable` | 납품물 추적 (기한 경고 + 실험 결과 연결) |

---

## 지식 승격 흐름 (Promotion Flow)

```
사용자의 수정·지적 발생
        │ 즉시
        ▼
tasks/lessons.md          ← 세션 교훈 (빠른 기록, 항상 열려있는 파일)
        │ 반복 검증 후 (/learn promote)
        ▼
skill_graph/analysis/{주제}/_lessons.md   ← 검증된 프로젝트 지식 자산
        │ 핵심만 요약
        ▼
MEMORY.md (Lessons Learned 섹션)           ← 매 세션 자동 로드
```

### 실험 지식 승격

```
experiments/YYYY-MM-DD_실험명/report.md
        │ 반복 확인·검증 시
        ▼
analysis/{주제}/_lessons.md              ← "승격"
        │ 핵심 요약
        ▼
MEMORY.md (Key Experiment Results)       ← 매 세션 자동 로드
```

---

## Keyword Linking (노트 자동 연결)

`skill_graph/`의 노트들은 `## 관련 노트` 섹션을 통해 DAG 형태로 연결됩니다.
`/link-notes` 스킬과 `/update-note` 생성 시 자동 연결이 이를 지원합니다.

### 키워드 소스 (우선순위)

1. **명시적 키워드** — 노트 상단 `> **keywords**: PTY, session, resume` (쉼표 구분)
2. **헤딩 추출** — H1, H2 텍스트에서 stopword 제외 후 추출
3. **경로 토큰** — 카테고리명 + 파일명 하이픈 분리

### 매칭 규칙

- 두 노트의 키워드 교집합이 **2개 이상**이면 관련 노트로 판정
- 양방향 링크 필수 (A→B 추가 시 B→A도 추가)
- 기존 수동 링크(`선행:`, `후속:` 등)는 절대 삭제하지 않음
- `_TEMPLATE.md`는 스캔 대상에서 제외

### 사용법

```bash
/link-notes                    # skill_graph/ 전체 스캔 및 연결
/link-notes skill_graph/...   # 특정 파일만 대상으로 연결
/update-note features my-feat  # 노트 생성 시 자동으로 연결 시도
```

---

## 6단계 실험 프로세스 (Research / Industry-Academia)

```
1. 문제 분석 (Problem Analysis)  → 현상 + 원인 추정 + 관련 선행 노트
2. 가설 설정 (Hypothesis)        → "X하면 Y 개선" + 근거 + 예상 수치
3. 실험 설정 (Experiment Design) → 대조군/실험군 + config diff + 실행 커맨드
────────────── 실험 실행 ──────────────
4. 결과 (Results)                → 정량 결과표 + 로그 경로
5. 결과 분석 (Analysis)          → 가설 검증 ✅/❌/⚠️ + 부수 발견
6. 피드백 (Feedback)             → 교훈 + 다음 실험 제안 + _lessons.md 승격 여부
```

**규칙**: 실험 시작 전 1~3단계를 **반드시 먼저 작성**한 후 실행. 완료 후 4~6단계 기록.
- claim 하나당 직접 증거를 연결하고, 가능한 한 baseline 대비 단일 주장 검증(single-claim test)으로 쪼갭니다.
- 메인 결과 후보는 seed/반복 측정 여부를 명시하고, 재현성 항목(commit hash, config diff, split, log path)을 남깁니다.
- 실패 실험도 버리지 않고 `_lessons.md` 승격 후보로 관리합니다.

---

## 디렉토리 구조

### Base (모든 프리셋 공통)

```
your-project/
├── CLAUDE.md                     # 프로젝트 지침 + 워크플로우 규칙
├── MEMORY_TEMPLATE.md            # MEMORY.md 참조용 사본
├── .claude/
│   ├── settings.local.json       # 프로젝트별 자동 허용 명령어 + hooks 설정
│   └── skills/                   # Slash commands (8개)
│       ├── todo/SKILL.md         # /todo
│       ├── lessons/SKILL.md      # /lessons
│       ├── update-note/SKILL.md  # /update-note
│       ├── link-notes/SKILL.md   # /link-notes
│       ├── verify/SKILL.md       # /verify
│       ├── checkpoint/SKILL.md   # /checkpoint
│       ├── compact/SKILL.md      # /compact
│       ├── learn/SKILL.md        # /learn
│       └── orchestrate/SKILL.md # /orchestrate
├── orchestrator/                 # 멀티에이전트 오케스트레이션 모듈
│   ├── __init__.py
│   ├── __main__.py               # python -m orchestrator
│   ├── config.py                 # 환경변수 기반 설정
│   ├── session.py                # 세션 라이프사이클
│   ├── protocol.py               # Markdown 기반 통신 프로토콜
│   ├── agent.py                  # Codex 에이전트 프로세스 관리
│   ├── monitor.py                # 멀티에이전트 모니터링
│   ├── merge.py                  # Git 브랜치 머지
│   └── cli.py                    # CLI 엔트리포인트
├── AGENTS.md                     # Codex 에이전트 가이드
├── agents/                       # 에이전트 정의
│   ├── planner.md                # 계획 수립 전문가 (opus)
│   └── code-reviewer.md          # 코드 리뷰 전문가 (sonnet)
├── contexts/                     # 세션 모드 전환
│   ├── dev.md                    # 구현 집중 모드
│   ├── research.md               # 탐색/조사 모드
│   └── review.md                 # PR 리뷰 모드
├── hooks/                        # 자동 실행 훅 스크립트
│   ├── suggest-compact.sh        # 전략적 compact 제안
│   ├── git-push-reminder.sh      # git push 리뷰 리마인더
│   └── lessons-reminder.sh       # 교훈 기록 리마인더
├── tasks/
│   ├── todo.md                   # 세션 계획·체크리스트·결과
│   └── lessons.md                # 누적 교훈 (수정/지적 → 패턴 추출)
└── skill_graph/
    ├── experiments/
    │   └── _TEMPLATE.md          # 6단계 실험 보고서 템플릿
    ├── analysis/
    │   └── _LESSONS_TEMPLATE.md  # lessons.md 승격 목적지 템플릿
    ├── bugfix/
    ├── ideas/
    └── papers/
```

### Dev (추가)

```
your-project/
├── .claude/skills/               # + 5 dev skills
│   ├── feature/SKILL.md          # /feature
│   ├── bugfix/SKILL.md           # /bugfix
│   ├── lock-file/SKILL.md        # /lock-file
│   ├── unlock-file/SKILL.md      # /unlock-file
│   └── quality-gate/SKILL.md     # /quality-gate
├── agents/                       # + 2 dev agents
│   ├── security-reviewer.md      # OWASP Top 10 보안 검토 (sonnet)
│   └── build-error-resolver.md   # 빌드 에러 해결 (sonnet)
├── .locks/                       # 멀티에이전트 파일 잠금 디렉토리
└── skill_graph/
    ├── features/                 # 신규 기능 구현 기록
    ├── bugfix/                   # 버그 수정 (원인 분석 포함)
    ├── refactor/                 # 리팩토링 (Before/After)
    ├── devops/                   # 빌드/배포/인프라 변경
    ├── decisions/                # 아키텍처/기술 결정 (ADR)
    └── analysis/                 # 교훈 승격 목적지
```

### Research (추가)

```
your-project/
├── .claude/skills/               # + 2 research skills
│   ├── experiment/SKILL.md       # /experiment
│   └── analyze/SKILL.md          # /analyze
└── skill_graph/
    ├── experiments/_TEMPLATE.md  # 6단계 + config_diff + single-claim test
    ├── ideas/_TEMPLATE.md        # novelty screen + risk screen
    └── papers/_TEMPLATE.md       # related work / competitor / baseline 정리
```

### Industry-Academia (추가)

```
your-project/
├── .claude/skills/               # + 3 industry-academia skills
│   ├── experiment/SKILL.md       # /experiment (마일스톤 연결)
│   ├── meeting/SKILL.md          # /meeting
│   └── deliverable/SKILL.md      # /deliverable
├── data/
│   ├── public/        # 공개 데이터셋
│   └── proprietary/   # 기업 제공 데이터 (git 미추적, .gitignore 처리)
│       └── README.md  # 데이터 출처·사용 조건 기록
├── demo/              # 기업 발표·데모용
├── reports/           # 자동 생성 성능 리포트
└── skill_graph/
    ├── deliverables/  # 납품물 관련 기록
    └── meetings/      # 회의록
```

---

## Setup 후 할 일

1. **`CLAUDE.md` 편집** — `<!-- -->` 주석을 프로젝트에 맞게 채우기 (Project Summary, Architecture 등)
2. **`MEMORY.md` 편집** — `~/.claude/projects/{path}/memory/MEMORY.md` 초기 내용 작성
3. **`tasks/lessons.md`** — 세션 시작마다 먼저 확인
4. **Claude Code 시작** — 해당 디렉토리에서 `claude` 실행
5. **Slash commands 사용** — `/todo`, `/lessons`, `/verify`, `/learn` 등 설치된 커맨드 활용
6. **Context modes** — "research 모드로 진행" 또는 `contexts/*.md` 참조
7. **(선택) `settings.local.json` 편집** — 자주 쓰는 bash 명령 자동 허용 추가

## MEMORY.md 경로

Claude Code는 프로젝트 경로의 `/`를 `-`로 치환하여 메모리 디렉토리를 결정합니다:

```
~/.claude/projects/-{path-with-dashes}/memory/MEMORY.md
```

예: `/home/user/projects/my-research` → `~/.claude/projects/-home-user-projects-my-research/memory/MEMORY.md`

`setup.sh`가 이 경로를 자동으로 감지하여 MEMORY.md를 초기화합니다.

## Credits

Hooks, Agent definitions, Context Modes, Strategic Compact, Continuous Learning 개념은 [everything-claude-code](https://github.com/affaan-m/everything-claude-code)에서 영감을 받아 이 프로젝트의 4-layer 아키텍처에 맞게 적응·재구현했습니다.

## License

MIT
