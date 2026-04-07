---
name: uiux-orchestrator
description: "UI/UX end-to-end 오케스트레이터. 기획된 기능·화면을 UX 플로→비주얼 스펙→프론트 구현→QA까지 순차(또는 병렬)로 조율한다. 신규 화면·기능 전체, 디자인 시스템 도입, 대규모 UI 개편, 'UI UX 전체''화면 하나 끝까지''스펙부터 QA까지' 요청 시 반드시 이 스킬을 사용. 후속: 수정, 부분 재작업, 플로만/디자인만/구현만 다시, 스펙 갱신, QA 재실행, '이전 결과 개선'도 포함."
---

# UI/UX Orchestrator

## 실행 모드

- **기본: 순차 Task** — UX → Visual → Implementation → Review (의존성 강함)
- **병렬 가능 구간** — 동일 화면 세트에 대해 **비주얼 토큰 정리**와 **UX 에지 케이스 확장**을 나눌 때만 메인이 두 Task를 병렬로 스폰하고 `_workspace/`로 합친다. 일반적으로는 순차를 권장한다.

## Phase 0: 컨텍스트

1. `_workspace/` 존재 여부 확인
2. 분기:
   - 없음 → 초기: `00_input` 준비 후 Phase 1
   - 있음 + **부분 수정** 요청 → 해당 단계 스킬만 재실행 (예: 구현만 → `ui-implementation` + 기존 스펙 Read)
   - 있음 + **새 기능** → `_workspace`를 `_workspace_{YYYYMMDD_HHMMSS}/`로 이름 변경 후 초기화

## Phase 1: 준비

- 사용자 목표·범위·프레임워크·디자인 파일 링크를 `_workspace/00_input/brief.md`에 정리

## Phase 2: UX

- `ux-experience` 스킬 지침 실행 → `05_ux_flow.md`

## Phase 3: Visual

- `ui-visual-design` 스킬 지침 실행 → `10_visual_spec.md`

## Phase 4: Implementation

- `ui-implementation` 스킬 지침 실행 → 코드 + `30_impl_notes.md`

## Phase 5: Review

- `ui-review` 스킬 지침 실행 → `40_ui_qa_report.md`

## Phase 6: 정리

- 사용자에게 P0 이슈 요약, 다음 액션(머지/수정/스펙 변경) 제안
- `_workspace/` 보존

## Task 위임 시

메인은 각 단계에서 해당 역할 파일을 프롬프트에 포함한다:

- UX: `.cursor/harness/agents/ux-architect.md`
- Visual: `.cursor/harness/agents/ui-visual-designer.md`
- 구현: `.cursor/harness/agents/ui-frontend-builder.md`
- QA: `.cursor/harness/agents/ui-review-qa.md`

## 데이터 흐름

```
00_input → 05_ux_flow → 10_visual_spec → (코드) + 30_impl_notes → 40_ui_qa_report
```

## 에러 핸들링

- 한 단계 실패 시 다음 단계로 진행하지 말고, 실패 원인을 `_workspace/_briefing.md`에 기록 후 사용자 확인

## 테스트 시나리오

- **정상:** 짧은 brief → 05 → 10 → 구현 → QA 리포트에 P0 없음
- **에러:** 구현 단계에서 스펙 불일치 발견 → 30에 기록, Phase 3 재호출 또는 사용자 결정 대기
