---
name: ui-visual-design
description: "UI 비주얼·디자인 시스템 스킬. 디자인 토큰, 타이포·컬러·간격, 컴포넌트 상태 스펙, 반응형 브레이크포인트, Figma/이미지 레퍼런스 정리. 화면 시각 설계, 컴포넌트 스펙 작성, 디자인 토큰 정의, 다크모드·테마, '디자인 시스템''스타일 가이드''UI 스펙' 요청 시 반드시 사용. 순수 플로만은 ux-experience 스킬."
---

# UI Visual Design

## 지시

1. **컨텍스트** — `_workspace/05_ux_flow.md`가 있으면 먼저 Read한다. 없으면 입력에서 화면·상태 목록을 추론해 메인에 확인한다.
2. **기존 시스템** — 코드베이스에서 테마·토큰·UI 라이브러리(예: CSS 변수, Tailwind preset, 디자인 토큰 JSON)를 찾아 **확장 우선**한다.
3. **산출물** — `_workspace/10_visual_spec.md`에 작성:
   - 토큰 표(이름, 값, 용도)
   - 컴포넌트별: 변형(variant), 크기, 상태, 간격, 아이콘 슬롯
   - 브레이크포인트별 레이아웃 노트
   - **대비**: 텍스트/배경 조합에 대한 목표(가능하면 비율 명시)
4. **문서 스타일** — 스펙 본문에서 사용자에게 보이는 UI 라벨은 **굵게** 표기한다 (예: **로그인**). 코드·토큰 값은 인라인 코드 또는 코드 블록만 사용한다.
5. 에셋이 필요하면 `_workspace/10_assets_notes.md`에 파일명·포맷·해상도를 적는다.

## 산출물

- `_workspace/10_visual_spec.md` (필수)
- `_workspace/10_assets_notes.md` (선택)

## 참고

- 역할: `.cursor/harness/agents/ui-visual-designer.md`
- Harness 문서 스타일 가이드(UI 라벨 강조 등): [HDH Style Guide](https://developer.harness.io/docs/hdh/style-guide)
