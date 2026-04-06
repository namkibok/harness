# 오케스트레이터 스킬 템플릿

오케스트레이터는 여러 역할(Task)을 조율하는 상위 스킬이다. Cursor에서는 **템플릿 A: 멀티 Task 병렬**(Claude Code “에이전트 팀”에 대응)과 **템플릿 B: 순차 Task**(경량)를 구분한다.

---

## 템플릿 A: 멀티 Task 병렬 모드 (Cursor · Claude의 “에이전트 팀” 대응)

`TeamCreate`/`SendMessage` 대신 **메인이 동일 턴에서 `Task`를 여러 번 호출**하고(`run_in_background: true`), 산출물은 **`_workspace/`**에 모은 뒤 메인이 Read·통합한다.

```markdown
---
name: {domain}-orchestrator
description: "{도메인} 멀티 Task 워크플로를 조율하는 오케스트레이터. {초기 키워드}. 후속: 수정, 부분 재실행, 업데이트, 보완, 재실행, 결과 개선 시에도 이 스킬 사용."
---

# {Domain} Orchestrator

{도메인}에서 여러 Task(서브에이전트)를 병렬·순차 호출해 {최종 산출물}을 만든다.

## 실행 모드: 멀티 Task + `_workspace/`

## 역할 구성

| 역할 | Task subagent_type (예) | 요약 | 스킬 | 출력 |
|------|-------------------------|------|------|------|
| {teammate-1} | generalPurpose / explore / shell | {역할} | {skill} | `_workspace/...` |
| {teammate-2} | … | {역할} | {skill} | `_workspace/...` |

상세: `.cursor/harness/agents/{name}.md` — Task 프롬프트에서 Read 하도록 명시.

## 워크플로우

### Phase 0: 컨텍스트 확인

1. `_workspace/` 존재 여부
2. 분기: 없음 → 초기 / 있음+부분 수정 → 해당 Task만 / 있음+새 입력 → `_workspace` 백업 후 초기
3. 부분 재실행 시 이전 산출물 경로를 Task 설명에 포함

### Phase 1: 준비

1. 입력 분석
2. `_workspace/` 생성, `_workspace/00_input/` 저장

### Phase 2: 병렬 Task 스폰

동일 턴에서 의존 없는 작업만 병렬:

```
Task( description: "`.cursor/harness/agents/{teammate-1}.md` 준수. …",
      subagent_type: generalPurpose, run_in_background: true )
```

**파일 규칙:** 각 역할은 지정 경로에만 기록. 교차 정보는 `_workspace/_briefing.md` 등으로 메인이 중재.

### Phase 3: {주요 작업}

병렬 Task 완료를 기다린 뒤 메인이 파일 검사. 실패 시 재시도·재할당.

### Phase 4: {검증/통합}

Read로 수집 → 통합 → `{output-path}/{filename}`

### Phase 5: 정리

`_workspace/` 보존, 사용자 요약.

## 데이터 흐름

```
[메인] ─Task(parallel)→ role-a → a.md ─┐
       ─Task(parallel)→ role-b → b.md ─┼→ Read → 통합
```

## 에러 핸들링

| 상황 | 전략 |
|------|------|
| Task 1개 실패 | 재시도 → 실패 시 누락 명시 후 진행 |
| 다수 실패 | 사용자 확인 |
| 산출물 충돌 | 출처 병기 |

## 테스트 시나리오

- 정상: 병렬 Task → 통합 산출물
- 에러: 한 Task 실패 → 부분 통합·미수집 명시
```

---

## 템플릿 B: 순차 Task 모드 (경량)

`Task`를 한 번에 하나씩 호출하거나 메인이 순서를 강제한다.

```markdown
---
name: {domain}-orchestrator
description: "{도메인} 순차 Task 오케스트레이터. {초기 키워드}. 후속 작업 키워드 동일하게 포함."
---

# {Domain} Orchestrator

{도메인}을 순차 Task로 처리하여 {최종 산출물}을 생성.

## 실행 모드: 순차 Task

## 역할 구성

| 단계 | subagent_type | 역할 | 스킬 | 출력 |
|------|--------------|------|------|------|
| {agent-1} | … | {역할} | {skill} | {output-file} |
| {agent-2} | … | {역할} | {skill} | {output-file} |

## 워크플로우

### Phase 0: 컨텍스트 확인

`_workspace/` 유무로 초기 / 부분 재실행 / 전체 재실행 분기 (템플릿 A와 동일).

### Phase 1: 준비

`_workspace/` 및 `00_input/` 준비.

### Phase 2: {주요 작업}

**병렬:** 동일 턴에 N개 `Task` 동시 호출 + `run_in_background: true`

| 역할 | 입력 | 출력 | run_in_background |
|------|------|------|-------------------|
| {agent-1} | {소스} | `_workspace/{phase}_{agent}_{artifact}.md` | true |

**순차:** 이전 파일 경로를 다음 Task 설명에 넣음.

### Phase 3: {검증/통합}

Read → 통합 → 최종 파일.

### Phase 4: 정리

`_workspace/` 보존, 요약 보고.

## 데이터 흐름

```
입력 → Task(agent-1) → artifact-1 ─┐
       Task(agent-2) → artifact-2 ─┼→ 통합 → 최종
```

## 에러 핸들링

| 상황 | 전략 |
|------|------|
| Task 1개 실패 | 1회 재시도 후 누락 명시 |
| 과반 실패 | 사용자 확인 |

## 테스트 시나리오

정상 1 + 에러 1 이상.
```

---

## 작성 원칙

1. **실행 모드 명시** — “멀티 Task 병렬” 또는 “순차 Task”.
2. **멀티 Task 모드** — 병렬 호출 조건, `_workspace/` 경로 규약, 메인 통합 단계.
3. **순차 모드** — 각 Task의 입력(이전 산출물 경로)과 `subagent_type`, `run_in_background` 명시.
4. **파일 경로** — `_workspace/` 기준으로 구체적으로.
5. **Phase 의존성** — 문서에 적을 것.
6. **에러 핸들링** — 낙관적 가정 금지.
7. **테스트 시나리오** — 정상 1 + 에러 1 이상.

## description 작성 시 후속 작업 키워드

재실행, 수정, 보완, 부분 재실행, 이전 결과 개선, 도메인 일상 키워드 등을 포함한다.

## 실제 오케스트레이터 참고

팬아웃/팬인: 준비 → Phase 0 → **N개 Task 병렬** → Read·통합 → 정리. 예시 흐름은 `team-examples.md` 상단 Cursor 안내와 함께 본다.
