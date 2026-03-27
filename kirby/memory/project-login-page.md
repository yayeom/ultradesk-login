# 프로젝트: 로그인 페이지 컨셉 디자인

> 생성일: 2026-03-26
> 최종 업데이트: 2026-03-27
> 상태: 진행 중 — A/B안 Supabase 연동 완료, Vercel 배포 준비

---

## 프로젝트 개요

- **서비스명**: UltraDesk (원격지원 SaaS)
- **목적**: 로그인/회원가입 페이지 디자인 컨셉 + 기능 연동
- **기술 스택**: Vercel + Supabase Auth
- **컨셉**: "모든 디바이스와 사람을 연결" — 추상적이고 심플한 디자인
- **산출물 위치**: `login-page/prototype/`

---

## 파일 구조

```
login-page/prototype/
├── a/index.html           ← A안 (Supabase 연동)
├── b/index.html           ← B안 (Supabase 연동)
├── login-concepts.html    ← A/B 통합본 (탭 전환, Supabase 미연동)
├── login-concepts.md      ← 컨셉 문서
├── r_logo.png             ← R 로고 소스 이미지
└── vercel.json            ← Vercel 배포 설정 (Root: login-page/prototype)
```

---

## A안 (확정 — 디벨롭 중)

### 비주얼
- **배경**: 라벤더/페리윙클 파스텔 그라데이션 (`#E4E8FF → #B8BFFF` + radial 하이라이트)
- **컬러 오브**: `#F9E7FF`(핑크) + `#D1DFFF`(블루) 3개, `mix-blend-mode: screen`, 18/22/25초 주기
- **노드 인터랙션**: idle 시 20개 힌트 노드가 R 근처에서 호흡 + 호버 시 전체 수렴
- **노드 컬러**: 90% 블루(`#2564ED`) + 5% 퍼플(`#B668FF`) + 5% 시안(`#5AEDFB`)
- **반짝임**: 별빛 트윙클 (부드러운 십자 광선, 5~10초 주기)
- **로고**: UltraDesk (블루/퍼플 노드 심볼 SVG + 다크 텍스트)

### R 로고 형성 시스템
- **소스 이미지**: 플랫 빨간 R 로고 → base64 임베드
- **감지**: `R>150, R>G×1.3, R>B×1.3`
- **배치**: 포아송 디스크 샘플링 + 시드 고정 (mulberry32, seed=42)
- **노드 수**: 로고 300개 (idle 60개) / 배경 120개
- **모션**: 형성 0.012 / 해산 0.035 + 중심 push

### 폼
- 우측 글래스모피즘 580px
- "Welcome to UltraDesk" / "Create your account" 전환
- Supabase Auth 연동 완료

## B안 (유지)

### 비주얼
- 다크 배경 (`#09090B`)
- 블루/퍼플/시안 오브 3개가 3D 궤도 회전 (행성 공전)
- 글래스모피즘 로그인 카드 (중앙)
- UltraDesk 로고: 버튼 그라데이션(`#3B82F6→#8B5CF6`) 적용

### 폼
- "Welcome to UltraDesk" / "Create your account" 전환
- Supabase Auth 연동 완료

---

## Supabase 연동 상태

| 기능 | A안 | B안 |
|------|-----|-----|
| 이메일 로그인 (signInWithPassword) | O | O |
| 회원가입 (signUp + full_name) | O | O |
| 로그인 ↔ 회원가입 토글 | O | O |
| Google OAuth (signInWithOAuth) | 코드 O, Supabase 설정 필요 | 코드 O, Supabase 설정 필요 |
| 비밀번호 찾기 (resetPasswordForEmail) | O | O |
| 세션 체크 (getSession) | O | O |
| SDK 초기화 안전 처리 (try/catch) | O | O |

### Supabase 프로젝트 설정 현황
- URL: `xxruetynrhqdpadsqboz.supabase.co`
- 이메일 회원가입: 활성화
- 이메일 자동확인: **꺼짐** (가입 후 이메일 인증 필요)
- Google OAuth: **미설정** (Supabase 대시보드에서 활성화 필요)

---

## 배포

| 플랫폼 | URL | 상태 |
|--------|-----|------|
| GitLab | `gitlab.rsupport.com/yayeom/yayeom` | push 완료 |
| GitHub | `github.com/yayeom/ultradesk-login` | push 완료 |
| Vercel | 미배포 | vercel.json 설정 완료, 연결 대기 |
| 로컬 | `http://localhost:8765/a/`, `/b/` | python3 http.server |

### Vercel 배포 설정
- Root Directory: `login-page/prototype`
- Framework: Other
- vercel.json: `/a` → `/a/index.html`, `/b` → `/b/index.html`

---

## 핵심 기술 결정 이력

| 날짜 | 결정 | 이유 |
|------|------|------|
| 03-26 | 3D 이미지 → 플랫 2D 이미지 | 그림자/반사로 색상 감지 실패 |
| 03-26 | 이미지 파일 → base64 임베드 | 로컬 HTML CORS 차단 |
| 03-26 | Math.random → 시드 고정 PRNG | 형태 재현성 확보 |
| 03-26 | WebGL 셰이더 → CSS 정적 그라데이션 | 참고 사이트 분석 결과 단순 CSS가 적합 |
| 03-26 | CSS 오브 float → 3D 궤도 회전 (B안) | 행성 공전 컨셉 요청 |
| 03-27 | 로그인만 → 로그인+회원가입 토글 | 같은 페이지 전환 (B2B SaaS 표준 패턴) |
| 03-27 | Supabase SDK 안전 처리 | SDK 실패 시 UI 토글이 안 되는 버그 수정 |

---

## Git 커밋 이력

| 커밋 | 내용 | 날짜 |
|------|------|------|
| `a085624` | A/B안 컨셉 프로토타입 초기 | 03-26 |
| `b42e169` | 배경 개선 — 정적 그라데이션 + 컬러 오브 페이드 | 03-26 |
| `d477eb1` | B안 행성 궤도 + 컨셉 문서 + 메모리 기록 | 03-26 |
| `3c27124` | UltraDesk 로고 적용 (A/B안) | 03-26 |
| `a4b8bf4` | 힌트 호흡 + 액센트 컬러 | 03-27 |
| `ddb9854` | A안 배경/폼/문구 + B안 로고/문구 업데이트 | 03-27 |
| `0c2c7d3` | 2일차 작업 기록 | 03-27 |
| `c31e87e` | A/B안 분리 + Supabase 이메일 로그인 | 03-27 |
| `d976287` | B안 Supabase 연동 | 03-27 |
| `5810052` | 파일 구조 변경 (a/index.html, b/index.html) | 03-27 |
| `5878553` | Vercel 배포 설정 추가 | 03-27 |
| `8503f07` | 회원가입 기능 추가 (로그인/회원가입 전환) | 03-27 |
| `981fbe8` | Supabase SDK 초기화 안전 처리 | 03-27 |

---

## 다음 단계

- [ ] Vercel 배포 연결
- [ ] Supabase Google OAuth 설정
- [ ] Supabase Site URL에 Vercel 도메인 추가
- [ ] 이메일 인증 테스트
- [ ] 폼 인터랙션 정교화 (로딩 스피너, 에러 상태 디자인)
- [ ] 반응형 레이아웃 (모바일)
- [ ] B안 채택/폐기 결정
