# Webiny 전수조사 & 활용 전략 정리 (KO)

> 작성일: 2026-09-28
> 작성: Claude Code (카리나 페르소나)
> 대상 레포: [webiny/webiny-js](https://github.com/webiny/webiny-js) (원본) / [bmshin94/webiny-js](https://github.com/bmshin94/webiny-js) (내 포크)
> 분석 브랜치: `claude/determined-lovelace-1s77kj`

---

## 목차

1. [이게 뭔가 (전수조사 결과)](#1-이게-뭔가-전수조사-결과)
2. [쉽게 다시 설명](#2-쉽게-다시-설명)
3. [Q&A 7문답](#3-qa-7문답)
4. [수익화 아이디어](#4-수익화-아이디어)
5. [참고 링크](#5-참고-링크)

---

## 1. 이게 뭔가 (전수조사 결과)

### 한 줄 정의

**AWS 서버리스 위에서 돌아가는, 코드로 확장하는 오픈소스 엔터프라이즈 CMS 프레임워크.**
레포 자기소개는 `AI-programmable CMS for enterprises hosting on AWS`.

### 규모

| 항목                      | 수치                          |
| ------------------------- | ----------------------------- |
| 레포 용량                 | 약 149MB                      |
| 패키지 수 (`packages/`)   | 156개                         |
| 확장 예제 (`extensions/`) | 24개                          |
| AI 스킬 문서 (`SKILL.md`) | 79개                          |
| GitHub 스타 / 포크        | 약 8,000 / 678                |
| CI 워크플로우             | 25개+                         |
| 라이선스                  | MIT (Community) + 상용 에디션 |

### 폴더 구조 해부

| 경로                      | 정체                                                                                                                      | 메모                                          |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| `packages/`               | 프레임워크 본체 156개 (`api-*`, `app-*`, `cli-*`, `pulumi-*`, `lexical-*`, `mcp`)                                         | 읽고 배우는 곳                                |
| `extensions/`             | 사용자 확장 실전 예제 (`MyApiRoute.ts`, `AdminTheme`, `saleorEcommerce`, `myLambdaFunction` 등)                           | 실무 정답지                                   |
| `skills/user-skills/`     | AI 에이전트용 지식 문서 79장 (`api/`, `admin/`, `agents/`, `infra/`)                                                      | 숨은 보물                                     |
| `ai-context/`             | `prds/`(기획서), `plans/`(구현계획), `specs/`, `code-style/`(코드 규칙)                                                   | AI 협업 방법론 공개                           |
| `.claude/`                | Claude Code 스킬 (`preflight`, `tester`, `write-a-prd`, `prd-to-plan`, `grill-me`, `backend-developer`) + `settings.json` | 워크플로우 자동화                             |
| `.mcp.json`               | MCP 서버 3개 등록: `webiny`, `stdlib`, `codegraph`                                                                        | AI 도구 연결 설정                             |
| `CLAUDE.md` / `AGENTS.md` | AI 행동 규칙서 (빌드/테스트/커밋 체크리스트 포함)                                                                         | 내가 카리나 페르소나 26줄 추가함 (`8cd5f300`) |
| `docs/`                   | 아키텍처, 컨트리뷰팅, `superpowers/`(설계 핸드오프)                                                                       |                                               |
| `.github/workflows/`      | CI/CD 25개+ (PR 댓글 `/vitest`로 전체 테스트 트리거)                                                                      | 대규모 OSS CI 교과서                          |
| `webiny.config.tsx`       | 설정을 JSX 컴포넌트로 선언 (`<Api.Extension>`, `<Admin.Extension>`)                                                       | 독특한 설계                                   |

### 구성 제품

1. **Headless CMS** — 콘텐츠 모델 + GraphQL API + 필드 단위 권한 + 다국어 + 버전관리
2. **Website Builder** — 드래그앤드롭 페이지 편집기 + Next.js SDK
3. **File Manager** — CDN 연동 에셋 관리
4. **Webiny Framework** — 위 셋을 TypeScript로 확장하는 뼈대 (핵심)

실행 환경은 **내 AWS 계정 안**: Lambda + DynamoDB + S3 + CloudFront (+ 선택적 OpenSearch).
인프라는 Pulumi(IaC)로 `yarn webiny deploy` 한 번에 프로비저닝.

### 언제 쓰나

**적합**

- 데이터를 자체 AWS에 둬야 하는 보안/컴플라이언스 요구
- 멀티테넌시가 1급 요구사항 (한 번 배포로 수천 테넌트)
- 화이트라벨로 내 제품 안에 CMS 임베드
- 설정이 아니라 코드(TypeScript)로 확장
- 팀에 TypeScript/React 역량 보유

**부적합**

- 10페이지짜리 단순 블로그 (오버킬)
- AWS 미사용 (GCP/Azure/온프레 지원 없음)
- TS/React 역량 없음
- 노코드 SaaS를 원함

### 나에게 주는 가치

1. **AI 에이전트 설계 실전 레퍼런스** — MCP 서버 구현체(`packages/mcp/src`)와 7개 AI 툴 자동설정 코드
2. **AI 협업 방법론 전체 공개** — PRD → 구현계획 → 코드스타일 → 프리플라이트 → 커밋 파이프라인
3. **대규모 TS 모노레포 아키텍처 샘플** — DI 컨테이너, 유즈케이스/프레젠터/이벤트핸들러 패턴

---

## 2. 쉽게 다시 설명

### 비유 1: 레고 공장

워드프레스가 "완성된 프라모델"이라면, Webiny는 **레고 부품 + 설계도 + 조립 로봇**이다.
뭐든 만들 수 있지만 조립할 줄 알아야 한다.

### 비유 2: 4층 건물

```
4층  extensions/     <- 내가 사는 집 (여기만 수정)
3층  webiny.config   <- 스위치 판넬 (JSX On/Off)
2층  packages/       <- 건물 골조 156개 (이미 지어짐)
1층  AWS             <- 땅 (Lambda / DynamoDB / S3)
지하  Pulumi          <- 굴착기 (자동)
```

`yarn webiny deploy` 한 번이면 지하부터 4층까지 자동으로 지어진다.

### 비유 3: Headless = 반찬가게

일반 CMS는 요리+접시+인테리어를 다 주는 음식점, Headless CMS는 요리만 주고 접시는 내가 고르는 반찬가게.
GraphQL로 데이터만 받아서 React / Next.js / Vue / 앱 어디든 담으면 된다.
(그래서 `website-builder-nextjs`, `-vue`, `-nuxt` 패키지가 따로 있다.)

### 비유 4: 멀티테넌시 = 호텔 한 채

서버 하나(호텔 1채)에 고객사 1000곳(투숙객)이 들어가는데 서로의 방은 못 본다.
데이터/회원/이미지/권한 완전 분리. 직접 만들면 6개월 걸릴 기능이 기본 제공.

### `skills/`가 왜 중요한가

AI에게 "Webiny로 GraphQL 만들어줘"라고 하면 학습 데이터에 없어서 할루시네이션이 난다.
`skills/user-skills/api/graphql-api/SKILL.md`를 읽히면 실제 문법대로 생성한다.

각 SKILL.md 상단의 frontmatter가 **자동 호출 트리거** 역할을 한다.

```yaml
---
name: webiny-project-structure
description: >
  Use this skill when the developer asks about folder structure...
---
```

### 한 문장 정리

> Webiny = AWS에 자동으로 깔리는, 코드로 뭐든 고칠 수 있는 기업용 CMS
> \+ AI가 그걸 정확히 다루게 해주는 지식 패키지 79장 세트

---

## 3. Q&A 7문답

### Q1. 설치 및 사용법

주의: 이 레포는 "쓰는 용도"가 아니라 "프레임워크를 개발하는 용도"다.

**A. 실제로 Webiny를 쓸 때 (일반적인 경우)**

```bash
# 사전 준비: Node.js 22+, Yarn, AWS 계정(프로그래밍 액세스)
npx create-webiny-project my-project
cd my-project

yarn webiny deploy        # AWS 배포 (첫 배포 5~15분)
yarn webiny info          # 어드민 URL 확인

yarn webiny watch admin   # localhost:3001 React 개발서버
yarn webiny watch api     # 로컬 Lambda 실행환경

yarn webiny destroy       # AWS 리소스 전체 삭제 (요금 차단)
```

AWS 프리티어를 넘으면 과금되므로 테스트 후 `destroy` 필수.

**B. 이 레포 자체 (프레임워크 기여/학습)**

```bash
yarn > /dev/null 2>&1
yarn build
yarn build -p @webiny/api-core --safe-replace   # 단일 패키지
yarn check -p @webiny/api-core                  # 타입체크
WEBINY_STORAGE=sql yarn test packages/api-core  # 테스트
```

함정: 스토리지 플래그 없이 테스트하면 수백 개가 **조용히 스킵되고 exit 0**이라 통과처럼 보인다 (`AGENTS.md` 명시).

**C. AI 기능만 분리해서 쓰기 (추천)**

```bash
npx webiny-mcp serve --skills=./skills
```

`packages/mcp`는 npm 패키지(`@webiny/mcp`)이며, `src/agents/`에 Cursor / Claude / Copilot / Windsurf / Kiro / Cline / OpenCode 자동설정 코드가 있다.

### Q2. 플러그인? 스킬? MCP?

전부 다 해당하지만 본체는 **프레임워크**다.

| 레이어           | 실체                               | 위치                  |
| ---------------- | ---------------------------------- | --------------------- |
| 본체             | 프레임워크/플랫폼                  | `packages/`           |
| 플러그인 시스템  | Api / Admin / Infra / Cli 4종 확장 | `extensions/`         |
| MCP 서버         | `@webiny/mcp`                      | `packages/mcp`        |
| 스킬             | SKILL.md 79장                      | `skills/user-skills/` |
| Claude Code 스킬 | preflight, tester, write-a-prd 등  | `.claude/skills/`     |

```
Webiny 프레임워크 (본체)
 └─ MCP 서버 (@webiny/mcp)   <- AI 연결 창구
     └─ Skills 79개           <- MCP가 읽어서 AI에 전달
```

MCP는 통로, 스킬은 화물, 프레임워크는 목적지.

### Q3. API 토큰이 필요한가

| 상황                               | 필요 여부 | 필요한 것                                      |
| ---------------------------------- | --------- | ---------------------------------------------- |
| 코드 읽기/학습                     | 불필요    | -                                              |
| MCP 서버 로컬 실행                 | 불필요    | 로컬 파일만 읽음                               |
| AWS 배포                           | 필요      | AWS Access Key / Secret                        |
| 프론트에서 CMS 호출                | 필요      | Webiny 발급 API Key (`extensions/MyApiKey.ts`) |
| 멀티테넌시 / RBAC 등 유료 기능     | 필요      | WCP 라이선스 키 (`packages/wcp`)               |
| Auth0 / Okta / EntraID 연동        | 필요      | 각 IdP 설정값                                  |
| AI 기능 (`ai-chat`, `ai-powerups`) | 필요      | LLM 제공사 API 키                              |

Webiny 자체에 내는 사용량 과금 토큰은 없다. 내 AWS 요금 + (선택) 상용 라이선스 구조.

### Q4. 왜 GitHub에서 유명한가

1. **틈새 정확히 공략** — "자체 호스팅 + 서버리스 + 멀티테넌시" 조합의 오픈소스가 희귀 (Strapi는 서버 관리 필요, Contentful은 SaaS 종속)
2. **레퍼런스** — Amazon, Emirates, 포춘 500, 정부기관. 수억 건 콘텐츠 / 페타바이트 에셋 운영
3. **MIT 커뮤니티 에디션만으로도 CMS + 페이지빌더 + 파일매니저 풀세트**
4. **AI 붐 정면 대응** — MCP 서버 + 스킬 79개를 공식 지원한 사실상 최초의 CMS. "AI-programmable CMS" 포지셔닝
5. **솔직한 README** — "When Not to Use Webiny" 섹션을 대놓고 둠 → 개발자 신뢰
6. **엔지니어링 문화** — AGENTS.md / CLAUDE.md, 25개+ CI, 코드스타일 문서화

### Q5. 로컬 에이전트 구축에 도움이 되는가

**매우 큰 도움이 된다. CMS를 안 써도 이 부분만으로 가치가 충분하다.**

배울 것 5가지:

1. **MCP 서버 실제 구현** — `@modelcontextprotocol/sdk` + `zod`. 코어는 `McpServer.ts`, `ConfigureMcp.ts` 두 파일
2. **멀티 툴 호환 전략** — `src/agents/`에서 설치된 AI 툴을 감지(`discover.ts`)해 각 툴 설정 파일 자동 생성
3. **스킬 포맷 설계** — frontmatter `description`을 "Use this skill when..."으로 써서 자동 호출 트리거화. 실전 샘플 79개
4. **에이전트 페르소나 분리** — `skills/user-skills/agents/`의 api-developer / admin-developer / infra-engineer / auth-specialist / website-builder-developer / full-stack-developer
5. **CodeGraph 개념** — "grep 대신 codegraph 먼저"라는 강제 규칙. 심볼/호출관계 그래프 탐색으로 정확도 ↑ 토큰 ↓

따라할 구조:

```
내_에이전트/
├── .mcp.json              # Webiny 구조 참고
├── AGENTS.md              # 빌드/테스트/커밋 규칙
├── skills/
│   └── <주제>/SKILL.md     # frontmatter 트리거 방식
└── mcp-server/            # packages/mcp 구조 참고
```

### Q6. 수익화

→ [4. 수익화 아이디어](#4-수익화-아이디어) 참고.

### Q7. React나 PHP로 만들 수 있나

**React: 이미 React다.**

- 어드민 UI 전체가 React (`app-*`)
- `webiny.config.tsx`부터 JSX
- 페이지빌더 커스텀 엘리먼트 = React 컴포넌트
- 자체 유틸: `react-composition`, `react-properties`
- 프론트: `website-builder-nextjs` / `-react` / `-vue` / `-nuxt`

**PHP: 절반만 가능하다.**

| 목표                                 | 가능 여부                                            |
| ------------------------------------ | ---------------------------------------------------- |
| PHP로 Webiny 확장 작성               | 불가능 (Lambda + TypeScript 구조)                    |
| PHP(Laravel)에서 Webiny GraphQL 호출 | 가능                                                 |
| PHP로 Webiny 유사품 자체 구현        | 이론상 가능하나 비추천 (서버리스 생태계가 Node 중심) |

권장: 기존 PHP 자산이 있으면 화면은 PHP 유지 + CMS만 Webiny. React로 간다면 풀스택 TypeScript 통일.

---

## 4. 수익화 아이디어

### Tier 1 — 즉시 가능 (자본 0)

**① AI 스킬팩 / MCP 툴체인 판매 (최우선 추천)**

- 근거: "AI가 우리 프레임워크를 못 다룬다"는 문제는 모든 프레임워크에 존재. Webiny가 그 수요를 증명함
- 상품: 도메인별(전자상거래, 의료, 세무, 국내 PG, 네이버/카카오 API) SKILL.md 패키지 + MCP 서버
- 차별점: 국내 서비스는 영어권 AI가 거의 모름 → 한국어 스킬팩은 경쟁자 부재
- 가격: 팩당 $29~99 또는 구독 $19/mo
- 난이도: 하 / 비용: 0원
- 첫 스텝: Webiny `skills/` 포맷 그대로 내가 잘 아는 도메인 스킬 10장 무료 배포 → 반응 후 유료화

**② 유료 템플릿 / 스타터킷**

- 상품: Webiny + Next.js 업종별 완성 템플릿 (병원, 학원, 부동산, 커머스)
- 가격: $49~299 또는 설치 대행 $500~
- 출발점: `extensions/saleorEcommerce`, `sampleEcommerce` 예제 존재

**③ 콘텐츠 / 교육**

- 상품: "AWS 서버리스 CMS 구축" 강의, "MCP 서버 직접 만들기" 전자책, 유튜브
- 타이밍: MCP/에이전트 한국어 자료가 거의 없는 골든타임
- 수익: 강의 1개 월 50~300만원 수준

### Tier 2 — 중간 투자 (3~6개월)

**④ 니치 SaaS (수익 잠재력 1위)**

- 멀티테넌시가 기본 제공된다는 점을 최대 활용
- 대상: 학원 관리, 병원 콘텐츠 포털, 프랜차이즈 지점별 홈페이지, 부동산 매물 CMS
- 절감 효과: 테넌트 분리 / 권한 / 파일관리 / 어드민 UI / 콘텐츠 모델링이 이미 완성 → 6~12개월 단축
- 수익: 월 구독 3~30만원 × 고객사. 예) 30곳 × 10만원 = 월 300만원, 인프라비는 Lambda라 수만원대
- 주의: 멀티테넌시는 Business Edition($79/mo) 필요. 고객 3곳이면 회수

**⑤ 프리랜서 / 에이전시**

- 포지션: "AWS 서버리스 CMS 구축" 전문. 국내에 Webiny 경험자가 거의 없어 희소성
- 단가: 구축 500~~3,000만원, 유지보수 월 50~~200만원
- 수요처: 데이터 국내 보관이 필요한 공공/의료/금융 (SaaS CMS 사용 불가 영역)

**⑥ 화이트라벨 CMS 임베드**

- 내 제품 안에 Webiny를 자체 CMS 기능처럼 내장. README가 명시한 공식 유스케이스
- 효과: CMS 기능 개발 6개월 → 2주 수준으로 단축

### Tier 3 — 장기

**⑦ Webiny 생태계 포지션 선점** — 유료 확장(Extension) 판매, 한국 시장 파트너 선점 (현재 공백)

**⑧ AI 에이전트 인프라 제품화** — `packages/mcp`의 멀티툴 자동설정 패턴을 범용화. "레포를 넣으면 MCP 서버 + 스킬 자동 생성" SaaS. `.claude/skills/webiny-skill-creator`가 이미 스킬 자동 생성 메타스킬로 존재

### 추천 실행 로드맵

```
1개월차   스킬팩 무료 배포 -> 반응 측정 (리스크 0)
2~3개월   스킬팩 유료화 + 유튜브/블로그로 인지도 확보
4~6개월   그 과정을 강의 / 템플릿으로 상품화
6~12개월  확보한 도메인 지식으로 니치 SaaS 착수
```

핵심 전략: **Webiny를 파는 게 아니라, Webiny에서 배운 AI 에이전트 노하우를 먼저 판다.**

### 리스크

- AWS 종속 (다른 클라우드 불가)
- 러닝커브 (TypeScript + React + AWS + GraphQL 동시 요구)
- 국내 인지도 낮음 → 영업 시 설득 비용 발생
- 일부 핵심 기능(멀티테넌시/RBAC/워크플로우)은 유료 라이선스

단, Tier 1 항목들은 리스크가 사실상 0이다.

---

## 5. 참고 링크

### GitHub

- 원본 레포: https://github.com/webiny/webiny-js
- 내 포크: https://github.com/bmshin94/webiny-js
- Next.js 스타터: https://github.com/webiny/website-builder-nextjs
- MCP 프로토콜 사양: https://github.com/modelcontextprotocol

### 공식 문서

- 문서: https://www.webiny.com/docs
- AI 개발 가이드: https://www.webiny.com/docs/build-with-ai/ai-assisted-development
- 확장 가이드: https://www.webiny.com/docs/core-concepts/extensions
- 학습 코스: https://www.webiny.com/learn
- 가격: https://www.webiny.com/pricing
- 커뮤니티 Slack: https://www.webiny.com/slack

### npm

- `@webiny/cli`, `@webiny/mcp`(`npx webiny-mcp serve --skills=./skills`), `create-webiny-project`

### 레포 내 필독 파일

| 파일                       | 왜 봐야 하나                   |
| -------------------------- | ------------------------------ |
| `README.md`                | 전체 개요 + When Not to Use    |
| `AGENTS.md` / `CLAUDE.md`  | AI 협업 규칙, 빌드/테스트 함정 |
| `packages/mcp/src/`        | MCP 서버 실제 구현             |
| `packages/mcp/src/agents/` | 7개 AI 툴 자동설정 패턴        |
| `skills/user-skills/`      | 스킬 79장 (포맷 레퍼런스)      |
| `ai-context/code-style/`   | 코드 규칙 작성법               |
| `extensions/`              | 확장 실전 예제 24종            |
| `webiny.config.tsx`        | JSX 기반 설정 설계             |

### 분석 환경 메모

이번 분석 세션에서 `codegraph`(실행파일 없음)와 `webiny`(연결 종료) MCP 서버가 연결에 실패해,
`CLAUDE.md`가 지정한 codegraph 대신 셸 기반 탐색(`cat` / `find` / `grep`)으로 전수조사를 수행함.
로컬에서는 두 MCP 서버를 정상 기동한 뒤 사용하는 것을 권장.
