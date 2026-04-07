---
name: ui-implementation
description: "UI 구현 스킬. UX·비주얼 스펙을 기존 프론트 패턴에 맞춰 코드로 옮긴다. Vue/React/기타, 반응형, a11y 속성, 상태별 UI. '화면 구현''컴포넌트 만들어''스펙 반영''피그마대로' 요청 시 사용. 스펙 없이 임의 디자인으로 구현하지 말고, 없으면 ux-experience / ui-visual-design 먼저 요청한다."
---

# UI Implementation

## 지시

1. **필수 입력** — `Read`로 확인: `_workspace/05_ux_flow.md`, `_workspace/10_visual_spec.md`. 없으면 사용자에게 스펙 생성 또는 경로 지정을 요청한다.
2. **코드베이스 정찰** — 라우팅, 레이아웃, 공통 컴포넌트, 스타일 유틸을 검색해 **동일 패턴**으로 맞춘다.
3. **구현** — 화면 ID·상태를 주석이나 문서에 링크할 수 있으면 링크한다. 하드코딩 색/간격은 토큰·테마가 있으면 치환한다.
4. **접근성** — 버튼/링크 시맨틱, `aria-label`/`aria-describedby`, 모달 포커스 트랩, 키보드 닫기 등 UX 스펙의 a11y 항목을 반영한다.
5. **기록** — `_workspace/30_impl_notes.md`에: 변경 파일 목록, 스펙과 다른 점(차이 + 이유/질문), 수동 테스트 체크리스트(3~7개).

## 산출물

- 소스 코드 변경
- `_workspace/30_impl_notes.md` (필수)

## 참고

- 역할: `.cursor/harness/agents/ui-frontend-builder.md`
