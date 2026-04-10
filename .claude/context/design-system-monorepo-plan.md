# 범용 디자인 시스템 모노레포 설계

## 날짜
2026-04-10

## 작업 내용

### 1. awesome-design-md 프로젝트 분석
- 59개 기업의 디자인 시스템을 DESIGN.md 마크다운 포맷으로 정리한 컬렉션
- 9개 섹션 구조 (Visual Theme, Color, Typography, Components, Layout, Depth, Do's/Don'ts, Responsive, Agent Prompt)
- 실제 DESIGN.md 파일은 `getdesign.md` 외부 호스팅으로 이전됨
- CLI로 가져오기: `npx getdesign@latest add [company]`

### 2. 범용 디자인 시스템 설계 결정
- **핵심 컨셉**: 토큰만 교체하면 전체 UI가 바뀌는 범용 디자인 시스템 (Token Swap)
- **모노레포 구조**: Turborepo + pnpm
  - `packages/design-system/` — 토큰 + 공통 컴포넌트
  - `apps/saju/` — 첫 번째 앱 (일본 타겟 사주 서비스, Next.js)
- **기술 스택**: React + TypeScript + Tailwind CSS + Storybook 8
- **동작 코어**: Radix Primitives (headless), react-day-picker, @tanstack/table
- **스타일링**: CSS 변수 토큰 기반 직접 구현

### 3. 토큰 구조 — Primitive/Semantic 2단
- **Primitive**: 원시값 RGB 채널 (`--blue-500: 59 130 246`)
- **Semantic**: 의미 기반 (`--color-bg-surface`, `--color-action-primary-hover`)
- **상태 토큰**: hover/active/disabled 별도 정의
- **다크모드**: `.dark` 클래스에서 Semantic 토큰만 재매핑
- **Tailwind 투명도**: RGB 채널 정의로 `bg-primary/50` 유틸리티 지원

### 4. 컴포넌트 (27개)
- Layout 6: HStack, VStack, Grid, Container, Spacer, Divider
- UI 6: Button, Card, Input, Text, Avatar, Badge
- Overlay 3: Modal, Drawer, Toast
- Navigation 3: Tabs, Navbar, Sidebar
- Form 4: Select, Checkbox, Radio, DatePicker
- Data 1: Table
- Feedback 4: Skeleton, EmptyState, BottomSheet, IconButton

### 5. 컴포넌트 공통 규칙
- 토큰 기반 (하드코딩 없음)
- size/variant/className props
- as prop (Polymorphic) + `PolymorphicComponentProps<C>` 제네릭 타입
- forwardRef
- 각 컴포넌트에 meta.json (에이전트용 메타데이터)
- 각 컴포넌트에 .stories.tsx (Storybook)

### 6. 단계적 구현
- **1차**: 모노레포 스캐폴딩 + Storybook + 2단 토큰 + Layout 6개 + Button/Card/Text/Input → 실제 화면 조립 검증
- **2차**: Avatar, Badge, IconButton + Modal/Drawer/Toast (Radix) + Skeleton/EmptyState
- **3차**: Form (Radix 기반) + DatePicker + Table + Navigation + BottomSheet

### 7. 멀티 에이전트 워크플로우
- Claude Code: 코딩 + 빌드
- Claude in Chrome / Claude Preview: 브라우저에서 UI 확인
- Gemini CLI: 시각적 디자인 리뷰어 역할 (선택적 추가)
- Ralph Loop: Docker 샌드박스에서 태스크 리스트 자동 순회 실행 가능

### 8. 세션 간 컨텍스트 유지
- CLAUDE.md — 프로젝트 규칙 (자동 읽음)
- docs/DESIGN_SPEC.md — 전체 설계 스펙
- DESIGN_RULES.md + design-rules.json — 접근성/디자인 정량 규칙
- *.meta.json — 컴포넌트 메타데이터

### 9. 사용자 선호 (메모리에 저장됨)
- AI 에이전트가 읽고 수정하기 가장 적합한 도구 우선 선택
- 설계/구현 세션 분리 선호, 문서로 컨텍스트 연결

## 관련 파일 목록
- `.claude/plans/woolly-soaring-patterson.md` — 상세 플랜 (프로젝트 구조, 토큰 예시 코드, Storybook 구성 등 포함)
- `.claude/projects/-Users-teru-axz-pc-awesome-design-md/memory/MEMORY.md` — 사용자 메모리
- `.claude/projects/-Users-teru-axz-pc-awesome-design-md/memory/project_design_system_monorepo.md`
- `.claude/projects/-Users-teru-axz-pc-awesome-design-md/memory/feedback_agent_friendly.md`
- `.claude/projects/-Users-teru-axz-pc-awesome-design-md/memory/feedback_plan_only_first.md`

## 코드 변경 사항
- 코드 구현 없음 (설계/계획 단계만 진행)

## 현재 상태
**설계 완료 / 구현 미시작**

다음 세션에서 할 일:
1. 새 프로젝트 폴더에 모노레포 스캐폴딩
2. CLAUDE.md + docs/DESIGN_SPEC.md 생성 (플랜에서 추출)
3. 1차 범위 구현 시작 (토큰 + Layout + 핵심 컴포넌트 4개 + Storybook)
