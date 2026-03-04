# 🦕 DINO의 뚝딱 블로그

사진과 짧은 메모만 입력하면, AI가 자연스러운 네이버 블로그 글을 뚝딱 만들어줍니다.

## 주요 기능

- **사진 업로드**: 최대 30장의 사진을 드래그 앤 드롭
- **글 스타일 선택**: 맛집 / 일상 / 여행 / 제품리뷰 / 홍보 + 커스텀 스타일
- **AI 글 생성**: 사진을 분석하고 사람처럼 자연스러운 블로그 글 작성
- **미리보기**: 완성된 글이 블로그에서 어떻게 보이는지 예시 제공
- **복사 & 발행**: 제목 / 본문 / 해시태그를 복사해서 네이버 블로그에 바로 붙여넣기

## 사용 방법

1. 사진을 올린다
2. 짧은 메모를 입력한다 (예: "강남 파스타 맛집, 분위기 좋았음")
3. 글 스타일을 선택한다
4. AI가 블로그 글을 생성한다
5. 미리보기로 확인하고, 필요하면 수정한다
6. 복사 버튼을 눌러 네이버 블로그에 붙여넣기한다

## 기술 스택

- **Frontend/Backend**: Streamlit
- **AI**: Claude API (Anthropic) - 이미지 + 텍스트 멀티모달
- **배포**: Streamlit Cloud

## 시작하기

```bash
# 저장소 클론
git clone https://github.com/your-username/blog-writer.git
cd blog-writer

# 패키지 설치
pip install -r requirements.txt

# 실행
streamlit run app.py
```

## 라이선스

MIT License
