# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# currentDate
Today's date is 2026-03-06.

# 절대 규칙

1. **내가 허락할 때까지 코드 짜지 말 것** - 모든 설계와 계획을 사용자와 충분히 논의한 후, 명시적 허락을 받아야만 코드 작성 가능
2. **프로젝트 파악 시 참고 문서** - 프로젝트 방향은 `README.md`, 진행 현황은 `progress.md`, 기능 명세는 `spec.md`, 기술 설계는 `plan.md`를 참고할 것
3. **코드 작성 시 무조건 phase 단위 테스트** - 코드 구현에 들어가면 각 phase마다 반드시 테스트를 작성하고 통과해야 다음 phase로 넘어갈 것
4. **Next.js/React 문법 설명 필수** - 사용자가 Next.js 초보이므로, 코드 작성 시 문법과 구조를 왜 이렇게 쓰는지 설명하면서 진행할 것
5. **웹앱 관련 개념 설명 필수** - 웹앱 관련 문법, 포맷, 개념(API 요청, body 크기 제한, base64, 스트리밍, 배치 처리 등)을 사용할 때는 반드시 쉽게 설명하고, 사용자가 이해했는지 확인할 것

# 프로젝트 개요

사진과 짧은 메모를 입력하면 AI(Claude API)가 블로그 글을 자동 생성하는 범용 블로그 글 생성기.

## 기술 스택
- **Frontend/Backend**: Next.js (React) + API Routes
- **AI**: Claude API (Anthropic) - 이미지 + 텍스트 멀티모달
- **배포**: Vercel

## 개발 명령어

```bash
# 의존성 설치
npm install

# 개발 서버 실행
npm run dev

# 테스트
npm test

# 특정 테스트만 실행
npm test -- --testPathPattern="파일명"
```

## 핵심 문서

| 문서 | 용도 |
|------|------|
| `README.md` | 프로젝트 방향, 기능 소개 |
| `progress.md` | 진행 현황 추적 |
| `spec.md` | 기능 명세 (뭘 만들 건지) |
| `plan.md` | 기술 설계 (어떻게 만들 건지) |

## 주요 모듈 구조

- **src/app/**: Next.js App Router 페이지 (API 키 입력, 글 작성, 사용 가이드)
- **src/app/api/**: API Routes (키 검증, 글 생성 스트리밍)
- **src/components/**: React 컴포넌트 (업로더, 에디터, 미리보기, 복사, 피드백 대화)
- **src/lib/**: 유틸리티 (이미지 리사이즈, Claude API, 프롬프트 조합, 비용 추정, 복사)
- **src/prompts/**: 카테고리별 프롬프트 파일 (맛집/일상/여행/제품리뷰/홍보)
- **__tests__/**: 단위 테스트, 컴포넌트 테스트, API Route 테스트
