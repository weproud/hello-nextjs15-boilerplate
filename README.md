# 제품 요구 명세서 (PRD): Next.js 15 풀스택 애플리케이션

본 문서는 Next.js 15 (App Router) 및 최신 기술 스택을 활용한 현대적이고 확장 가능한 풀스택 웹 애플리케이션의 구축을 위한 상세 제품 요구 명세서(PRD)입니다. 초기 초안 및 온라인 조사 결과를 종합하여, 각 기술 스택 선택의 심층적 근거, 주요 기능 구현의 기술적 접근 방식 및 도전 과제를 상세히 명시합니다. 본 문서는 타입 안전성, 성능 최적화, 뛰어난 개발자 경험 및 실용성을 최우선으로 고려하며, 향후 실제 개발 프로젝트의 로드맵 역할을 수행합니다.

## 1. 프로젝트 개요 (Project Overview)

### 1.1 목적 (Purpose)

본 프로젝트는 Next.js 15의 최신 기능(App Router, Turbopack, Async Request API 등)과 엄선된 현대적 기술 스택을 결합하여, 타입 안전성, 고성능, 뛰어난 유지보수성 및 확장성을 갖춘 풀스택 웹 애플리케이션의 기준 모델을 제시하는 것을 목표로 합니다. 특히, 복잡한 UI 패턴(모달 기반 인증) 및 동적인 데이터 처리(실시간 업데이트)를 효율적으로 구현하는 기술적 접근 방식을 구체화합니다.

### 1.2 목표 (Goals)

- 최첨단 기술 스택 통합: Next.js 15 및 관련 생태계의 최신 모범 사례를 반영한 풀스택 아키텍처 설계 및 구축.
- 개발 생산성 및 경험 극대화: Turbopack, TypeScript, Zod, Prisma, shadcn/ui 등을 활용하여 빠르고 안정적인 개발 환경 및 워크플로우 구축.
- 고성능 및 확장성 확보: Next.js의 렌더링 전략, SWR 캐싱, Supabase/PostgreSQL의 확장성, 모듈화된 코드베이스를 통해 대규모 트래픽 및 기능 확장에 대비.
- 타입 안전성 및 코드 품질 강화: TypeScript, Zod 유효성 검사, Prisma ORM, ESLint/Prettier를 통해 견고하고 유지보수 용이한 코드베이스 유지.
- 안정적인 기능 구현: Auth.js 기반 모달 인증, Supabase Realtime 기반 실시간 데이터 동기화, Tiptap 기반 리치 텍스트 편집 등 핵심 기능의 기술적 접근 방식 구체화.
- 견고한 품질 보증 체계: Vitest 단위/통합 테스트, Playwright E2E 테스트 자동화 구축.
- 유연한 다국어 및 테마 지원: next-intl 및 next-themes 라이브러리를 활용한 사용자 경험 개선.

## 2. 기술 스택 (Tech Stack) - 심층 분석 및 도전 과제

본 프로젝트는 다음과 같은 기술 스택을 사용합니다. 각 기술의 선택 근거, 예상되는 도전 과제 및 해결 방안을 상세히 설명합니다.

- 프레임워크: Next.js 15 (App Router)

  - 선택 근거: - 풀스택 기능: SSR/SSG, API Routes(Route Handlers, Server Actions), Middleware 등 웹 애플리케이션 구축에 필요한 모든 기능을 내장하여 개발 복잡성 감소. - App Router: 파일 시스템 기반 라우팅, Server Components/Client Components 분리, Layouts, Parallel & Intercepting Routes 등 현대적 React 패턴 및 고성능 UI 구축 지원. - React 19 지원: 최신 React 기능(React Compiler 실험적 도입 등) 활용 가능. - 성능 최적화: Turbopack 개발 환경 안정화, Async Request API 개선, 캐싱 전략 유연성 강화, 이미지/폰트 최적화 내장.
    > Next.js 15는 React 19 지원, Turbopack 정식 Dev 환경 도입, 캐싱 및 Async API 대대적 개선으로 성능과 개발 경험 혁신을 제공합니다¹.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: App Router 패턴의 학습 곡선, Async Request API 및 기본 캐싱 정책 변경에 따른 마이그레이션 필요성². Turbopack의 `next build` 단계는 아직 알파 단계 지원¹.
    - 해결 방안: 공식 문서 및 마이그레이션 가이드 활용, `@next/codemod` 사용¹³. 주요 변경 사항(특히 캐싱)에 대한 철저한 이해와 애플리케이션 특성에 맞는 캐싱 전략 설계. 빌드 환경은 안정화까지 Webpack 사용을 고려하거나, Turbopack 제한 사항을 인지하고 대안 마련¹.
  - 기술적 접근 방식 (예시):
    - 데이터 패칭: Server Components에서 `async/await` 및 확장된 `fetch` API 활용. Client Components에서 SWR 활용.
    - 라우팅: `app` 디렉토리 구조 기반 파일 시스템 라우팅. 동적 라우팅 `app/[slug]`, 병렬 라우팅 `app/@modal`, 인터셉트 라우팅 `app/(.)modal` 패턴 사용.

- 번들러: Turbopack

  - 선택 근거:
    - 혁신적인 개발 속도: Webpack 대비 압도적으로 빠른 로컬 서버 시작 및 핫 리로딩 성능 제공¹.
    - Next.js 15 통합: 개발 환경 기본 번들러로 채택되어 최적의 호환성 보장.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: `next build` 지원 단계가 낮고, 일부 Webpack 설정(alias, extensions 커스텀 등)이 아직 완벽히 지원되지 않거나 사용법이 다름¹².
    - 해결 방안: 개발 환경에 집중하여 빠른 피드백 활용. 빌드 시 제한 사항은 인지하고, 필요한 경우 대안(Webpack으로 fallback 또는 제한 사항 우회) 고려.
  - 기술적 접근 방식: `next dev` 명령어를 통한 기본 사용. 필요시 `next.config.ts`의 `experimental.bundleCompiler` 설정 활용².

- 언어: TypeScript

  - 선택 근거:
    - 타입 안전성: 개발 단계에서 오류 조기 발견, 런타임 에러 감소, 코드 예측 가능성 향상.
    - 개발 생산성: 자동 완성, 리팩토링 용이성, 문서화 효과.
    - 생태계 호환성: 주요 라이브러리(React, Next.js, Prisma, Zod 등)와의 강력한 통합 지원.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 초기 학습 곡선, 복잡한 타입 정의의 어려움. 대규모 Prisma 스키마에서 타입 생성 오버헤드 발생 가능성⁴.
    - 해결 방안: 공식 문서 및 커뮤니티 자료 활용. 점진적 타입 적용 전략. Prisma 대규모 스키마 문제는 Prisma 이슈 #4807을 인지하고, 모듈 분리, 필요한 경우 대안 고려⁴.
  - 기술적 접근 방식: 모든 코드 파일에 `.ts` 또는 `.tsx` 확장자 사용. 엄격한 타입 체크 설정 (`strict: true` in `tsconfig.json`). Zod 등과 연계한 타입 안전성 강화.

- 데이터베이스: Supabase (PostgreSQL)

  - 선택 근거: - 관리형 PostgreSQL: 안정적이고 강력한 오픈 소스 관계형 데이터베이스 제공. - BaaS 기능 통합: 인증, 스토리지, Edge Functions 등 백엔드 기능 제공. - 실시간 구독: Postgres Changes 기능을 통해 데이터베이스 변경 사항 실시간 전파 지원. - Auth.js 연동 용이: 인증 사용자와 데이터베이스 레코드 간의 통합 관리 편리.
    > Supabase는 관리형 PostgreSQL 기반으로 강력한 확장성을 제공하며, Realtime 기능으로 실시간 데이터 처리가 용이합니다⁵⁶.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 대규모 쓰기(Write) 작업 시 마스터 노드 성능에 제한될 수 있음. Supabase Storage가 CDN에 최적화되지 않음. 예상치 못한 비용 발생 (RAM 사용량 등)⁵⁶.
    - 해결 방안: 읽기 복제본(Read Replicas) 활용⁵. 대용량 파일 제공 시 별도 CDN 서비스 고려⁵. 사용량 모니터링 및 비용 예측², 적절한 컴퓨트 플랜 선택⁵.
  - 기술적 접근 방식: Supabase Client SDK 사용. Row Level Security (RLS) 설정으로 데이터 접근 제어. Postgres Changes를 활용한 실시간 구독 구현⁶.

- ORM: Prisma

  - 선택 근거: - 타입 안전한 쿼리: 스키마 기반 자동 타입 생성으로 개발자 경험 및 코드 안정성 향상⁴. - 직관적인 API: CRUD 작업 및 관계형 데이터 조작 간소화⁷. - 마이그레이션 시스템: 데이터베이스 스키마 변경 이력 관리 및 적용 용이⁷.
    > Prisma는 타입 안전성, 자동 마이그레이션 등 많은 장점을 제공합니다⁴⁷.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 약 60개 이상의 테이블 규모에서 타입 생성 오버헤드로 개발 환경 저하 발생 가능성⁴. `client extensions` 사용 시 잠재적 문제 발생 가능성⁴. 특정 조인 형태의 제약⁴.
    - 해결 방안: 대규모 스키마 문제 발생 시 모듈 분리, 필요한 경우 Drizzle, Kysely 또는 순수 SQL과 같은 대안 고려⁴. `client extensions`는 신중하게 사용⁴. 성능 Critical한 쿼리는 네이티브 쿼리 활용 방안 검토⁴. 테스트용 DB에서 마이그레이션 선행²⁷.
  - 기술적 접근 방식: `schema.prisma` 파일에 데이터 모델 정의. `PrismaClient` 인스턴스를 사용하여 데이터베이스 작업 수행. `prisma migrate dev` 명령어를 통한 마이그레이션 관리²⁷. `$transaction`으로 원자적 작업 처리⁷. `include`, `select` 등으로 N+1 문제 방지⁷.

- UI 라이브러리: shadcn/ui

  - 선택 근거: - 높은 커스터마이징성: 소스 코드 직접 포함 방식으로 디자인 시스템 통합 및 변경 유연성 극대화⁸. - Radix UI & Tailwind CSS 기반: 접근성(WAI-ARIA), 키보드 네비게이션 등 모범 사례 반영 및 스타일링 용이⁸. - 최소한의 종속성: 프로젝트 경량화⁸.
    > shadcn/ui는 최신 Tailwind CSS와 Radix UI를 조합해 접근성과 확장성, 유지보수성을 극대화한 고품질 React UI 컴포넌트를 제공합니다⁸.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 라이브러리 업데이트 시 수동으로 코드 변경 사항 병합 필요⁸.
    - 解決방안: 정기적으로 shadcn/ui CLI를 통해 업데이트 확인 및 적용. 커스텀 코드를 명확히 분리하여 병합 용이성 확보.
  - 기술적 접근 방식: shadcn/ui CLI (`npx shadcn-ui@latest add <component>`)를 사용하여 컴포넌트 추가⁸. Tailwind CSS 설정 파일 (`tailwind.config.js`) 및 CSS 변수(`globals.css`)를 통해 스타일 커스터마이징⁸. Class Variance Authority (CVA) 활용⁸.

- 에디터: Tiptap

  - 선택 근거: - 확장성: ProseMirror 기반으로 다양한 기능 확장 및 커스터마이징 용이⁹. - 프레임워크 agnostic: React 환경에 잘 통합되며, 향후 다른 프레임워크와의 호환성 고려 가능. - 실시간 협업 잠재성: Yjs 등 라이브러리와 연동하여 실시간 협업 편집 기능 구현 기반 마련⁹.
    > Tiptap은 최신 Next.js 15 환경에서 React UI와 결합해 고도로 확장 및 커스터마이징 가능한 리치 텍스트 에디터로 자리잡고 있습니다⁹.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 깊은 커스터마이징 시 ProseMirror 내부 구조 이해 필요⁹. 서버 사이드 문서 조작 복잡성⁹. 클라이언트 전용 라이브러리이므로 Next.js App Router 환경에서 'use client' 명시 필수⁹.
    - 해결 방안: 초기에는 제공되는 Extensions 활용, 필요시 점진적으로 ProseMirror 학습. 서버에서는 JSON 형식으로 문서 저장 및 조작⁹. 에디터 컴포넌트에 명확하게 `'use client'` 디렉티브 적용⁹.
  - 기술적 접근 방식: `@tiptap/react`의 `useEditor` 훅 및 `EditorContent` 컴포넌트 사용⁹. `Extensions`를 통해 기능 추가 (예: `StarterKit` for basic formatting, `Image` for image insertion)⁹. 에디터 콘텐츠는 HTML 또는 JSON 형식으로 저장/불러오기⁹.

- 인증: Auth.js (NextAuth)

  - 선택 근거: - Next.js 통합: Next.js App Router에 최적화된 인증 솔루션 제공¹⁰. - 다양한 Provider 지원: OAuth, 이메일, Credentials 등 여러 인증 방식 통합 용이¹⁰. - 유연한 세션 관리: JWT 또는 데이터베이스 세션 지원¹⁰.
    > Auth.js는 App Router 병렬/인터셉팅 라우트 패턴과 환상적으로 결합되어, 인증 흐름과 비정형 UI(모달)간 보안·분리·재사용성을 대폭 강화합니다¹¹¹⁰.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: App Router 환경에서 Intercepting Routes를 활용한 모달 인증 구현 시 공식 예시가 제한적임¹⁰. 세션 보안(JWT, Environment variables) 철저한 관리가 필요¹⁰.
    - 해결 방안: App Router의 병렬 및 인터셉트 라우트 패턴(`@slot`, `default.js`, `(.)` 컨벤션)과 Auth.js의 `useSession` 훅 및 `middleware.ts`를 조합하여 구현¹¹¹⁰. 환경 변수 및 Secret 값은 안전한 방식으로 관리¹⁰.
  - 기술적 접근 방식: `app/api/auth/[...nextauth]/route.js` 경로에 인증 API 구성¹⁰. `middleware.ts`에서 인증 경로 보호 및 리다이렉트 처리¹⁰. `app/@auth/(.)signin/page.tsx`와 같은 구조로 인증 모달 페이지 구현¹¹.

- 국제화: next-intl

  - 선택 근거: - Next.js App Router 지원: 서버 및 클라이언트 컴포넌트에서 다국어 메시지 처리 지원¹². - 유연한 로케일 감지: HTTP Header(`Accept-Language`) 또는 기타 방식을 통한 사용자 언어 감지¹². - 클라이언트 측 번역 효율화: 라우팅에 의존하지 않는 방식으로 클라이언트 컴포넌트 번역 처리(사용자 요구사항에 따라 라우팅 기반 방식 대신 클라이언트 전환 방식 선택)¹².
    > next-intl은 Next.js App Router 환경에서 다국어 지원을 통합하고, 클라이언트 사이드 국제화 전략을 유연하게 구현할 수 있도록 돕습니다¹².
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 메시지 파일 관리 및 업데이트의 번거로움. 복수 로케일 지원 시 SEO 고려사항(Next.js 기본 i18n 라우팅 미사용 시 추가 작업 필요)¹².
    - 해결 방안: 번역 관리 시스템(TMS) 활용 또는 자동화 도구 도입 고려. `generateMetadata` 함수 활용 등 Next.js 15의 SEO 기능 적극 활용¹².
  - 기술적 접근 방식: `messages` 디렉토리에 언어별 JSON 파일 관리¹². `i18n.js` 또는 `i18n.ts` 파일 설정¹². `middleware.ts`를 통한 초기 로케일 설정²¹². `NextIntlClientProvider`로 메시지 제공, 클라이언트 컴포넌트에서 `useTranslations` 훅 사용¹².

- 테스팅: Vitest (unit/integration), Playwright (E2E)

  - 선택 근거: - Vitest: Vite 기반으로 빠른 실행 속도와 개발 환경 통합 용이¹⁴. 단위 및 통합 테스트에 적합¹⁴. MSW와 연계하여 API 모킹 효율화¹⁴. - Playwright: 최신 브라우저 지원, 안정적인 실행, 자동 대기 기능으로 E2E 테스트 강력 지원¹⁵. 병렬 테스트 용이¹⁶.
    > Vitest는 Vite, Next.js 15 등 최신 스택과 높은 호환성과 빠른 수행 속도를 제공하며¹⁴, Playwright는 사용자 여정 테스트 자동화에 효과적입니다¹⁵.
  - 예상 도전 과제 및 해결 방안:
    - 도전 과제: 테스트 커버리지 확보 및 유지보수. E2E 테스트의 Flakiness(불안정성) 관리¹⁵.
    - 해결 방안: 테스트 우선 개발(Test-Driven Development) 또는 테스트 코드 작성 습관화. E2E 테스트 시나리오를 Given-When-Then 방식으로 명확히 작성¹⁵. 테스트 독립성 유지, 안정적인 선택자 사용(예: `data-testid`)¹⁵. CI/CD 파이프라인에 테스트 자동화 통합.
  - 기술적 접근 방식:
    - Vitest: 컴포넌트(`@testing-library/react` 함께 사용), 커스텀 훅, 유틸리티 함수 등 단위 테스트. MSW로 API 의존적인 통합 테스트¹⁴.
    - Playwright: 주요 사용자 플로우(로그인, 데이터 CRUD, 설정 변경 등) E2E 시나리오 작성¹⁵. 모달 상호 작용 테스트.

- 기타 필수 스택:
  - pnpm: 빠른 패키지 설치 및 효율적인 디스크 사용.
  - Zod: 런타임 데이터(폼 입력, API 응답) 유효성 검사 및 타입 추론 연계⁸.
  - Zustand: 간결하고 효율적인 클라이언트 상태 관리.
  - next-themes: 다크/라이트 모드 테마 전환 구현.
  - Tailwind CSS: 유틸리티 우선 CSS, 빠른 스타일링, shadcn/ui와 통합⁸.
  - ESLint, Prettier: 코드 스타일 및 품질 관리.
  - SWR, Fetch: 클라이언트/서버 데이터 페칭 전략⁸.

## 3. 기능 요구사항 (Functional Requirements) - 기술적 접근 상세화

### 3.1 사용자 인증 (User Authentication) - 모달 기반 구현 상세

- Auth.js를 사용하여 소셜(Google, GitHub 등) 및 이메일/비밀번호 인증을 구현합니다.
- 모달 기반 인증 플로우:
  - 기술적 접근: Next.js App Router의 Parallel Routes 및 Intercepting Routes 패턴을 활용합니다.
  - 사용자가 보호된 페이지 또는 인증이 필요한 액션(예: 글 작성)을 시도할 때, 페이지 이동 없이 현재 페이지 위에 로그인/회원가입 모달이 렌더링됩니다.
  - 파일 구조 예시: `/app/@auth/(.)signin/page.tsx` (`(.)`는 현재 레벨에서 인터셉트, `@auth`는 병렬 슬롯 이름)¹¹.
  - `app/@auth/default.tsx` 파일은 인증 모달이 없을 때 (`null` 또는 폴백 UI)를 처리합니다.
  - 인증 성공/실패 시 `next/navigation`의 `useRouter` 또는 `redirect` 함수를 활용하여 모달을 닫거나 특정 페이지로 리다이렉트합니다¹¹.
  - 인증 상태는 `auth.js`에서 제공하는 `useSession()` 훅으로 관리하며, UI 렌더링 및 접근 권한 제어에 사용합니다¹¹.
- 보안 고려사항:
  - `middleware.ts`를 사용하여 특정 경로에 대한 인증 여부를 체크하고, 미인증 시 로그인 모달 또는 페이지로 리다이렉트합니다¹⁰.
  - Auth.js의 JWT 및 Session 콜백을 활용하여 세션 객체에 필요한 사용자 정보(예: Role)를 포함시키고 관리합니다¹⁰.
  - 환경 변수(`NEXTAUTH_URL`, `NEXTAUTH_SECRET`)는 안전하게 관리합니다¹⁰. Server Actions 사용 시에도 Next.js 15의 강화된 보안 기능을 활용합니다¹.

### 3.2 데이터 관리 (Data Management) - 실시간 동기화 포함

- Supabase (PostgreSQL) 데이터베이스에 대한 CRUD 작업을 Prisma ORM을 통해 구현합니다.
- 기술적 접근:
  - 읽기 작업: Server Components에서 `async/await`로 직접 Prisma 호출. Client Components에서 SWR 훅과 API Route Handler 또는 Server Action을 통해 데이터 페칭.
  - 쓰기/수정/삭제: Form Actions 또는 API Route Handlers를 통해 Server Action에서 Prisma 호출.
  - 실시간 데이터 동기화:
    - Supabase Realtime 기능 중 Postgres Changes를 활용합니다⁶.
    - 클라이언트에서 `supabase.channel('schema-changes').on('postgres_changes', { event: '*', schema: 'public', table: 'your_table' }, payload => { ... })` 와 같은 형태로 특정 테이블의 변경 사항(INSERT, UPDATE, DELETE)을 구독합니다⁶.
    - 구독받은 변경 사항은 Zustand와 같은 상태 관리 라이브러리 또는 SWR의 mutate 함수를 활용하여 클라이언트 UI에 즉시 반영합니다.
    - 예시: 게시글 목록 페이지에서 새로운 게시글이 작성되거나 기존 게시글이 수정/삭제될 때, 페이지 새로고침 없이 목록이 업데이트됩니다. 협업 에디터 구현 시, Tiptap과 Yjs, Supabase Realtime을 연동하여 여러 사용자의 편집 내용을 실시간으로 동기화합니다⁹.
  - 도전 과제 (Realtime): 복잡한 RLS 정책 하에서 실시간 구독 필터링 및 성능 최적화. 대규모 동시 변경 발생 시 데이터 충돌 해결 및 동기화 일관성 유지 (특히 협업 편집).
  - 해결 방안 (Realtime): RLS 정책을 세밀하게 설계하고 테스트⁵. Yjs와 같은 CRDT(Conflict-free Replicated Data Type) 라이브러리를 활용하여 클라이언트 단에서 데이터 충돌을 처리하고 Supabase에는 최종 결과 또는 병합된 트랜잭션 전송⁹.

### 3.3 리치 텍스트 편집 (Rich Text Editing)

- Tiptap 에디터를 사용하여 사용자가 풍부한 형식의 콘텐츠를 작성할 수 있도록 합니다.
- 기술적 접근:
  - React 환경의 Tiptap (`@tiptap/react`) 사용. `useEditor` 훅으로 에디터 인스턴스 관리, `EditorContent` 컴포넌트로 UI 렌더링⁹.
  - 필요한 기능(볼드, 이탤릭, 목록, 이미지, 링크 등)은 Tiptap Extensions를 추가하여 활성화합니다⁹.
  - 에디터 내용은 HTML 또는 JSON 형식으로 저장 및 불러오기 합니다. JSON 형식이 ProseMirror 내부 표현에 가깝고 유연하여 추천됩니다⁹.
  - 예시 (이미지 업로드):
    - 사용자가 에디터에 이미지를 드래그 앤 드롭하거나 삽입 버튼 클릭.
    - 클라이언트에서 파일 선택 또는 드롭된 파일 처리.
    - Supabase Storage 또는 별도 스토리지 서비스에 파일 업로드.
    - 업로드 성공 시 반환된 이미지 URL을 Tiptap `Image` Extension의 커맨드를 사용하여 에디터 내용에 삽입⁹.
    - UI/UX 개선: 이미지 삽입 후 자동으로 새 단락 생성 및 커서 이동⁹.
  - Tiptap 컴포넌트 및 관련 로직은 `'use client'` 디렉티브를 명시하여 Client Component로 구현합니다⁹.
- 향후 협업 확장성: Tiptap은 Yjs와의 통합을 지원하므로, Supabase Realtime 또는 Liveblocks와 연계하여 실시간 다중 사용자 협업 편집 기능을 구현할 수 있습니다⁹.

### 3.4 국제화 (Internationalization - i18n)

- `next-intl` 라이브러리를 사용하여 애플리케이션의 UI 텍스트 및 메시지에 대한 다국어 지원을 구현합니다.
- 기술적 접근 (클라이언트 전환 방식):

  - 사용자의 언어 선택(예: 드롭다운)에 따라 클라이언트 단에서 즉시 UI 텍스트 언어를 변경합니다.
  - 번역 메시지는 언어별 JSON 파일(`messages/[locale].json`)에 저장합니다¹².
  - Client Component에서는 `useTranslations(namespace)` 훅을 사용하여 네임스페이스별 번역 메시지를 가져옵니다¹².
  - 예시 (`messages/en.json`):

  ```json
  {
    "Auth": {
      "signIn": "Sign In",
      "signUp": "Sign Up",
      "welcome": "Welcome, {name}!"
    },
    "Posts": {
      "title": "Title",
      "content": "Content"
    }
  }
  ```

  - 예시 (컴포넌트 사용):

  ```typescript
  "use client";

  import { useTranslations } from "next-intl";

  export default function SignInButton() {
    const t = useTranslations("Auth");
    return <button>{t("signIn")}</button>;
  }
  ```

  - 서버 측에서 로케일 감지 후 초기 메시지를 불러와 `NextIntlClientProvider`를 통해 클라이언트에 제공합니다¹².
  - 도전 과제: 라우팅 기반 방식(URL에 로케일 포함)을 사용하지 않으므로, 초기 로딩 시 서버에서 적절한 언어 메시지를 제공하는 전략이 필요하며 SEO에 대한 추가 고려가 요구될 수 있습니다¹².
  - 해결 방안: `middleware.ts`에서 `Accept-Language` 헤더 기반 로케일 감지 및 초기 메시지 로딩²¹². SEO를 위해 `generateMetadata` 함수에서 `createTranslator` 사용¹².

## 4. 비기능 요구사항 (Non-Functional Requirements)

### 4.1 성능 (Performance) - 최신 Next.js 15 최적화

- 빠른 로딩: Next.js 15의 기본 SSR/SSG 기능, Turbopack 개발서버¹³, 최적화된 이미지 로딩(`next/image`) 및 폰트 로딩(`next/font`).
- 데이터 페칭 최적화:
  - Next.js 15의 Async Request API 활용¹: Server Components에서 `cookies()`, `headers()`, `searchParams` 사용 시 불필요한 동적 렌더링 방지.
  - GET Route Handlers 및 `fetch` 기본 캐싱 비활성화(`cache: 'no-store'`) 고려¹: 데이터 신선도가 중요한 경우 명시적으로 캐시를 제어.
  - SWR의 Stale-while-revalidate 전략으로 클라이언트 측 데이터 업데이트 및 캐싱 효율화.
- 렌더링 성능: Server Components와 Client Components의 역할 명확히 분리. 초기 렌더링 및 데이터 페칭은 Server Components에서 수행, 상호작용이 필요한 부분만 Client Components로 전환. 코드 스플리팅 자동화.

### 4.2 확장성 (Scalability) - 데이터베이스 및 아키텍처

- 데이터베이스 확장: Supabase 기반 PostgreSQL의 Read Replicas 활용을 통한 읽기 확장⁵. 대규모 쓰기 부하는 데이터 모델링 및 쿼리 최적화⁵.
- 애플리케이션 확장: Next.js Serverless Functions(`app/api` Route Handlers, Server Actions) 활용으로 백엔드 로직 분산 및 독립적 스케일링. 모듈화된 코드베이스 설계를 통한 기능별 독립 개발/배포 용이성 확보.
- Prisma 고려사항: 대규모 스키마에서 타입 생성 오버헤드 발생 가능성 인지⁴. 필요시 ORM 대안 고려 또는 데이터 모델링/모듈 분리 전략 수립⁴.

### 4.3 코드 품질 (Code Quality) 및 타입 안전성 (Type Safety)

- TypeScript, Zod, Prisma ORM을 통해 애플리케이션 전반의 타입 안전성 확보.
- ESLint 및 Prettier 설정 강제 적용하여 코드 스타일 일관성 유지 및 잠재적 오류 방지. Next.js 15는 ESLint 9 지원¹.
- 코드 리뷰 프로세스 필수화.

### 4.4 접근성 (Accessibility)

- shadcn/ui (Radix UI 기반) 컴포넌트의 내장 접근성 기능 최대한 활용⁸.
- WCAG 2.1 AA 기준 준수 목표. 키보드 네비게이션, 적절한 ARIA 속성, 충분한 색상 대비 확보.
- 리치 텍스트 에디터(Tiptap) 콘텐츠의 접근성 고려 (예: 이미지에 alt 텍스트 포함 기능).

### 4.5 보안 (Security) - Next.js 15 기능 활용

- Auth.js 기반 안전한 인증 및 세션 관리¹⁰. `middleware.ts`를 통한 접근 제어¹⁰.
- Supabase Row Level Security (RLS) 설정으로 데이터베이스 접근 권한 세밀 제어⁵.
- Next.js 15 Server Actions 보안 강화¹: 예측 불가능한 ID 사용, dead code elimination.
- 환경 변수(`.env.local`) 및 Secret 관리는 안전한 방식으로 수행¹⁰.
- XSS, CSRF 등 일반적인 웹 보안 위협 방지 (입력값 검증: Zod 활용⁸, 적절한 헤더 설정 등).

### 4.6 테스팅 (Testing) - 자동화 체계 구축

- 단위/통합 테스트 (Vitest):
  - Vitest를 사용하여 React 컴포넌트(Testing Library 함께 사용), 커스텀 훅, 유틸리티 함수, API 호출 로직(MSW로 모킹) 등 테스트¹⁴.
  - CI/CD 파이프라인에 통합하여 코드 변경 시 자동 실행.
- E2E 테스트 (Playwright):
  - Playwright를 사용하여 사용자 로그인/로그아웃, 데이터 생성/조회/수정/삭제 플로우, 모달 상호작용 등 핵심 사용자 여정 테스트 자동화¹⁵.
  - Chromium, Firefox, WebKit 등 다양한 브라우저에서 테스트 실행¹⁵.
  - CI/CD 파이프라인에 통합.

---

## 5. 기술적 접근 방식 및 구현 예시 (Specific Implementation Details)

### 5.1 모달 기반 인증 (Parallel & Intercepting Routes)

- 개념: 메인 페이지 컨텍스트를 유지한 채 특정 라우트의 내용을 오버레이 형태로 보여주는 패턴.
- App Router 구조:
  ```
  /app
  |- @auth         // Parallel route slot for auth modals
  |  |- default.tsx // Renders null when no modal is active
  |  |- signin      // Intercepted path
  |     |- page.tsx // Sign In modal content
  |- (dashboard)   // Route group (optional, logical grouping)
  |  |- page.tsx   // Dashboard page (main content)
  |- layout.tsx   // Root layout (includes @auth slot)
  |- page.tsx     // Home page
  ```
- 구현 예시 (Root Layout): `@auth` 슬롯을 포함하여 병렬 라우트가 렌더링될 위치 지정.

  ```tsx
  import type { Metadata } from "next";
  import { Inter } from "next/font/google";
  import "./globals.css";

  const inter = Inter({ subsets: ["latin"] });

  export const metadata: Metadata = {
    title: "Next.js 15 App",
    description: "Modern Fullstack App",
  };

  export default function RootLayout({
    children,
    auth, // Parallel route slot prop
  }: {
    children: React.ReactNode;
    auth: React.ReactNode;
  }) {
    return (
      <html lang="en">
        <body className={inter.className}>
          {children}
          {auth} {/* Renders the matched parallel route content (e.g., modal) */}
        </body>
      </html>
    );
  }
  ```

- 구현 예시 (Intercepting Sign-in Page): 로그인 모달 콘텐츠. `'use client'` 필요.

  ```tsx
  "use client";

  import { useRouter } from "next/navigation";
  import { Modal } from "@/components/ui/modal"; // shadcn/ui modal example
  import SignInForm from "@/components/auth/SignInForm";

  export default function InterceptedSignInPage() {
    const router = useRouter();
    const onOpenChange = (open: boolean) => {
      if (!open) {
        router.back(); // Close modal by navigating back
      }
    };

    // Render the modal component
    return (
      <Modal isOpen={true} onOpenChange={onOpenChange}>
        <SignInForm />
      </Modal>
    );
  }
  ```

- 트리거: `<Link href="/signin">` 클릭 또는 `router.push('/signin')` 호출 시, `/app/signin/page.tsx` 대신 `/app/@auth/(.)signin/page.tsx`가 인터셉트되어 모달로 렌더링됩니다.

### 5.2 실시간 데이터 동기화 (Supabase Realtime)

- 기술 스택: Supabase Realtime, Supabase Client SDK, (선택) Zustand 또는 SWR.
- 개념: 데이터베이스의 특정 테이블 변경(INSERT, UPDATE, DELETE) 이벤트를 클라이언트에서 구독하고, 이벤트 발생 시 UI를 즉시 업데이트. Row Level Security (RLS)를 통해 사용자가 접근 권한이 있는 데이터 변경만 수신하도록 제어⁵.
- 구현 예시 (클라이언트 구독):

  ```typescript
  "use client";

  import { useEffect } from "react";
  import { createClient } from "@supabase/client";
  import { useStore } from "@/store"; // Example using Zustand

  const supabase = createClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  );

  export function RealtimePostsListener() {
    const addPost = useStore((state) => state.addPost);
    const updatePost = useStore((state) => state.updatePost);
    const removePost = useStore((state) => state.removePost);

    useEffect(() => {
      const channel = supabase
        .channel("public:posts") // Subscribe to changes in the 'posts' table
        .on(
          "postgres_changes",
          { event: "*", schema: "public", table: "posts" },
          (payload) => {
            console.log("Change received!", payload);
            // Update local state based on the event type and payload data
            if (payload.eventType === "INSERT") {
              addPost(payload.new);
            } else if (payload.eventType === "UPDATE") {
              updatePost(payload.new);
            } else if (payload.eventType === "DELETE") {
              removePost(payload.old.id);
            }
          }
        )
        .subscribe(); // Start subscribing

      // Cleanup function
      return () => {
        supabase.removeChannel(channel);
      };
    }, [addPost, updatePost, removePost]);

    return null; // This component doesn't render anything
  }
  ```

- 통합: 위 컴포넌트를 메인 레이아웃 또는 데이터 실시간 업데이트가 필요한 페이지에 포함시킵니다. RLS 설정이 필수입니다.
- 고려사항: 대규모 동시 변경 발생 시 네트워크 부하 및 클라이언트 상태 관리 복잡성 증가. 협업 편집 등 복잡한 시나리오에서는 Yjs와 같은 CRDT 라이브러리와 Supabase Realtime을 함께 사용하는 것을 고려합니다⁹.

### 5.3 리치 텍스트 에디터 (Tiptap)

- 기술 스택: Tiptap (`@tiptap/react`), Tiptap Extensions, (선택) Supabase Storage 또는 외부 스토리지 서비스.
- 개념: 사용자 입력을 받아 HTML/JSON 형식의 구조화된 콘텐츠로 변환하고, 다양한 서식 지정 및 미디어 삽입 기능 제공.
- 구현 예시 (기본 에디터 컴포넌트):

  ```tsx
  "use client";

  import { useEditor, EditorContent } from "@tiptap/react";
  import StarterKit from "@tiptap/starter-kit";
  import Placeholder from "@tiptap/extension-placeholder";
  // Import other extensions like Image, Link, etc.

  import "./TiptapEditor.css"; // Basic styles

  export default function TiptapEditor({
    content,
    onUpdate,
    placeholder = "Write something...",
  }) {
    const editor = useEditor({
      extensions: [
        StarterKit, // Basic formatting: bold, italic, lists, etc.
        Placeholder.configure({ placeholder }),
        // Add other extensions here (e.g., Image, Link)
      ],
      content: content, // Initial content (HTML or JSON)
      onUpdate: ({ editor }) => {
        // Call a prop function to pass the updated content
        // console.log(editor.getHTML());
        // console.log(editor.getJSON());
        onUpdate(editor.getJSON()); // Or getHTML() depending on storage format
      },
    });

    // Basic menu bar (implement separately using editor.commands)
    if (!editor) {
      return null;
    }

    return (
      <div>
        {/* Implement menu bar here using editor commands (e.g., editor.commands.toggleBold()) */}
        <EditorContent editor={editor} />
      </div>
    );
  }
  ```

- 데이터 저장: `onUpdate` 콜백에서 받은 JSON 또는 HTML 데이터를 부모 컴포넌트 상태에 저장하거나 API 호출을 통해 데이터베이스(Prisma/Supabase)에 저장합니다.
- 이미지 업로드 연동: Tiptap `Image` Extension을 추가하고, 별도의 파일 업로드 로직(Input, Drag/Drop 핸들러)을 구현하여 Supabase Storage에 업로드 후 `editor.commands.setImage({ src: imageUrl })` 커맨드를 호출하여 에디터에 삽입합니다⁹.
- 고려사항: Tiptap 컴포넌트는 클라이언트 전용이므로 반드시 `'use client'` 디렉티브를 사용합니다⁹. 서버에서 에디터 콘텐츠를 표시할 때는 Tiptap 대신 HTML 렌더링 라이브러리나 Markdown 변환 라이브러리를 사용합니다.

### 5.4 국제화 (next-intl)

- 기술 스택: next-intl, React Context, JSON 메시지 파일.
- 개념: 언어별 메시지 파일을 로드하고 React Context를 통해 앱 전역에 제공하여 클라이언트 컴포넌트에서 번역된 텍스트를 쉽게 사용. 라우팅 변경 없이 언어 전환.
- App Router 구조:
  ```
  /app
  |- [locale]       // Optional: if using routing strategy (not in this PRD based on user req)
  |  |- layout.tsx // Provides NextIntlClientProvider
  |  |- page.tsx   // Page content
  |- messages      // Stores JSON message files
  |  |- en.json
  |  |- ko.json
  |- i18n.ts       // Configures locales and message loading
  |- middleware.ts // Detects locale and redirects (optional based on user req)
  |- (app content) // Main app pages
  ```
- 구현 예시 (i18n.ts): 메시지 파일 로드 및 로케일 유효성 검사.

  ```typescript
  import { getRequestConfig } from "next-intl/server";

  // Supported locales
  const locales = ["en", "ko"];

  export default getRequestConfig(async ({ locale }) => {
    // Validate that the incoming `locale` is one of our supported locales
    if (!locales.includes(locale as any)) {
      throw new Error(`Unsupported locale: ${locale}`);
    }

    // Load translated messages
    return {
      messages: (await import(`./messages/${locale}.json`)).default,
    };
  });
  ```

- 구현 예시 (Root Layout 또는 relevant Layout): `NextIntlClientProvider` 설정.

  ```tsx
  import { NextIntlClientProvider } from "next-intl";
  import { getMessages } from "next-intl/server";

  export default async function LocaleLayout({ children, params: { locale } }) {
    // Providing all messages to the client side
    const messages = await getMessages();

    return (
      <html lang={locale}>
        <body>
          <NextIntlClientProvider messages={messages}>
            {children}
          </NextIntlClientProvider>
        </body>
      </html>
    );
  }
  ```

- 구현 예시 (Client Component): `useTranslations` 훅 사용.

  ```tsx
  "use client";

  import { useTranslations } from "next-intl";

  export default function Greeting({ name }: { name: string }) {
    const t = useTranslations("Auth"); // Use 'Auth' namespace from messages.json

    return <p>{t("welcome", { name })}</p>; // Use message key with variable
  }
  ```

- 언어 전환 UI: 클라이언트 컴포넌트에서 버튼 클릭 등으로 로케일 상태를 변경하고, 해당 로케일에 맞는 메시지를 다시 로드하는 로직 구현. (예: Context API 또는 Zustand 사용)

---

## 6. 결론 (Conclusion)

본 PRD는 Next.js 15 App Router 기반의 풀스택 애플리케이션 구축을 위한 상세한 기술 요구사항 및 접근 방식을 제시합니다. Turbopack을 통한 개발 생산성, TypeScript/Zod/Prisma를 통한 타입 안전성, shadcn/ui/Tiptap을 통한 고품질 UI, Auth.js/Supabase Realtime을 통한 동적 기능 구현, Vitest/Playwright를 통한 품질 보증 등 각 기술 스택의 장점을 극대화하는 통합 아키텍처를 설계합니다. 특히, App Router의 병렬/인터셉트 라우트를 활용한 모달 기반 인증과 Supabase Realtime을 활용한 실시간 데이터 동기화는 현대적인 웹 애플리케이션의 핵심 사용자 경험을 제공할 것입니다. 제시된 도전 과제 및 해결 방안은 실제 개발 과정에서의 잠재적 위험을 관리하고 효율적인 구현을 위한 가이드라인을 제공합니다. 본 문서를 기반으로 성공적인 프로젝트 개발을 진행할 수 있을 것으로 기대합니다.

---

References

[1] Next.js 15 - https://nextjs.org/blog/next-15
[2] Next.js 15의 새로운 기능: 주요 특성과 업데이트 - Apidog - https://apidog.com/kr/blog/next-js-15-what-is-new-2/
[3] Next.js 15 핵심 정리 - https://www.heropy.dev/p/n7JHmI
[4] 경고: 대규모 프로젝트에 Prisma를 사용하기 전에 다시 생각해보세요 - https://www.reddit.com/r/nextjs/comments/1i9zvyy/warning_think_twice_before_using_prisma_in_large/?tl=ko
[5] Supabase는 얼마나 확장성이 좋아요? - Reddit - https://www.reddit.com/r/Supabase/comments/u8dzc5/how_scalable_is_supabase/?tl=ko
[6] Supabase Realtime 이용해서 실시간 알림기능 구현하기 - velog - https://velog.io/@skdbsqls/230915-Supabase-realtime-%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C-%EC%8B%A4%EC%8B%9C%EA%B0%84-%EC%95%8C%EB%A6%BC%EA%B8%B0%EB%8A%A5-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0
[7] Prisma를 이용한 데이터베이스 작업 - 안동민 개발노트 - https://andongmin.com/docs/nest/ch3/ch3-3
[8] shadcn/ui 의 해부 - 조영제 - https://siosio3103.medium.com/shadcn-ui-%EC%9D%98-%ED%95%B4%EB%B6%80-ebd469c34614
[9] 2025년엔 어떤 Rich Text 편집기 프레임워크를 선택해야 할까? - https://x.com/GeekNewsHada/status/1888831065005129758
[10] Auth.js(NextAuth.js) 핵심 정리 - https://www.heropy.dev/p/MI1Khc
[11] [번역] Next.js13 App Router - Routing - Parallel Routes - https://velog.io/@glassfrog8256/%EB%B2%88%EC%97%AD-Next.js13-App-Router-Routing-Parallel-Routes
[12] Next.js를 사용해서 다국어 지원하는 방법 - next-intl, next app router - https://surviveasdev.tistory.com/entry/Nextjs%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%84%9C-%EB%8B%A4%EA%B5%AD%EC%96%B4-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-next-intl-next-app-router
[13] Turbopack – Nextjs 한글 문서 - https://nextjs-ko.org/docs/architecture/turbopack
[14] vitest + msw 로 변경에 유연한 단위 / 통합 테스트코드 작성하기 - https://velog.io/@brince/vitest-msw-%EB%A1%9C-%EB%B3%80%EA%B2%BD%EC%97%90-%EC%9C%A0%EC%97%B0%ED%95%9C-%EB%8B%A8%EC%9C%84-%ED%86%B5%ED%95%A9-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0
[15] 사용자 동작을 중심으로 Playwright로 E2E 테스트 작성하기 - Part 1 - https://onepredict.github.io/playwright-e2e-part1/
[16] Playwright로 E2E 테스트 작성하기 - TOAST UI - https://ui.toast.com/posts/ko_20210818/
# hello-nextjs15-boilerplate
