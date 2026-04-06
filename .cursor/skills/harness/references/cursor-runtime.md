# Cursor 런타임 매핑 (Claude Code 하네스 대비)

[revfactory/harness](https://github.com/revfactory/harness)는 Claude Code의 에이전트 팀 API(`TeamCreate`, `SendMessage`, `TaskCreate` 등)를 전제로 한다. Cursor에서는 **동일한 설계 의도**를 아래 방식으로 구현한다.

## 개념 대응표

| Claude Code (원본) | Cursor |
|-------------------|--------|
| `.claude/agents/*.md` | `.cursor/harness/agents/*.md` (역할·프로토콜 명세; Task 프롬프트의 근거) |
| `.claude/skills/` | 프로젝트 `.cursor/skills/` 또는 사용자 `~/.cursor/skills/` |
| `CLAUDE.md` | `AGENTS.md` (프로젝트 루트) + 필요 시 `.cursor/rules/*.mdc` |
| 에이전트 팀 (팀원 간 메시지) | **메인 에이전트 + 다중 `Task` 호출** + `_workspace/` **파일 프로토콜**로 정보 공유 |
| `Agent` / 팀원 스폰 | **`Task` 도구** (`subagent_type`: `generalPurpose`, `explore`, `shell` 등) |
| 커스텀 에이전트 타입 | 해당 이름의 마크다운 정의를 **Read 후 프롬프트에 주입**하거나, 오케스트레이터 스킬에 요약 포함 |
| `model: "opus"` | Task에 전달 가능한 `model`이 있으면 고난도 작업에 유리한 모델 선택, 없으면 프로젝트 기본 정책 |

## 협업 제약 (중요)

- Cursor의 서브에이전트(Task)는 **서로 직접 대화하지 않는다.**
- “팀 채팅”이 필요한 패턴은 **`_workspace/{역할}_{산출물}.md`**에 기록하고, **메인이 통합 단계에서 Read·병합**하는 방식으로 모델링한다.
- 팬아웃/팬인은 **동일 턴에서 `Task`를 여러 번 `run_in_background: true`로 호출**하는 패턴으로 구현한다.

## 스킬 트리거

- Cursor에서도 스킬의 `description`이 트리거에 핵심이다. 프로젝트 스킬은 `.cursor/skills/<name>/SKILL.md`에 둔다.
- `~/.cursor/skills-cursor/`는 Cursor 내장용이므로 **사용자 스킬은 `~/.cursor/skills/`** 에 둔다.

## 추가 참고

- Cursor 규칙(파일별·항상 적용): `.cursor/rules/*.mdc` — [Cursor Rules 문서](https://cursor.com/docs/context/rules)
- 원본 하네스 저장소: [github.com/revfactory/harness](https://github.com/revfactory/harness)
