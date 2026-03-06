# plan.md - 기술 설계서

## 1. 폴더 구조

```
blog-writer/
├── public/                    # 정적 파일
│   └── dino-logo.svg          # DINO 로고
├── src/
│   ├── app/                   # Next.js App Router (페이지)
│   │   ├── layout.tsx         # 공통 레이아웃
│   │   ├── page.tsx           # API 키 입력 (첫 화면)
│   │   ├── write/
│   │   │   └── page.tsx       # 글 작성 (메인 페이지)
│   │   ├── guide/
│   │   │   └── page.tsx       # 사용 가이드
│   │   └── api/               # API Routes (서버)
│   │       ├── validate-key/
│   │       │   └── route.ts   # API 키 유효성 검증
│   │       ├── generate/
│   │       │   └── route.ts   # 글 생성 (스트리밍)
│   │       └── regenerate/
│   │           └── route.ts   # 재생성 (스트리밍, 텍스트만)
│   ├── components/            # React 컴포넌트
│   │   ├── auth/
│   │   │   └── ApiKeyForm.tsx       # API 키 입력 폼
│   │   ├── upload/
│   │   │   ├── PhotoUploader.tsx    # 사진 업로드 (드래그 앤 드롭)
│   │   │   ├── PhotoCard.tsx        # 개별 사진 카드 (썸네일 + 메모)
│   │   │   └── PhotoGrid.tsx        # 사진 그리드 (순서 변경)
│   │   ├── settings/
│   │   │   ├── CategorySelect.tsx   # 카테고리 선택
│   │   │   ├── ToneSelect.tsx       # 톤/말투 선택
│   │   │   ├── LengthSelect.tsx     # 글 길이 선택
│   │   │   ├── LanguageSelect.tsx   # 언어 선택
│   │   │   ├── KeywordInput.tsx     # 검색 키워드 입력
│   │   │   └── FreeTextInput.tsx    # 자유 서술 입력
│   │   ├── editor/
│   │   │   ├── BlockEditor.tsx      # 사진 블록 단위 에디터
│   │   │   ├── TextEditor.tsx       # 일반 텍스트 에디터 (사진 없을 때)
│   │   │   └── EditorBlock.tsx      # 개별 블록 (사진 + 텍스트)
│   │   ├── preview/
│   │   │   ├── BlogPreview.tsx      # 블로그 미리보기
│   │   │   └── CopyButtons.tsx      # 복사 버튼 (HTML/텍스트)
│   │   ├── chat/
│   │   │   └── FeedbackChat.tsx     # 재생성 피드백 대화
│   │   ├── common/
│   │   │   ├── DinoLoading.tsx      # DINO 로딩 애니메이션
│   │   │   ├── CostEstimate.tsx     # 예상 비용 표시
│   │   │   ├── TopicMemo.tsx        # 전체 주제 메모 입력
│   │   │   └── ErrorBoundary.tsx    # 에러 바운더리
│   │   └── layout/
│   │       ├── Header.tsx           # 헤더 (네비게이션)
│   │       └── Section.tsx          # 섹션 구분 카드
│   ├── lib/                   # 유틸리티 / 비즈니스 로직
│   │   ├── claude.ts          # Claude API 호출 (배치 처리, 스트리밍)
│   │   ├── image.ts           # 이미지 리사이즈, base64 변환
│   │   ├── prompt.ts          # 프롬프트 조합 로직
│   │   ├── cost.ts            # 비용 추정 계산
│   │   ├── copy.ts            # HTML/텍스트 복사 유틸
│   │   └── validation.ts      # 입력 유효성 검사
│   ├── prompts/               # 카테고리별 프롬프트 파일
│   │   ├── restaurant.txt     # 맛집
│   │   ├── daily.txt          # 일상
│   │   ├── travel.txt         # 여행
│   │   ├── review.txt         # 제품리뷰
│   │   └── promotion.txt      # 홍보
│   ├── types/                 # TypeScript 타입 정의
│   │   └── index.ts
│   └── styles/                # CSS
│       └── globals.css
├── __tests__/                 # 테스트 파일
│   ├── unit/                  # 단위 테스트
│   │   ├── image.test.ts
│   │   ├── prompt.test.ts
│   │   ├── cost.test.ts
│   │   ├── copy.test.ts
│   │   └── validation.test.ts
│   ├── components/            # 컴포넌트 테스트
│   │   ├── ApiKeyForm.test.tsx
│   │   ├── PhotoUploader.test.tsx
│   │   ├── PhotoGrid.test.tsx
│   │   ├── CategorySelect.test.tsx
│   │   ├── ToneSelect.test.tsx
│   │   ├── BlockEditor.test.tsx
│   │   ├── BlogPreview.test.tsx
│   │   ├── CopyButtons.test.tsx
│   │   ├── FeedbackChat.test.tsx
│   │   └── DinoLoading.test.tsx
│   └── api/                   # API Route 테스트
│       ├── validate-key.test.ts
│       ├── generate.test.ts
│       └── regenerate.test.ts
├── package.json
├── tsconfig.json
├── next.config.js
├── jest.config.js
├── .gitignore
├── .env.example               # 환경변수 예시 (API 키 등)
├── CLAUDE.md
├── README.md
├── spec.md
├── plan.md
└── progress.md
```

## 2. 핵심 타입 정의

```typescript
// src/types/index.ts

// 사진 데이터
interface PhotoItem {
  id: string;              // 고유 ID
  file: File;              // 원본 파일
  preview: string;         // 썸네일 URL (URL.createObjectURL)
  base64: string;          // 리사이즈된 base64 (API 전송용)
  mimeType: string;        // 이미지 MIME 타입 (image/jpeg, image/png, image/webp) — Claude API 필수
  memo: string;            // 사진별 메모 (선택)
  order: number;           // 순서
}

// 글 설정
interface WriteSettings {
  category: Category;
  tone: ToneOption;
  customTone: string;      // 자유 입력 톤 (tone이 'custom'일 때)
  length: 'short' | 'medium' | 'long';
  language: string;
  keywords: string;        // 검색 키워드
  freeText: string;        // 자유 서술
}

// 카테고리
type Category = 'restaurant' | 'daily' | 'travel' | 'review' | 'promotion';

// 톤 옵션
type ToneOption = 'eumseum' | 'haeyo' | 'hapsyo' | 'banmal' | 'friendly' | 'custom';
// eumseum=음슴체, haeyo=해요체, hapsyo=합쇼체, banmal=반말, friendly=친근한 존댓말

// 생성 결과
interface GeneratedPost {
  title: string;
  blocks: PostBlock[];     // 사진 블록 단위
  hashtags: string[];
}

// 글 블록 (사진 + 텍스트)
interface PostBlock {
  id: string;
  photoId: string | null;  // 사진 없는 블록도 가능 (인트로/마무리)
  text: string;
}

// 대화 메시지 (재생성용)
interface ChatMessage {
  role: 'user' | 'assistant';
  content: string;
}
```

## 3. 데이터 흐름

```
[사용자 입력]
     │
     ▼
사진 업로드 → 클라이언트 리사이즈(1568px) → base64 변환
     │
     ▼
전체 주제 메모 + 사진별 메모 + 설정값
     │
     ▼
프롬프트 조합 (카테고리 프롬프트 + 설정 + 자유 서술)
     │
     ▼
예상 비용 계산 → 사용자 확인
     │
     ▼
[클라이언트에서 배치 분할] (Vercel body 4.5MB 제한 대응)
     │
     ▼
배치 1: 클라이언트 → /api/generate (사진 1~10) → Claude API (스트리밍)
     │                                                │
     │                                                ▼
     │                                           실시간 출력 → 사용자 화면
     │
     ▼
클라이언트에서 배치 1 결과 보관
     │
     ▼
배치 2: 클라이언트 → /api/generate (사진 11~20 + 이전 요약) → Claude API
     │
     ▼
... 반복 ...
     │
     ▼
최종 결과 → GeneratedPost 구조로 파싱
     │
     ▼
[에디터] → 사진 블록 단위 편집
     │
     ▼
[미리보기] → 블로그 스타일 렌더링
     │
     ▼
[복사] → HTML 또는 텍스트 (사진 위치 placeholder 포함)
```

## 4. 상태 관리

- **React useState + Context API** 조합
- `ApiKeyContext`: API 키 상태 (sessionStorage 연동)
- 글 작성 페이지 내 상태는 `useState`로 로컬 관리 (사진 목록, 설정값, 생성 결과, 대화 히스토리)
- 페이지 간 공유가 필요한 상태만 Context 사용
- **에러 바운더리**: `src/components/common/ErrorBoundary.tsx` — 컴포넌트 렌더링 에러를 잡아서 "문제가 발생했습니다" 화면 표시, 앱 전체 크래시 방지

## 5. 환경변수

```bash
# .env.example
# 이 프로젝트는 사용자가 직접 API 키를 입력하므로
# 서버 환경변수에 API 키를 저장하지 않음
# 아래는 개발/테스트용 설정

# (선택) 개발 시 테스트용 API 키
CLAUDE_API_KEY_FOR_TEST=sk-ant-...
```

## 6. Next.js 설정 (next.config.js)

```js
// API Route body 크기 제한 확장 (base64 이미지 전송용)
// 배치 10장 기준: 약 3~4MB
api: {
  bodyParser: {
    sizeLimit: '4.5mb'
  }
}
```

## 7. API Route 설계

### POST /api/validate-key
- **요청**: `{ apiKey: string }`
- **동작**: Claude API에 간단한 요청을 보내서 키 유효성 확인
- **응답**: `{ valid: boolean, error?: string }`

### POST /api/generate (배치 단위 생성)
- **설계 핵심**: 클라이언트가 10장씩 배치 분할하여 API를 여러 번 호출 (Vercel body 4.5MB 제한 대응)
- **요청**:
  ```json
  {
    "apiKey": "sk-...",
    "photos": [{ "base64": "...", "mimeType": "image/jpeg", "memo": "..." }],
    "topicMemo": "강남 데이트 코스",
    "settings": { "category": "restaurant", "tone": "haeyo", ... },
    "previousSummary": "",
    "batchIndex": 0,
    "totalBatches": 3
  }
  ```
- **동작**:
  1. 프롬프트 파일 로드 + 조합
  2. previousSummary가 있으면 컨텍스트에 포함
  3. Claude API 호출 (스트리밍)
  4. 마지막 배치가 아니면 요약도 함께 생성
- **응답**: `ReadableStream` (스트리밍)
- **배치 실패 시**: 클라이언트에서 해당 배치 재시도 또는 에러 표시, 이전 배치 결과는 보존

### POST /api/regenerate (재생성)
- **요청**:
  ```json
  {
    "apiKey": "sk-...",
    "chatHistory": [{ "role": "assistant", "content": "..." }, { "role": "user", "content": "이 부분 더 자세하게" }],
    "settings": { "category": "restaurant", "tone": "haeyo", ... }
  }
  ```
- **동작**: 대화 히스토리 기반으로 Claude API 호출 (사진 재전송 없음, 텍스트만)
- **응답**: `ReadableStream` (스트리밍)

## 8. 사용 라이브러리

| 용도 | 라이브러리 | 이유 |
|------|-----------|------|
| 프레임워크 | Next.js 14+ (App Router) | React 기반, API Routes 내장, Vercel 최적화 |
| 스타일링 | Tailwind CSS | 빠른 UI 개발, 클래스 기반 |
| 드래그 앤 드롭 | @dnd-kit/core + @dnd-kit/sortable | React 친화적, 가벼움, 터치 지원 |
| AI SDK | @anthropic-ai/sdk | Claude API 공식 SDK, 스트리밍 지원 |
| 이미지 리사이즈 | browser-image-compression | 클라이언트 리사이즈, 가벼움 |
| 클립보드 복사 | clipboard API (네이티브) | HTML 복사 지원, 별도 라이브러리 불필요 |
| 테스트 | Jest + React Testing Library | Next.js 공식 지원, 컴포넌트 테스트 |
| 아이콘 | lucide-react | 가벼움, 트리 쉐이킹 지원 |

## 9. 테스트 전략

### 테스트 도구
- **Jest**: 단위 테스트, API Route 테스트
- **React Testing Library**: 컴포넌트 렌더링/인터랙션 테스트
- **jest-environment-jsdom**: 브라우저 환경 시뮬레이션

### 테스트 실행 명령어
```bash
# 전체 테스트 실행
npm test

# 특정 파일만 테스트
npm test -- --testPathPattern="image.test"

# watch 모드 (파일 변경 시 자동 실행)
npm test -- --watch

# 커버리지 리포트
npm test -- --coverage
```

### 테스트 방식

#### 단위 테스트 (`__tests__/unit/`)
- **대상**: `src/lib/` 내 유틸리티 함수들
- **방식**: 순수 함수 입력/출력 검증
- **예시**:
  - `image.test.ts`: 리사이즈 로직, base64 변환, 포맷 검증
  - `prompt.test.ts`: 프롬프트 조합 결과 검증
  - `cost.test.ts`: 비용 계산 정확도 검증
  - `copy.test.ts`: HTML/텍스트 변환, placeholder 삽입 검증
  - `validation.test.ts`: 필수 입력 검증, 파일 포맷 검증

#### 컴포넌트 테스트 (`__tests__/components/`)
- **대상**: `src/components/` 내 React 컴포넌트
- **방식**: 렌더링 확인 + 사용자 인터랙션 시뮬레이션
- **예시**:
  - `ApiKeyForm.test.tsx`: 입력, 제출, 에러 표시 테스트
  - `PhotoUploader.test.tsx`: 파일 드롭, 포맷 필터링, 장수 제한 테스트
  - `PhotoGrid.test.tsx`: 순서 변경, 삭제 테스트
  - `BlockEditor.test.tsx`: 블록별 텍스트 편집 테스트
  - `CopyButtons.test.tsx`: HTML/텍스트 복사 테스트

#### API Route 테스트 (`__tests__/api/`)
- **대상**: `src/app/api/` 내 서버 엔드포인트
- **방식**: 요청/응답 검증, 에러 케이스 검증
- **예시**:
  - `validate-key.test.ts`: 유효한 키, 잘못된 키, 빈 키 테스트
  - `generate.test.ts`: 요청 구조 검증, 배치 분할 로직 테스트

### Mock 전략
- **Claude API**: `@anthropic-ai/sdk`를 mock하여 실제 API 호출 없이 테스트
- **sessionStorage**: jest 환경에서 mock 처리
- **File/Blob**: 테스트용 이미지 파일 mock 생성

## 10. 구현 Phase

> **전략**: 사용자 플로우 순서대로 기능을 먼저 완성하고, 디자인은 마지막에 한 번에 입힌다.
> 각 Phase에서는 최소한의 기본 UI만 사용하고, Phase 11에서 전체 디자인을 다듬는다.

### Phase 1: 프로젝트 초기화
- Next.js 프로젝트 생성 (App Router, TypeScript, Tailwind CSS)
- 폴더 구조 세팅
- Jest + React Testing Library 설정
- 타입 정의 (`src/types/index.ts`)
- **테스트**: 빌드 성공, 개발 서버 실행, 테스트 러너 실행 확인

### Phase 2: API 키 입력 페이지
- `ApiKeyForm` 컴포넌트
- `/api/validate-key` API Route
- sessionStorage 저장/조회 로직
- 가이드 링크, 종량제 안내 텍스트
- 인증 성공 시 `/write` 라우팅
- **테스트**: 키 입력 → 검증 → 저장 → 라우팅 흐름, 잘못된 키 에러 표시

### Phase 3: 사진 업로드
- `PhotoUploader` (드래그 앤 드롭)
- `PhotoCard` (썸네일 + 메모 입력)
- `PhotoGrid` (드래그로 순서 변경, @dnd-kit)
- `src/lib/image.ts` (리사이즈 + base64 변환)
- 포맷 제한 (jpg/png/webp), 장수 제한 (30장)
- **테스트**: 파일 업로드, 리사이즈 정상 동작, 순서 변경, 삭제, 포맷 필터링

### Phase 4: 입력 폼 + 설정
- `TopicMemo` (전체 주제 메모, 필수 검증)
- 설정 컴포넌트들 (카테고리, 톤, 길이, 언어, 키워드, 자유 서술)
- `Section` (섹션 구분 카드 UI)
- `src/lib/validation.ts`
- **테스트**: 필수 입력 검증, 설정값 변경, 유효성 검사

### Phase 5: 프롬프트 + 비용 추정
- 카테고리별 프롬프트 파일 작성 (`src/prompts/`)
- `src/lib/prompt.ts` (프롬프트 조합)
- `src/lib/cost.ts` (비용 추정)
- `CostEstimate` 컴포넌트
- **테스트**: 프롬프트 조합 결과, 비용 계산 정확도

### Phase 6: Claude API 연동 + 글 생성
- `src/lib/claude.ts` (배치 처리, 스트리밍)
- `/api/generate` API Route
- `DinoLoading` 애니메이션
- 생성 중 이탈 방지 (beforeunload)
- 응답 파싱 → `GeneratedPost` 구조
- **테스트**: 배치 분할 로직, 요약 전달, 스트리밍 응답 처리, 에러 핸들링

### Phase 7: 에디터
- `BlockEditor` (사진 블록 단위 편집)
- `TextEditor` (사진 없을 때 일반 텍스트)
- `EditorBlock` (개별 블록)
- **테스트**: 블록 텍스트 편집, 사진 유무에 따른 에디터 전환

### Phase 8: 재생성 + 대화
- `FeedbackChat` 컴포넌트
- 대화 히스토리 관리 (세션 메모리)
- AI 역질문 UI
- 재생성 시 덮어쓰기
- **테스트**: 피드백 입력 → 재생성 요청, 대화 히스토리 누적

### Phase 9: 미리보기 + 복사
- `BlogPreview` (블로그 스타일 레이아웃)
- `CopyButtons` (HTML/텍스트 복사)
- `src/lib/copy.ts` (HTML 변환, placeholder 삽입)
- **테스트**: 미리보기 렌더링, HTML/텍스트 복사 내용 검증, placeholder 포함 확인

### Phase 10: 사용 가이드
- 가이드 페이지 작성
- 헤더 네비게이션
- 전체 E2E 흐름 점검
- **테스트**: 페이지 라우팅, 네비게이션

### Phase 11: 디자인 다듬기
- 전체 UI/UX 디자인 통일 (색상, 간격, 타이포그래피)
- DINO 브랜딩 적용
- 반응형 레이아웃 (PC 우선, 모바일 대응)
- DINO 로딩 애니메이션 정교화
- 인터랙션 피드백 (hover, focus, transition)
- **테스트**: 반응형 레이아웃, 애니메이션 동작 확인

### Phase 12: 배포
- Vercel 연결 + 배포 설정
- 환경변수 세팅
- 최종 동작 확인
