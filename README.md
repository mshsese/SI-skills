# SI Skills — 서일이앤씨 AX 교육용 Claude Code 스킬 모음

강의 실습 전용으로 다듬은 Claude Code 커스텀 스킬 패키지입니다.
실무용 `pm-` 스킬(github.com/mshsese/pm-skills)을 **강의장에서 바로 결과물을 내는 데** 맞춰 경량화했습니다.

## 왜 SI- 인가

실무용 스킬은 "완벽한 기획·완벽한 계획"이 목표라 시간이 무제한입니다.
강의에서는 정해진 시간 안에 **수강생이 앱을 배포까지** 끝내야 합니다.
그래서 기획·검토·계획 단계가 "산으로 가지 않도록" 상한을 걸었습니다.

| 스킬 | 강의 경량화 핵심 |
|---|---|
| **SI-deep-interview** | 질문 최대 10개, 한 주제 깊게 파지 않음, 10~15분 안에 기획안 → 바로 빌드 |
| **SI-grill-me** | relentless 제거, 치명적 약점만 최대 10개, 점검 후 통과 |
| **SI-write-plan** | TDD 루프·세부 단계 제거, 짧은 구현 체크리스트 |

나머지 스킬(auto·caveman·ctx-update·flow·office-hours·shadcn·to-issues·ui-design-system·requesting-code-review)은
실무판과 동일하되 내부 참조만 SI-로 맞췄습니다.

## 설치 방법

Claude Code에 입력:

```
github.com/mshsese/SI-skills 에서 SI 스킬 전부 설치해줘
```

또는 개별 스킬 폴더를 `~/.claude/skills/` 아래에 복사하면 됩니다.

## 스킬 목록 (12종)

### 작업 관리
- `SI-ctx-update` — 작업 맥락 자동 정리·저장
- `SI-caveman` — 답변 압축 (토큰 절약)
- `SI-auto` — 확인 없이 스스로 판단·실행

### 기획 · 아이디어
- `SI-deep-interview` ★경량 — 생각을 인터뷰로 구체화 (질문 10개 상한)
- `SI-grill-me` ★경량 — 기획 약점 점검 (질문 10개 상한)
- `SI-office-hours` — 만들 가치가 있는지 검증

### 계획 · 실행
- `SI-to-issues` — 작업 단위로 쪼개기
- `SI-write-plan` ★경량 — 짧은 구현 체크리스트

### 개발 · UI
- `SI-ui-design-system` — 코드 전, UI 디자인 기준 문서화
- `SI-shadcn` — 실제 화면에 디자인 입히기(시공)
- `SI-requesting-code-review` — 코드 품질 점검

### 워크플로우 안내
- `SI-flow` — 위 스킬들을 개발 순서대로 안내

> ★경량 = 강의 시간에 맞춰 질문·산출을 가볍게 다듬은 버전

## 실무용과의 관계

- 실무용 정본: [github.com/mshsese/pm-skills](https://github.com/mshsese/pm-skills) (계속 유지)
- 이 저장소(SI-): 서일이앤씨 AX 교육 강의 실습 전용

---

Lifebell · 서일이앤씨 AX 심화교육
