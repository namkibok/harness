# 프로젝트 에이전트 / 스킬 맥락

이 저장소는 Cursor용 **Harness 메타 스킬**과 **UI/UX 개발 하네스**를 포함한다.

## 하네스: UI/UX 개발

**목표:** UX 플로 → 비주얼 스펙 → 구현 → QA까지 `_workspace/` 파일 프로토콜로 재현 가능하게 조율한다.

**역할 (에이전트 명세):**

| 파일 | 역할 |
|------|------|
| `.cursor/harness/agents/ux-architect.md` | 플로·IA·a11y 요구·카피 톤 |
| `.cursor/harness/agents/ui-visual-designer.md` | 토큰·컴포넌트 비주얼 스펙 |
| `.cursor/harness/agents/ui-frontend-builder.md` | 스펙 → 코드 |
| `.cursor/harness/agents/ui-review-qa.md` | 스펙 대비 검증·리포트 |

**스킬:**

| 스킬 | 용도 | 사용 단계 |
|------|------|-----------|
| `ux-experience` | UX 플로 문서화 | 단독 또는 오케스트레이터 2단계 |
| `ui-visual-design` | 비주얼·토큰 스펙 | 단독 또는 오케스트레이터 3단계 |
| `ui-implementation` | UI 구현 | 단독 또는 오케스트레이터 4단계 |
| `ui-review` | QA 리포트 | 단독 또는 오케스트레이터 5단계 |
| `uiux-orchestrator` | 전체 파이프라인 조율 | 엔드투엔드 요청 시 |

**실행 규칙:**

- 화면·기능을 **처음부터 끝까지** 다룰 때는 `uiux-orchestrator` 스킬을 기준으로 한다.
- 단계만 다시 할 때는 해당 단일 스킬만 사용한다.
- 중간 산출물은 프로젝트 루트 근처 **`_workspace/`** (또는 팀이 정한 경로)에 둔다.

**워크스페이스 파일 흐름:**

```
_workspace/00_input/     → brief
_workspace/05_ux_flow.md
_workspace/10_visual_spec.md
_workspace/30_impl_notes.md
_workspace/40_ui_qa_report.md
```

---

## 하네스: 메타 (역할·스킬 설계)

| 스킬 | 용도 |
|------|------|
| `harness` | 도메인별 하네스 신규 구축·감사·확장 |

경로: `.cursor/skills/harness/SKILL.md`

---

## 변경 이력

| 날짜 | 변경 내용 | 대상 | 사유 |
|------|-----------|------|------|
| 2026-04-07 | UI/UX 하네스 역할·스킬·오케스트레이터 추가 | agents/, skills/ | UI/UX 개발 스킬 구조 |
