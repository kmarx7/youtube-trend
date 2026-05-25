# YouTube Trend Report — CLAUDE.md

## 프로젝트 개요

- **서비스명**: YouTube Trend Report (유튜브 트렌드 분석)
- **목적**: 유튜브 크리에이터를 위한 트렌드 분석 · 키워드 리서치 · 채널 최적화 SPA
- **대상**: 한국어 사용자 (UI 전체 한국어)
- **배포**: GitHub → `https://github.com/kmarx7/youtube-trend`
- **로컬 서버**: `http://localhost:8787`

---

## 파일 구조

```
youtube_trend/
├── index.html        ← 유일한 소스 파일 (전체 앱이 여기에)
├── test-results/     ← Playwright 스크린샷 (커밋 안 함)
└── CLAUDE.md
```

> **단일 파일 SPA** — index.html 하나에 HTML · CSS · JS 전부 포함.
> 새 파일을 추가하지 말고 index.html만 수정한다.

---

## 기술 스택

- **프론트엔드**: Vanilla JS (프레임워크 없음), CSS (oklch 색상, CSS 변수)
- **YouTube API**: YouTube Data API v3 (`videos`, `search`, `channels`, `playlistItems`)
- **AI API**: OpenAI Chat Completions (`gpt-4o-mini`)
- **로컬 저장소**: localStorage (설정·모니터링 키워드)
- **테스트**: Playwright (Node.js)

---

## 핵심 코드 패턴

### 전역 객체
- `API` — IIFE 패턴, YouTube + OpenAI API 호출 전담
- `Gen` — 시드 기반 목 데이터 생성
- `VidRegistry` — 영상 카드 클릭 위임용 전역 맵 `{key: videoObj}`
- `VidModal` — 영상 재생 모달 (`open / close / setTitle / setBody`)
- `MonitorStore` — 모니터링 키워드 localStorage 관리
- `SettingsCfg` — API 키 · 설정 localStorage 관리
- `App` — 라우팅 · 지역 · 뷰 상태 관리

### 주요 함수
- `videoCard(v, opts)` — `.vid-card` HTML 생성 + VidRegistry 등록
- `openVidModal(type, v)` — 영상 분석 모달 오픈
- `renderHome/Trending/Analysis/Success/...()` — 각 페이지 렌더러
- `cacheFetch(key, fn, ttl)` — 인메모리 캐시
- `parseYtId(url)` — YouTube URL에서 videoId 추출
- `esc(s)` / `escAttr(s)` — HTML 이스케이프

### AI 섹션 패턴
```js
aiSectionSkeleton('섹션ID')          // 스켈레톤 placeholder 생성
aiSectionShow('섹션ID', '제목', html) // 실제 내용으로 교체
API._ai(systemPrompt, userPrompt)    // OpenAI 호출 → JSON 반환
```

### 영상 카드 클릭 위임
```js
// data-vplay 속성으로 클릭 감지 (전역 핸들러)
videoCard(v, {}) // → <div class="vid-card" data-vplay="키">

// URL 미리보기 카드 클릭 (data-ytplay)
// → VidModal.open(title, channel, '', ytId, searchUrl)
```

---

## 보안 규칙 (절대 어기지 말 것)

- **API 키를 소스 파일에 절대 저장하지 않는다**
- 테스트 시 Playwright `context.addInitScript()`로만 키 주입
- `.env` 파일도 만들지 않는다 (단일 HTML 파일 구조)
- 커밋 전 API 키가 index.html에 없는지 확인

---

## Git 워크플로우

```bash
# 기능 추가 시작
git checkout -b feature/기능명

# 작업 중 (여러 번 가능)
git add index.html
git commit -m "한국어 커밋 메시지"

# 완료 후
git checkout master
git merge feature/기능명
git push
git branch -d feature/기능명
```

- **브랜치명**: `feature/기능명` (한국어 or kebab-case 영문)
- **커밋 메시지**: 한국어, 변경 내용 중심
- **master 직접 커밋 금지** (긴급 버그픽스 제외)

---

## 코딩 규칙

- 주석 최소화 — 코드가 자명한 경우 주석 달지 않음
- 요청하지 않은 기능 임의 추가 금지
- 요청하지 않은 기존 코드 리팩토링 금지
- UI 텍스트는 항상 한국어
- 모바일 반응형 항상 유지 (`max-width: 860px`, `480px` 브레이크포인트)
- CSS 색상은 `oklch()` 사용 (CSS 변수 `--ink`, `--bg-elev` 등 활용)

---

## 테스트 · 확인 방법

- **Playwright** Node.js 스크립트로 헤드리스/헤드풀 브라우저 테스트
- 기능 추가 후 `headless: false`로 실제 브라우저 스크린샷 확인
- **API 키 주입**: `context.addInitScript()`로 localStorage에 설정
- 스크린샷 저장 경로: `test-results/`
- 모바일 확인: `devices['iPhone 14']` (390×844)
- JS 문법 오류 체크: `node --input-type=module`로 파싱 검증

---

## 응답 규칙

- **응답 언어**: 한국어
- 작업 완료 후 브라우저로 결과 확인 (Playwright 스크린샷)
- 큰 기능은 계획 먼저 설명하고 승인 후 시작
- "git push" 요청 시: feature 브랜치 작업 중이면 master 머지 후 push

---

## 주요 화면 목록

| 뷰 ID | 화면명 |
|-------|--------|
| `home` | 홈 대시보드 |
| `trending` | 급상승 동영상 |
| `analysis` | 주제/키워드 분석 |
| `success` | 성공 패턴 분석 |
| `algorithm` | 알고리즘 인사이트 |
| `channel` | 채널 분석 |
| `compare` | 채널 비교 |
| `ideas` | 콘텐츠 아이디어 |
| `monitor` | 트렌드 모니터링 |
| `optimize` | 영상 최적화 도구 |
| `revenue` | 수익화 분석 |
| `history` | 분석 히스토리 |
| `settings` | 설정 |
