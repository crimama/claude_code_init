# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Principle (프로젝트 원칙)

**산학과제는 "연구 성과"와 "실용적 결과물"을 동시에 달성해야 한다.** 학술 논문 투고와 기업 납품물(보고서/PoC/데모) 양쪽의 요구사항을 균형 있게 충족시키되, 기업이 요구하는 일정과 산출물 기준을 최우선으로 관리한다. 연구적 깊이는 납품 일정을 초과하지 않는 범위 내에서 추구한다.

## Project Summary

<!-- 과제 한 문단 요약 -->
<!-- 예: "비전 기반 제조 결함 검출 시스템. 삼성전기와의 산학과제로, 소수 정상 샘플로 학습하여 실시간 결함 검출을 수행하는 경량 모델을 개발한다." -->

## Stakeholders

<!-- 이해관계자 정리 -->
| 역할 | 이름/조직 | 주요 관심사 |
|------|----------|------------|
| 지도교수 | | 논문 성과, 연구 방향 |
| 기업 담당자 | | 납품물 품질, 일정 준수 |
| 연구원/학생 | | 구현, 실험 |

## Timeline & Milestones

<!-- 과제 일정 관리. Claude가 우선순위 판단에 활용. -->
| 마일스톤 | 기한 | 산출물 | 상태 |
|---------|------|--------|------|
| 1차 중간보고 | YYYY-MM-DD | 진행 보고서, 초기 결과 | 🔴 |
| 중간 납품 | YYYY-MM-DD | PoC 코드, 성능 리포트 | 🔴 |
| 최종 납품 | YYYY-MM-DD | 최종 모델, 보고서, 논문 초고 | 🔴 |
| 논문 투고 | YYYY-MM-DD | 학회/저널 제출 | 🔴 |

## Commands

```bash
# 실험 실행
# python main.py --config configs/default.yaml

# 데모 실행 (기업 발표용)
# python demo/app.py

# 성능 리포트 생성
# python scripts/generate_report.py --output reports/

# 테스트
# pytest tests/
```

## Architecture

### Pipeline Flow

```
<!-- 시스템 파이프라인 -->
Data Ingestion → Preprocessing → Model → Inference → Result/Report
```

### Key Modules

- `main.py` — 실험 진입점
- `models/` — 모델 정의
- `data_provider/` — 데이터 로더 (기업 데이터 포맷 대응)
- `utils/` — 유틸리티
- `configs/` — 실험 설정
- `demo/` — 기업 데모/발표용
- `reports/` — 자동 생성 리포트

### Conventions

-

## Data Layout

```
<!-- 데이터 구조. 기업 데이터는 별도 경로 명시. -->
data/
├── public/          # 공개 데이터셋 (MVTecAD 등)
└── proprietary/     # 기업 제공 데이터 (git 미추적)
    └── README.md    # 데이터 출처, 사용 조건 기록
```

**주의**: 기업 제공 데이터는 `.gitignore`에 반드시 포함. 외부 유출 금지.

## Dependencies

<!-- 런타임 의존성 -->

## Experiment Process

실험은 반드시 아래 **6단계 프로세스**를 따른다. 템플릿: `update_notes/experiments/_TEMPLATE.md`

```
1. 문제 분석 (Problem Analysis)  → 현상 + 원인 추정 + 관련 선행 노트
2. 가설 설정 (Hypothesis)        → "X하면 Y 개선" + 근거 + 예상 수치
3. 실험 설정 (Experiment Design) → 대조군/실험군 + config diff + 실행 커맨드
4. 결과 (Results)                → 정량 결과표 + 로그 경로
5. 결과 분석 (Analysis)          → 가설 검증 (✅/❌/⚠️) + 상세 분석 + 부수 발견
6. 피드백 (Feedback)             → 교훈 + 다음 실험 제안 + _lessons.md 승격 여부
```

**실험 진행 규칙:**
- 실험 시작 전 1~3단계 먼저 작성 후 실행
- 실험 완료 후 4~6단계 기록
- 검증된 패턴은 `analysis/{주제}/_lessons.md`로 승격
- `## 관련 노트`로 실험 간 연쇄 추적

## Update Notes

```
update_notes/
├── experiments/              # 실험 기록 (6단계)
│   ├── _TEMPLATE.md
│   └── YYYY-MM-DD_실험명/
├── analysis/                 # 분석 + 검증된 패턴
│   └── 주제명/
│       └── _lessons.md
├── bugfix/                   # 버그 수정 기록
├── ideas/                    # 연구 아이디어
├── deliverables/             # 📦 납품물 관련 기록
│   ├── YYYY-MM-DD_중간보고.md
│   └── YYYY-MM-DD_최종납품.md
└── meetings/                 # 📋 회의록
    └── YYYY-MM-DD_참석자_주제.md
```

**스킬 그래프:**
- 노트 간 `## 관련 노트`로 상대 경로 링크
- 실험 → 분석 → 아이디어 → 후속 실험 흐름 추적
- 납품물/회의록에서 관련 실험 노트 역링크

## Deliverable Rules (납품물 관리)

- 모든 납품물 관련 의사결정/변경사항은 `update_notes/deliverables/`에 기록
- 납품 보고서 작성 시 `update_notes/experiments/`의 결과를 참조·인용
- 기업 미팅 후 `update_notes/meetings/`에 회의록 작성 (액션아이템 포함)

## Meeting Notes Template

```markdown
# 회의록 — YYYY-MM-DD

## 참석자
-

## 안건
-

## 논의 내용
-

## 결정 사항
-

## Action Items
| 담당 | 내용 | 기한 |
|------|------|------|
| | | |

## 관련 노트
-
```

## Coding Rules

- **모듈화 필수**: config에서 on/off 가능하게 구현
- **Reproducibility**: seed 고정, config 기록
- **기업 데이터 보안**: proprietary 데이터 경로는 `.gitignore`에 포함, 하드코딩 금지
- **Demo-ready**: 주요 기능은 demo/ 에서 단독 실행 가능하게 유지

## Important Conventions

-
