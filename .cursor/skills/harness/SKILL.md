---
name: harness
description: "하네스를 구성합니다. 전문 역할(에이전트) 명세와 이를 실행할 스킬을 설계·생성하는 메타 스킬(Cursor용). (1) '하네스 구성해줘', '하네스 구축해줘' (2) '하네스 설계', '에이전트 팀' (3) 도메인별 자동화 체계 신규 구축 (4) 하네스 확장/재구성 (5) '하네스 점검', '에이전트/스킬 동기화', 'AGENTS.md 갱신' 등 유지보수 요청 시 사용."
---

# Harness (Cursor) — 에이전트 역할 & 스킬 설계자

도메인/프로젝트에 맞는 **역할 분리·스킬 세트·오케스트레이션**을 설계하고, Cursor에서 재사용 가능한 파일로 남기는 메타 스킬.

**원본:** [revfactory/harness](https://github.com/revfactory/harness) (Claude Code 플러그인)를 Cursor 워크플로에 맞게 이식.

**핵심 원칙 (Cursor):**
1. 역할 명세는 **`.cursor/harness/agents/*.md`**, 절차·도구 가이드는 **`.cursor/skills/<이름>/SKILL.md`** 에 둔다.
2. Claude Code의 **에이전트 팀 API는 없다.** 협업은 **`Task` 도구(서브에이전트) + `_workspace/` 파일 프로토콜**로 구현한다. 상세: `references/cursor-runtime.md`.
3. **`AGENTS.md`에 하네스 요약을 등록한다** — 새 세션에서도 “이 프로젝트에 어떤 역할/스킬이 있는지”가 로드되도록 한다.
4. 하네스는 **진화하는 시스템**이다. 실행 후 피드백을 반영해 역할·스킬·`AGENTS.md`를 갱신한다.

## 워크플로우

### Phase 0: 현황 감사

1. `프로젝트/.cursor/harness/agents/`, `프로젝트/.cursor/skills/`, `프로젝트/AGENTS.md`를 읽는다.
2. 분기:
   - **신규 구축**: 역할/스킬 디렉터리가 없거나 비어 있음 → Phase 1부터 전체 실행
   - **기존 확장**: 일부 추가/변경 → 아래 매트릭스에 따라 필요한 Phase만 실행
   - **운영/유지보수**: 감사·동기화 → Phase 7-5

**기존 확장 시 Phase 선택 매트릭스**

| 변경 유형 | P1 | P2 | P3 | P4 | P5 | P6 |
|-----------|----|----|----|----|----|-----|
| 역할(에이전트) 추가 | 건너뜀 | 배치만 | 필수 | 필요 시 | 오케스트레이터 수정 | 필수 |
| 스킬 추가/수정 | 건너뜀 | 건너뜀 | 건너뜀 | 필수 | 연결 변경 시 | 필수 |
| 아키텍처 변경 | 건너뜀 | 필수 | 영향 범위만 | 영향 범위만 | 필수 | 필수 |

3. 파일 시스템의 목록과 `AGENTS.md`를 대조해 **drift**를 기록한다.
4. 감사 요약과 실행 계획을 사용자와 맞춘다.

### Phase 1: 도메인 분석

1. 요청에서 도메인·목표 파악
2. 작업 유형 식별 (생성/검증/분석/배포 등)
3. 기존 역할·스킬과의 중복·충돌 분석
4. 코드베이스 탐색 (스택, 모듈)
5. **사용자 숙련도**에 맞춘 설명 깊이 조절

### Phase 2: 팀 아키텍처 설계

#### 2-1. 실행 모드: 멀티 Task vs 순차 Task

- **멀티 Task 병렬:** 독립 관점이 여럿일 때 — 동일 턴에서 `Task`를 여러 번(`run_in_background: true`), 산출물은 `_workspace/`에 고정 경로로 저장, 메인이 통합.
- **순차 Task:** 강한 단계 의존 — 이전 산출물 경로를 다음 Task 프롬프트에 명시.

> 의사결정·패턴 표는 `references/agent-design-patterns.md` (Cursor 실행 모드 섹션).

#### 2-2. 아키텍처 패턴

파이프라인, 팬아웃/팬인, 전문가 풀, 생성-검증, 감독자, 계층 위임 — 정의는 동일하되 **구현은 항상 메인 오케스트레이션 + Task + 파일**이다.

### Phase 3: 역할(에이전트) 명세 생성

**모든 전문 역할은 `프로젝트/.cursor/harness/agents/{name}.md`에 문서화한다.**

- Task 호출 시 이 파일을 **Read하여 프롬프트에 핵심을 주입**하거나, 오케스트레이터 스킬에 “반드시 이 파일을 따를 것”을 명시한다.
- 프롬프트에만 역할을 넣고 파일을 남기지 않는 것은 지양한다 (재현성·감사).

**모델:** Task 도구에 `model`을 줄 수 있으면 **고난도 위임에 유리한 모델**을 지정한다. 불가하면 프로젝트 기본을 따른다.

**팀 재구성:** Phase마다 다른 전문가 조합이 필요하면, 이전 `_workspace/` 산출물을 보존한 뒤 다음 Phase용 Task 세트를 새로 정의한다.

필수 섹션 예: 핵심 역할, 작업 원칙, 입출력 프로토콜, 에러 핸들링, 협업, **(멀티 Task 시) `_workspace/` 파일 규칙 및 메인 중재 시 기대 동작**.

> 템플릿: `references/agent-design-patterns.md`의 에이전트 정의 구조 + `references/team-examples.md` (Cursor 적용 주석 참고).

**AGENTS.md 임시 동기화 (Phase 3 완료 시):** 역할 목록 테이블 갱신. Phase 5-4에서 최종 정리.

**QA 역할 포함 시:** 실행·검증에 터미널/스크립트가 필요하면 `explore`만으로는 부족할 수 있으므로 **`generalPurpose` 등 적절한 Task 타입**을 선택한다. 상세: `references/qa-agent-guide.md` (`explore` 타입 주의).

### Phase 4: 스킬 생성

각 역할이 따를 절차를 `프로젝트/.cursor/skills/{name}/SKILL.md`에 둔다. 작성법: `references/skill-writing-guide.md`.

#### 4-1~4-5 (구조, description, 본문 원칙, Progressive Disclosure, 역할-스킬 연결)

원본 하네스 Phase 4와 동일. 경로만 `.cursor/skills/` 기준으로 유지한다.

**AGENTS.md 임시 동기화 (Phase 4 완료 시):** 스킬 목록·디렉터리 구조 반영.

### Phase 5: 통합 및 오케스트레이션

오케스트레이터 스킬은 “누가·언제·어떤 순서로 Task를 호출하고 `_workspace/`를 어떻게 쓰는가”를 정의한다. 템플릿: **`references/orchestrator-template.md` (템플릿 A: Cursor 멀티 Task)**.

#### 5-1. 데이터 전달 (Cursor)

| 전략 | 방식 |
|------|------|
| **파일 기반 (권장)** | 약속된 경로에 읽기/쓰기 — 서브에이전트 간 직접 통신 대체 |
| **순차 프롬프트** | 이전 산출물 경로·요약을 다음 Task 설명에 포함 |
| **병렬 후 통합** | 메인이 모든 파일 Read 후 단일 산출물 생성 |

규칙:
- `_workspace/`에 중간 산출물
- 파일명 예: `{phase}_{role}_{artifact}.{ext}`

#### 5-2. 에러 핸들링

원칙: 1회 재시도 → 실패 시 누락 명시·출처 병기. 상세는 `references/orchestrator-template.md`.

#### 5-3. 병렬 규모

동시 Task 수는 **2~5개** 전후를 기준으로 과도한 병렬을 피한다.

#### 5-4. AGENTS.md 하네스 블록

````markdown
## 하네스: {도메인명}

**목표:** {한 줄}

**역할 (에이전트 명세):**
| 파일 | 역할 |
|------|------|
| `.cursor/harness/agents/{name}.md` | {한 줄} |

**스킬:**
| 스킬 | 용도 | 사용 역할 |
|------|------|-----------|
| `{skill-name}` | {한 줄} | {역할} |

**실행 규칙:**
- {도메인} 관련 복합 작업은 `{orchestrator-skill}` 스킬을 따른다.
- 단순 질의는 일반 대화로 처리 가능.
- 중간 산출물: `_workspace/`

**디렉터리:**
```
.cursor/
├── harness/agents/     # 역할 정의
└── skills/             # 스킬 (하네스 스킬 포함)
```

**변경 이력:**
| 날짜 | 변경 | 대상 | 사유 |
|------|------|------|------|
| {YYYY-MM-DD} | 초기 구성 | 전체 | - |
````

역할 분담: `AGENTS.md`는 “무엇이 있고 언제 쓰는지”, 오케스트레이터 스킬은 “어떻게 실행하는지”.

#### 5-5. 후속 작업

- 오케스트레이터 `description`에 **재실행·수정·보완·부분 재실행** 키워드를 넣는다.
- Phase 0 컨텍스트 확인: `_workspace/` 유무로 초기/부분/전체 재실행을 가른다.
- 각 역할 `.md`에 “이전 산출물이 있을 때” 동작을 명시한다.

### Phase 6: 검증 및 테스트

`references/skill-testing-guide.md` 준수.

- 구조: 파일 위치, frontmatter, 역할↔스킬 참조 일관성
- **Claude 전용 커맨드 디렉터리는 생성하지 않는다** (불필요한 `.cursor/commands` 남발 금지)
- 스킬 실행·트리거·드라이런 — 원본 Phase 6과 동일

### Phase 7: 하네스 진화

7-1~7-4: 피드백 수집·반영·변경 이력 — 대상 파일이 `AGENTS.md`인 점만 제외하고 원본과 동일.

#### 7-5. 운영/유지보수

- `.cursor/harness/agents/` vs `AGENTS.md` 테이블
- `.cursor/skills/` vs `AGENTS.md`
- 불일치 시 수정 후 변경 이력 기록

## 산출물 체크리스트

- [ ] `.cursor/harness/agents/` — 역할 명세 파일
- [ ] `.cursor/skills/` — 역할별·오케스트레이터 스킬
- [ ] 오케스트레이터 1개 (데이터 흐름 + 에러 + 테스트 시나리오)
- [ ] 실행 방식 명시 (멀티 Task 병렬 / 순차 Task)
- [ ] 충돌 없는 스킬 description (후속 키워드 포함)
- [ ] SKILL.md 본문 500줄 이내 권장, 초과 시 `references/`
- [ ] **AGENTS.md**에 하네스 요약·변경 이력
- [ ] `_workspace/` 규칙 및 Phase 0 컨텍스트 분기

## 참고

- `references/cursor-runtime.md` — Claude Code ↔ Cursor 매핑
- `references/agent-design-patterns.md`
- `references/team-examples.md`
- `references/orchestrator-template.md`
- `references/skill-writing-guide.md`
- `references/skill-testing-guide.md`
- `references/qa-agent-guide.md`
