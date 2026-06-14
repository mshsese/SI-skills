---
name: SI-to-issues
description: 기획서나 PRD를 독립 실행 가능한 작업 단위(이슈)로 쪼개서 이슈 트래커에 등록해줌. "작업 쪼개줘", "이슈로 만들어줘" 할 때 사용. SI-write-plan보다 먼저 — 통짜 계획 대신 기획(grill-me 도메인 분할)과 UI 디자인 시스템 기준을 세로 슬라이스로 먼저 쪼갠다.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference (issue number, URL, or path) as an argument, fetch it from the issue tracker and read its full body and comments.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Issue titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

Iterate until the user approves the breakdown.

### 5. Publish the issues to the issue tracker

For each approved slice, publish a new issue to the issue tracker. Use the issue body template below. These issues are considered ready for AFK agents, so publish them with the correct triage label unless instructed otherwise.

Publish issues in dependency order (blockers first) so you can reference real issue identifiers in the "Blocked by" field.

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking ticket (if any)

Or "None - can start immediately" if no blockers.

</issue-template>

Do NOT close or modify any parent issue.
## 추가 규칙: PM 문서 통합 출력

이 섹션은 기본 이슈 트래커 규칙보다 우선한다. 사용자가 별도 이슈 트래커 저장을 명시하지 않으면, 결과는 선택된 프로젝트 폴더의 PM 문서 세트 안에 저장한다.

### 저장 파일명

`SI-to-issues`의 기본 산출물은 다음 파일이다.

```md
NN.작업목록.[project].md
```

### 목적

`작업목록`은 제품기획서, 기획검토, 기술설계, UI 디자인 시스템, 실행계획을 실제 구현 가능한 세로 조각 이슈로 나눈 문서다. 사용자가 보기에는 하나의 프로젝트 폴더 안에서 전체 흐름이 이어져야 한다.

### 번호 생성 규칙

- `00`~`04` 번호는 `SI-ctx-update` 전용으로 예약한다.
- `SI-to-issues`가 새로 만드는 PM 문서는 항상 `05` 이상 번호만 사용한다.
- 새 파일을 만들기 전, 선택된 프로젝트 폴더 안의 `NN.*.md` 파일을 스캔한다.
- 이미 존재하는 번호 중 최댓값을 찾고, `max(05, 최댓값 + 1)`을 새 번호로 사용한다.
- 기존 `NN.작업목록.[project].md`가 있으면 새 번호로 중복 생성하지 말고 갱신한다.
- 별도 `.scratch` 이슈 트래커 파일은 사용자가 원하거나 기존 프로젝트 규칙이 강하게 요구할 때만 생성한다. 이 경우에도 `작업목록` 문서에는 해당 이슈 파일 링크를 반드시 모은다.

### 작업목록 문서 형식

`NN.작업목록.[project].md`에는 최소한 다음 내용을 둔다.

```md
# 작업목록 - [project]

## 문서 연결

- 문서 상태: 검토중 | 확정
- 이 문서의 역할: 프로젝트를 구현 가능한 세로 조각 작업으로 나눈다.
- 먼저 읽을 문서: 프로젝트개요, 제품기획서, 기획검토, 기술설계, 기술설계.UI디자인시스템, 실행계획
- 다음에 읽을 문서: 구현계획
- 이 문서를 참고할 때: 어떤 작업부터 구현할지 정하거나 작업을 AI에게 맡길 때
- 마지막 갱신 이유: SI-to-issues로 작업 단위 분해

## 분해 기준

- 세로 조각으로 나눈다.
- 각 작업은 완료되면 단독으로 확인 가능해야 한다.
- DB/API/UI/테스트 같은 가로 레이어만 따로 떼지 않는다.
- UI가 포함된 작업은 `기술설계.UI디자인시스템`의 색상, 밀도, 컴포넌트, 금지 규칙을 완료 기준에 반영한다.

## 작업 목록

### 1. [작업명]

- 유형: AFK | HITL
- 상태: ready-for-agent | needs-info | ready-for-human
- 막힘: 없음 또는 선행 작업
- 포함 요구사항: [제품기획서/요구사항정리 기준]
- 완료 기준:
  - [ ] [검증 가능한 기준]

## 다음 단계

- 각 작업별로 `SI-write-plan`을 실행해 `NN.구현계획.[작업명].md`를 만든다.
```

### 프로젝트개요 갱신

`NN.프로젝트개요.[project].md`가 있으면 `작업목록` 링크와 현재 단계를 갱신한다.

- 현재 단계: `SI-to-issues 완료`
- 다음에 읽을 문서: 구현계획
- 새로 생긴 작업목록 문서 링크
- UI 디자인 시스템 문서가 있으면 작업목록과 함께 링크

### 저장 위치 규칙

- 결과 파일은 반드시 선택된 프로젝트 폴더 아래에 둔다.
- `C:\Users\mshse\Desktop\ClAgent` 루트에는 만들지 않는다.
- 저장 후 생성 또는 갱신한 파일 경로를 알려준다.
