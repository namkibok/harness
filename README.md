# harness (Cursor)

[revfactory/harness](https://github.com/revfactory/harness)의 하네스 개념을 **Cursor**용으로 옮긴 프로젝트 메타 스킬과 참조 문서입니다.

## 구성

- `.cursor/rules/*.mdc` — Cursor **프로젝트 규칙**(원격/팀 규칙 동기화 시에도 이 경로·확장자 사용)
- `.cursor/skills/harness/` — 메타 스킬 `SKILL.md` 및 `references/`
- `.cursor/skills/ux-experience`, `ui-visual-design`, `ui-implementation`, `ui-review`, `uiux-orchestrator` — **UI/UX 개발** 절차 스킬
- `.cursor/harness/agents/` — 역할 명세 (`ux-architect`, `ui-visual-designer`, `ui-frontend-builder`, `ui-review-qa` 등)
- `AGENTS.md` — 이 레포의 역할·스킬·`_workspace/` 흐름 요약

## 사용

프로젝트에 복사한 뒤 Agent에게 **「하네스 구성해줘」** 등으로 요청하거나, `.cursor/skills/`가 로드되는 환경에서 스킬 설명에 맞는 작업 시 자동 적용됩니다.

**UI/UX 전체 파이프라인:** `uiux-orchestrator` 스킬 트리거(또는 `AGENTS.md` 참고). 단계만 필요하면 `ux-experience` / `ui-visual-design` / `ui-implementation` / `ui-review` 중 선택.

자세한 매핑: `.cursor/skills/harness/references/cursor-runtime.md`

## 라이선스

원본 하네스는 Apache 2.0입니다. 이 저장소의 파생/번역·적용 부분은 동일 정신을 따릅니다.

## cursor Agent Settings 추가 사항
docs = https://developer.harness.io/docs/
