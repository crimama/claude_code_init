# Claude Code Init

Claude Code 프로젝트를 위한 초기 설정 템플릿.
**CLAUDE.md** (프로젝트 지침) · **MEMORY.md** (영속적 메모리) · **tasks/** (세션 운영) · **update_notes/** (지식 그래프)를 한 번에 세팅합니다.

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
| `base` | 범용 (기본값) | CLAUDE.md + MEMORY.md + tasks/ + update_notes/ | `/todo` `/lessons` `/update-note` `/link-notes` |
| `dev` | 소프트웨어 개발 | 멀티에이전트 협업 (파일 잠금), 개발 중심 update_notes, Memory Management | + `/feature` `/bugfix` `/lock-file` `/unlock-file` |
| `research` | ML/DL 연구 | 6단계 실험 프로세스, Config 태그 ([TUNE]/[ARCH]), Score Convention | + `/experiment` `/analyze` |
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
│  Layer 4: update_notes/  (지식 그래프)                     │
│  ─ experiments/ · analysis/ · bugfix/ · ideas/            │
│  ─ 노트 간 "## 관련 노트"로 DAG 형태 양방향 링크             │
│  ─ tasks/lessons.md의 검증된 패턴이 승격되는 목적지          │
│  ─ git tracked · 갱신 주기: 작업마다 (상세 기록 축적)        │
└─────────────────────────────────────────────────────────┘
```

### Layer별 역할 비교

|  | CLAUDE.md | MEMORY.md | tasks/ | update_notes/ |
|--|-----------|-----------|--------|---------------|
| **내용** | 규칙·구조 | 상태·요약 | 계획·교훈 | 상세 기록 |
| **비유** | 헌법 | 작업 일지 | 스프린트 보드 | 논문 아카이브 |
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

## Slash Commands (Skills)

`setup.sh` 실행 시 `.claude/skills/` 에 자동 설치되는 워크플로우 커맨드입니다.
Claude Code에서 `/커맨드명` 으로 바로 사용할 수 있습니다.

### Common (모든 프리셋)

| Command | 설명 |
|---------|------|
| `/todo` | `tasks/todo.md` 조회, 계획 작성, 체크, 완료 관리 |
| `/lessons` | `tasks/lessons.md` 조회, 교훈 추가, 검증된 패턴 승격 |
| `/update-note` | `update_notes/` 템플릿 기반 새 노트 생성 (키워드 자동 연결 포함) |
| `/link-notes` | 키워드 기반 update_notes/ 노트 자동 연결 |

### Dev 전용

| Command | 설명 |
|---------|------|
| `/feature` | 기능 개발 워크플로우 (노트 생성 + todo 연동 + plan 규칙) |
| `/bugfix` | 버그 수정 워크플로우 (근본 원인 분석 + 검증) |
| `/lock-file` | `.locks/` 파일 잠금 생성 (멀티에이전트 충돌 방지) |
| `/unlock-file` | `.locks/` 파일 잠금 해제 + 좀비 정리 |

### Research 전용

| Command | 설명 |
|---------|------|
| `/experiment` | 6단계 실험 프로세스 (가설 → 실행 → 교훈 승격) |
| `/analyze` | 분석 노트 생성 + `_lessons.md` 승격 관리 |

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
        │ 반복 검증 후
        ▼
update_notes/analysis/{주제}/_lessons.md   ← 검증된 프로젝트 지식 자산
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

`update_notes/`의 노트들은 `## 관련 노트` 섹션을 통해 DAG 형태로 연결됩니다.
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
/link-notes                    # update_notes/ 전체 스캔 및 연결
/link-notes update_notes/...   # 특정 파일만 대상으로 연결
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

---

## 디렉토리 구조

### Base (모든 프리셋 공통)

```
your-project/
├── CLAUDE.md                     # 프로젝트 지침 + 워크플로우 규칙
├── MEMORY_TEMPLATE.md            # MEMORY.md 참조용 사본
├── .claude/
│   ├── settings.local.json       # 프로젝트별 자동 허용 명령어
│   └── skills/                   # Slash commands
│       ├── update-note/SKILL.md  # /update-note
│       ├── link-notes/SKILL.md   # /link-notes
│       ├── lessons/SKILL.md      # /lessons
│       └── todo/SKILL.md         # /todo
├── tasks/
│   ├── todo.md                   # 세션 계획·체크리스트·결과
│   └── lessons.md                # 누적 교훈 (수정/지적 → 패턴 추출)
└── update_notes/
    ├── experiments/
    │   └── _TEMPLATE.md          # 6단계 실험 보고서 템플릿
    ├── analysis/
    │   └── _LESSONS_TEMPLATE.md  # lessons.md 승격 목적지 템플릿
    ├── bugfix/
    └── ideas/
```

### Dev (추가)

```
your-project/
├── .claude/skills/               # + 4 dev skills
│   ├── feature/SKILL.md          # /feature
│   ├── bugfix/SKILL.md           # /bugfix
│   ├── lock-file/SKILL.md        # /lock-file
│   └── unlock-file/SKILL.md      # /unlock-file
├── .locks/                       # 멀티에이전트 파일 잠금 디렉토리
└── update_notes/
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
└── update_notes/
    └── experiments/_TEMPLATE.md  # 6단계 + config_diff + Score Convention
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
└── update_notes/
    ├── deliverables/  # 납품물 관련 기록
    └── meetings/      # 회의록
```

---

## Setup 후 할 일

1. **`CLAUDE.md` 편집** — `<!-- -->` 주석을 프로젝트에 맞게 채우기 (Project Summary, Architecture 등)
2. **`MEMORY.md` 편집** — `~/.claude/projects/{path}/memory/MEMORY.md` 초기 내용 작성
3. **`tasks/lessons.md`** — 세션 시작마다 먼저 확인
4. **Claude Code 시작** — 해당 디렉토리에서 `claude` 실행
5. **Slash commands 사용** — `/todo`, `/lessons`, `/update-note` 등 설치된 커맨드 활용
6. **(선택) `settings.local.json` 편집** — 자주 쓰는 bash 명령 자동 허용 추가

## MEMORY.md 경로

Claude Code는 프로젝트 경로의 `/`를 `-`로 치환하여 메모리 디렉토리를 결정합니다:

```
~/.claude/projects/-{path-with-dashes}/memory/MEMORY.md
```

예: `/home/user/projects/my-research` → `~/.claude/projects/-home-user-projects-my-research/memory/MEMORY.md`

`setup.sh`가 이 경로를 자동으로 감지하여 MEMORY.md를 초기화합니다.

## License

MIT
