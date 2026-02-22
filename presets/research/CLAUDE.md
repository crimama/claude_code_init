# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Research Principle (연구 철칙)

**단순한 성능 향상이 목표가 아니다.** 연구로서 가치가 있으며 동시에 성능을 향상시킬 수 있는 **novelty 있는 방법론을 제안**하는 것이 최종 목표다. 모든 실험과 방법론 설계는 "왜 이 방법이 학술적으로 의미가 있는가?"를 먼저 답할 수 있어야 한다. Trick 나열이 아닌, 명확한 insight와 원리에 기반한 방법론을 추구한다.

## Project Summary

<!-- 연구 프로젝트 한 문단 요약 -->
<!-- 예: "Few-shot Anomaly Detection framework that extends PatchCore with Test-Time Prototype Expansion." -->

## Commands

```bash
# 실험 실행 (PYTHONPATH 설정 필요 시)
# PYTHONPATH=$(pwd):$PYTHONPATH python main.py --config configs/default.yaml

# Config override (OmegaConf 등 사용 시)
# python main.py --config configs/default.yaml DATASET.shot=4 MODEL.backbone=resnet50

# Baseline 실행
# python main.py EXPANSION.use=false

# 전체 실험 스위트
# bash scripts/run_experiments.sh

# Pilot mode (wandb off)
# PILOT=true bash scripts/run_experiments.sh
```

No test suite exists. Validation is done by running experiments and checking metrics.

## Architecture

### Pipeline Flow

```
<!-- 연구 파이프라인 도식화 -->
Training: Data → Feature Extraction → Model Fitting
Testing:  Data → Features → [Core Method] → Scoring → Metrics
```

### Key Modules

<!-- 주요 모듈과 역할 -->
- `main.py` — 진입점, 실험 오케스트레이션
- `models/` — 모델 정의
- `utils/` — 유틸리티 (metrics, visualization 등)
- `configs/` — 실험 설정 (YAML)
- `data_provider/` — 데이터 로더

### Score Convention

<!-- 점수 해석 규약 명시 (혼동 방지) -->
<!-- 예: "Lower score = more normal" 또는 "Higher score = anomaly" -->

### Registry / Extension Pattern

<!-- 새 모듈 추가 시 따라야 할 패턴 -->
<!-- 예: "@register('name') decorator + @dataclass" -->

## Data Layout

```
<!-- 데이터 디렉토리 구조 -->
{data_path}/{dataset_name}/{category}/train/
{data_path}/{dataset_name}/{category}/test/
```

## Dependencies

<!-- 런타임 의존성 -->
<!-- 미설치/대체된 패키지도 명시 -->

## Config Parameter Tags

<!-- Config 주석에 사용하는 태그 -->
`[TUNE]` = 자주 튜닝하는 하이퍼파라미터
`[ARCH]` = 아키텍처 선택 (덜 변경)
`[DEPRECATED]` = 호환성 유지용 (미사용)

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
- 실험 시작 전 `report.md`에 1~3단계(문제분석/가설/설정)를 **먼저 작성** 후 실행
- 실험 완료 후 4~6단계(결과/분석/피드백)를 기록
- 가설 검증 결과가 반복 활용 가능하면 `analysis/{주제}/_lessons.md`로 승격
- 후속 실험은 `## 관련 노트`에서 선행 실험 report.md를 링크하여 연쇄 추적

**실험 디렉토리 구조:**
```
update_notes/experiments/YYYY-MM-DD_실험명/
├── report.md            # 6단계 프로세스 전체 기록
├── config_diff.yaml     # baseline 대비 변경된 config
└── logs/                # 실험 로그
```

## Update Notes

실험, 분석, 버그픽스, 아이디어 등 유의미한 작업 시 반드시 `update_notes/` 아래에 `.md` 파일로 기록한다. **단순 누적 금지** — 주제별 계층 디렉토리로 구성.

```
update_notes/
├── experiments/
│   ├── _TEMPLATE.md              # 실험 보고서 템플릿 (6단계 프로세스)
│   └── YYYY-MM-DD_실험명/
│       ├── report.md
│       ├── config_diff.yaml
│       └── logs/
├── analysis/
│   └── 주제명/
│       ├── YYYY-MM-DD_설명.md
│       └── _lessons.md           # 검증된 패턴 축적 (승격된 교훈)
├── bugfix/
│   └── YYYY-MM-DD_설명.md
└── ideas/
    └── YYYY-MM-DD_설명.md
```

**스킬 그래프:**
- 노트 간 관련성이 있으면 `## 관련 노트` 섹션에 상대 경로로 링크
- 실험 → 분석 → 아이디어 → 후속 실험 흐름이 추적 가능하도록 연결
- 반복되는 패턴이나 검증된 기법은 `analysis/{주제}/_lessons.md`로 승격하여 축적
- 새 세션에서는 이 노트들을 persistent memory + skill context로 참조

## Coding Rules

- **모듈화 필수**: 새롭게 추가하는 모든 모듈/기능은 반드시 config에서 `enable: true/false`로 on/off 가능하게 구현한다. 과거 실험을 config만으로 정확히 재현할 수 있어야 한다.
- **Reproducibility**: seed 고정, config 기록, 환경 명시
- **Ablation-friendly**: 각 컴포넌트를 독립적으로 on/off 가능하게 설계

## Important Conventions

<!-- 프로젝트 고유 규약 -->
-
