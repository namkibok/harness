---
name: ui-review
description: "UI/UX 검증 스킬. UX·비주얼 스펙 대비 구현 교차 검토, 반응형·a11y 스모크, 빌드/린트. 출시 전 점검, 'UI 리뷰''QA''스펙이랑 다른 부분''접근성 검사' 요청 시 사용. 리포트는 _workspace/40_ui_qa_report.md. 수정은 ui-implementation 또는 스펙 역할에 위임한다."
---

# UI Review

## 지시

1. **기준서** — `05_ux_flow.md`, `10_visual_spec.md`, `30_impl_notes.md`를 Read한다.
2. **정적 검토** — 화면 ID별로 스펙 항목을 체크리스트로 두고 통과/실패/스펙결핍을 구분한다.
3. **a11y 스모크** — 키보드만으로 핵심 플로 1회, 포커스 가시성, 폼 에러 연계.
4. **실행** — 프로젝트에 스크립트가 있으면 `npm run lint`, `npm run build`(또는 팀 표준)을 실행하고 실패는 이슈로 기록한다. 실행 불가면 환경 한계를 보고서 상단에 적는다.
5. **보고** — `_workspace/40_ui_qa_report.md`에 요약, P0/P1 표, 재현 단계, 담당 제안(구현 vs 디자인 vs 기획).

## 산출물

- `_workspace/40_ui_qa_report.md` (필수)

## 참고

- 역할: `.cursor/harness/agents/ui-review-qa.md`
- Task 타입: 검증에 실행이 필요하면 `generalPurpose` 권장
