---
name: SI-shadcn
description: Next.js + Tailwind 프로젝트에서 프론트엔드 UI를 구현할 때 사용. 사용자가 준 디자인 레퍼런스(md 파일·레퍼런스 이미지·참고 URL)를 읽어 디자인 토큰을 뽑고 shadcn 테마에 주입한 뒤, shadcn/ui 컴포넌트로 inline 스타일 없이 일관되게 구현한다. 서일이앤씨 ERP, SNUH 인트라넷, 라이프벨 ERP 등에 UI를 추가·개선할 때 트리거.
---

# SI-shadcn — 레퍼런스 구동 shadcn/ui 프론트엔드 구현 가이드

## 강의용 사용 위치

`SI-shadcn`은 실제 Next.js + Tailwind 프로젝트가 생긴 뒤 화면을 구현하거나 리팩터링할 때 쓰는 구현 스킬이다.

강의 기본 흐름에서는 코드 작성 전에 먼저 `SI-ui-design-system`으로 `DESIGN.md`를 UI 디자인 시스템 문서로 정리하고, 이후 실제 프로젝트가 생긴 뒤 `SI-shadcn`으로 테마와 컴포넌트를 적용한다.

시간이 부족하거나 이미 프로젝트 구조가 명확하면 `SI-shadcn`에 `DESIGN.md`를 바로 주고 설계와 구현을 한 번에 진행할 수도 있다.

## 0. 핵심 원칙

- **레퍼런스 우선** — 디자인 기준(md/이미지/URL)이 주어지면 그것을 *진실의 출처*로 삼아 **테마부터 맞춘 뒤** 컴포넌트를 짠다. 컴포넌트를 먼저 찍어내고 색을 나중에 입히지 않는다.
- **inline style 금지** — `style={{ color: 'red' }}` 대신 shadcn 컴포넌트 + Tailwind 클래스 + 테마 변수 사용
- **shadcn 우선** — 버튼·카드·테이블·팝업 등 있는 컴포넌트는 직접 짜지 말고 shadcn 가져와서 씀
- **기술 스택 확인** — Next.js + TypeScript + Tailwind CSS 프로젝트에만 적용

### 작업 순서 (한눈에)

```
1. 디자인 레퍼런스 흡수 → 토큰 추출 → 테마 주입   ← 0순위 (이 섹션)
2. shadcn 세팅 (init + 컴포넌트 add)
3. 테마 위에서 컴포넌트로 화면 구현
4. 레퍼런스랑 대조 검수
```

---

## 1. 디자인 레퍼런스 흡수 → 테마 주입 (가장 먼저)

이 단계가 "내가 준 느낌대로 꾸며지는" 핵심이다. 특정 파일명을 하드코딩하지 않는다 — **사용자가 준 입력이 무엇이든** 그것을 디자인 기준으로 삼는다.

### 1.1 레퍼런스 입력 받기

아래 순서로 디자인 기준을 확보한다:

1. **사용자가 직접 지정한 것** — md 파일(이름 무엇이든: `DESIGN.md`, `브랜드가이드.md`, `디자인시스템.md` 등), 레퍼런스 이미지(스크린샷·피그마 캡처·무드보드), 참고 사이트 URL. 주어지면 이게 1순위 진실의 출처.
2. **자동 탐색** — 지정이 없으면 작업 폴더에서 `DESIGN.md` / `디자인*.md` / `*style*.md` 류를 찾아 "이걸 디자인 기준으로 쓸까요?" 한 번 확인.
3. **그래도 없으면** — "디자인 기준 파일이나 레퍼런스 이미지 있으면 줘. 없으면 shadcn 기본 톤(Neutral)으로 갈게" 라고 한 번 묻고, 없으면 기본 톤으로 진행.

> 이미지를 받으면 Read로 직접 보고, md는 Read로 읽고, URL은 필요 시 가져와 분석한다.

### 1.2 디자인 토큰 추출

레퍼런스에서 아래를 뽑아 **요약 표로 한 번 정리**한 뒤 진행한다 (사용자가 한눈에 확인·수정 가능하게):

| 토큰 | 뽑을 것 |
|------|--------|
| **색 팔레트** | 주색(primary), 배경/표면, 글자색, 강조/경고색. 명도 대비 확인 |
| **타이포그래피** | 본문/제목 폰트, 굵기 위계, 대략의 크기 스케일 |
| **모서리(radius)** | 날카로움 ↔ 둥글둥글 (예: 0 / 6px / 12px) |
| **여백·밀도** | 빽빽 ↔ 여유. 카드/섹션 간격 감각 |
| **그림자** | 그림자 없음(flat) ↔ 떠 있는 느낌 |
| **무드** | 한 줄로 (예: "warm ivory, 차분한 전략회의실 톤") |

레퍼런스에 명시 안 된 항목은 무드에 맞춰 합리적으로 채우되, 추정한 부분은 표시한다.

### 1.3 shadcn 테마에 주입

추출한 토큰을 shadcn 테마 시스템에 박는다. shadcn은 **테마가 CSS 변수 한 곳에 모여 있는** 구조라 여기만 바꾸면 전 컴포넌트에 일괄 반영된다.

1. **`components.json`** — `tailwind.cssVariables: true`(기본값) 확인. `baseColor`는 추출한 무드에 가장 가까운 것으로(neutral/zinc/stone/slate 등).
2. **`globals.css`** (또는 `components.json`의 `css`가 가리키는 파일) — shadcn가 심어둔 테마 변수 블록을 찾아 값을 교체한다:
   - `:root` 안의 시맨틱 토큰을 추출 색으로: `--background`/`--foreground`, `--primary`/`--primary-foreground`, `--secondary`, `--muted`, `--accent`, `--destructive`, `--border`, `--ring` 등. base/`-foreground`는 **표면색/그 위 글자색 쌍**이므로 대비를 지켜 함께 바꾼다.
   - `--radius` → 추출한 모서리 값.
   - 다크모드가 필요하면 `.dark` 블록에서 같은 토큰을 덮어쓴다.
   - 색 형식은 프로젝트가 쓰는 형식을 따른다(HSL 또는 OKLCH). Tailwind 버전에 따라 `@layer base`의 `:root` 또는 `@theme` 형식일 수 있으니 **실제 생성된 파일 형식을 그대로 따른다.**
3. **폰트** — `tailwind.config`(또는 Next `app/layout` 폰트 설정)에 본문/제목 폰트를 연결.

> Tailwind는 이 변수들을 `bg-background`, `text-foreground`, `border-border`, `ring-ring` 같은 유틸로 매핑한다. 즉 컴포넌트는 변수만 참조하고, 톤 변경은 변수에서 한 번에 일어난다.

### 1.4 레퍼런스 대조 (구현 후)

화면을 다 만든 뒤 레퍼런스 이미지/md와 나란히 비교한다: 주색·여백·모서리·밀도·무드가 일치하는가? 어긋난 부분은 **컴포넌트가 아니라 테마 변수에서** 우선 조정한다.

---

## 2. shadcn 세팅 (프로젝트에 처음 추가할 때)

프로젝트 폴더에서 아래 순서로 실행:

```bash
# 1단계: shadcn 초기화 (처음 한 번만)
npx shadcn@latest init
# 질문 나오면 baseColor는 1.2에서 정한 무드에 맞춰 선택 (Neutral/Zinc/Stone/Slate 등)

# 2단계: 필요한 컴포넌트 추가
npx shadcn@latest add button card table badge select dialog toast
```

설치 후 `src/components/ui/` 폴더에 파일들이 생김. 이제 내 코드에서 바로 import해서 씀. (테마 주입은 1.3에서 이미 끝난 상태)

---

## 3. 자주 쓰는 컴포넌트 치트시트

### 버튼
```tsx
import { Button } from "@/components/ui/button"

<Button>기본 버튼</Button>
<Button variant="outline">테두리 버튼</Button>
<Button variant="destructive">삭제 버튼</Button>
<Button size="sm">작은 버튼</Button>
```

### 카드 (프로젝트 현황, 통계 카드 등)
```tsx
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card"

<Card>
  <CardHeader>
    <CardTitle>프로젝트 현황</CardTitle>
  </CardHeader>
  <CardContent>
    내용
  </CardContent>
</Card>
```

### 배지 (상태 표시: 진행중, 완료, 입찰중 등)
```tsx
import { Badge } from "@/components/ui/badge"

<Badge>진행중</Badge>
<Badge variant="outline">입찰중</Badge>
<Badge variant="secondary">완료</Badge>
```

### 셀렉트 (담당자 선택, 상태 변경)
```tsx
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select"

<Select onValueChange={(v) => setStatus(v)}>
  <SelectTrigger>
    <SelectValue placeholder="상태 선택" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="진행중">진행중</SelectItem>
    <SelectItem value="완료">완료</SelectItem>
  </SelectContent>
</Select>
```

### 테이블 (나라장터 공고 목록, 프로젝트 목록)
```tsx
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table"

<Table>
  <TableHeader>
    <TableRow>
      <TableHead>공고명</TableHead>
      <TableHead>마감일</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    <TableRow>
      <TableCell>도로 보수 공사</TableCell>
      <TableCell>D-3</TableCell>
    </TableRow>
  </TableBody>
</Table>
```

### 다이얼로그 (팝업, 확인창)
```tsx
import { Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog"

<Dialog>
  <DialogTrigger asChild>
    <Button>상세 보기</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>프로젝트 상세</DialogTitle>
    </DialogHeader>
    내용
  </DialogContent>
</Dialog>
```

### 토스트 (성공/실패 알림)
```tsx
import { useToast } from "@/hooks/use-toast"

const { toast } = useToast()

toast({ title: "등록 완료", description: "프로젝트가 생성되었습니다." })
toast({ title: "오류", variant: "destructive", description: "다시 시도해주세요." })
```

---

## 4. 기존 프로젝트에 적용할 때 순서

1. **디자인 레퍼런스 흡수 → 테마 주입** (섹션 1) — 기존 프로젝트에도 톤 기준을 먼저 잡는다
2. `npx shadcn@latest init` 실행 (이미 했으면 건너뜀)
3. 필요한 컴포넌트만 `npx shadcn@latest add [컴포넌트명]` 으로 추가
4. 기존 inline style → shadcn 컴포넌트 + 테마 변수로 교체
5. `npm run build`로 에러 없는지 확인
6. 레퍼런스랑 대조 검수 (섹션 1.4)

---

## 5. 현재 프로젝트별 적용 포인트

| 프로젝트 | 디자인 기준 | 교체 우선순위 |
|---------|------------|------------|
| 서일이앤씨 ERP | (지정 시 해당 md) | `ErpDemoWorkspace.tsx` inline 스타일 → Card, Badge, Button, Table |
| SNUH 인트라넷 | (지정 시 해당 md) | 동일 스택이므로 동일 방식 적용 가능 |
| 라이프벨 ERP | `DESIGN.md` (warm ivory/typographic/전략회의실 톤) | 대시보드 카드·테이블 중심 |

> 디자인 기준 파일은 프로젝트마다 다르다. 위 표는 예시일 뿐, 실제로는 섹션 1.1 절차로 사용자가 준 입력을 따른다.

---

## 6. 컴포넌트 목록 전체 확인

```bash
npx shadcn@latest add --help
```

또는 shadcn/ui 공식 사이트에서 전체 컴포넌트 확인 가능.

---

## 7. 화면 기준: 기본은 PC, 요청 시 모바일

**기본값은 PC(데스크톱) 레이아웃이다.** 별도 요청이 없으면 데스크톱 기준으로 만든다.

사용자가 **"모바일 버전으로 만들어줘", "휴대폰 웹앱으로", "모바일 우선"** 등으로 요청하면 아래를 적용한다:

- **mobile-first**: 기본 스타일을 휴대폰 화면 기준으로 잡고, `md:` `lg:` 로 큰 화면을 확장한다.
- **viewport 메타 태그 확인**: `<meta name="viewport" content="width=device-width, initial-scale=1" />` (Next.js는 기본 포함되지만 확인).
- **터치 친화**: 버튼·링크 등 터치 타깃은 최소 44px 높이.
- **네비게이션**: 상단 가로 메뉴 대신 하단 탭바 또는 햄버거 메뉴 패턴.
- **넓은 컴포넌트 대체**: Table처럼 가로로 넓은 것은 모바일에서 카드 리스트로 바꾸거나 가로 스크롤 처리.
- **레이아웃**: 다단 그리드 대신 세로 1열 스택을 기본으로.

PC·모바일 모두 필요하면 반응형(하나의 코드로 양쪽 대응)으로 만들고, 모바일 화면부터 검수한다.
