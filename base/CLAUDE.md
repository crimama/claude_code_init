# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Principle

<!-- 프로젝트의 핵심 철학. Claude가 모든 판단의 기준으로 삼을 원칙. -->
<!-- 예: "사용자 경험이 최우선이다", "연구적 novelty가 핵심이다" 등 -->
**[여기에 프로젝트의 핵심 원칙을 작성하세요]**

## Project Summary

<!-- 프로젝트를 한 문단으로 설명. Claude가 맥락을 빠르게 파악하는 데 사용. -->

## Commands

```bash
# 프로젝트 빌드/실행/테스트 명령어
# Claude가 자동으로 사용할 수 있도록 정확한 명령어를 기재

# 예:
# python main.py --config configs/default.yaml
# npm run dev
# cargo test
```

## Architecture

### Pipeline / Data Flow

```
<!-- 프로젝트의 핵심 흐름을 간결하게 도식화 -->
Input → Processing → Output
```

### Key Modules

<!-- 주요 파일/모듈과 역할. Claude가 코드를 찾을 때 참조. -->
- `src/main.py` — 진입점
- `src/models/` — 모델 정의
- `src/utils/` — 유틸리티
- `configs/` — 설정 파일

### Conventions

<!-- 프로젝트 고유 규약 (import 방식, 네이밍, score 해석 등) -->
-

## Dependencies

<!-- 런타임 의존성 + 환경 특이사항 -->

## Update Notes

실험, 분석, 버그픽스, 아이디어 등 유의미한 작업 시 반드시 `update_notes/` 아래에 `.md` 파일로 기록한다. **단순 누적 금지** — 주제별 계층 디렉토리로 구성.

```
update_notes/
├── experiments/
│   ├── _TEMPLATE.md              # 실험/작업 보고서 템플릿
│   └── YYYY-MM-DD_작업명/
│       └── report.md
├── analysis/
│   └── 주제명/
│       ├── YYYY-MM-DD_설명.md
│       └── _lessons.md           # 검증된 패턴 축적 (승격된 교훈)
├── bugfix/
│   └── YYYY-MM-DD_설명.md
└── ideas/
    └── YYYY-MM-DD_설명.md
```

**필수 규칙:**
- 파일명: `YYYY-MM-DD_짧은_설명` 형식
- 분석/버그픽스/아이디어 노트 필수 섹션: **목적/배경**, **방법/설정**, **결과/관찰**, **다음 단계**
- 관련 작업 시작 전 `update_notes/` 내 기존 노트를 **반드시 먼저 확인**할 것

**스킬 그래프:**
- 노트 간 관련성이 있으면 `## 관련 노트` 섹션에 상대 경로로 링크
- 작업 흐름이 추적 가능하도록 연결 (실험 → 분석 → 아이디어 → 후속 실험)
- 반복되는 패턴이나 검증된 기법은 `analysis/{주제}/_lessons.md`로 승격
