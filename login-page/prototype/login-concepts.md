# 로그인 페이지 컨셉 디자인

> 작성: 커비 (Kirby) | 생성: 2026-03-26 | 최종 업데이트: 2026-03-27
> 상태: A/B안 Supabase 연동 완료, Vercel 배포 준비

---

## 1. 배경 및 목적

- **서비스명**: UltraDesk (원격지원 SaaS)
- **컨셉**: "모든 디바이스와 사람을 연결" — 추상적이고 심플한 대문
- **기술 스택**: Vercel + Supabase (Auth)
- **대상 사용자**: B2B 관리자, IT 담당자, 실무자

---

## 2. 파일 구조

```
login-page/prototype/
├── a/index.html           ← A안 (Supabase 연동)
├── b/index.html           ← B안 (Supabase 연동)
├── login-concepts.html    ← A/B 통합본 (탭 전환, Supabase 미연동)
├── login-concepts.md      ← 본 문서
├── r_logo.png             ← R 로고 소스 이미지
└── vercel.json            ← Vercel 배포 설정
```

---

## 3. 컨셉 비교 (A / B)

### A안 — Nodes & R (확정)

| 항목 | 내용 |
|------|------|
| **톤** | 밝고 깔끔한 라벤더/페리윙클 파스텔 |
| **배경** | `#E4E8FF → #B8BFFF` + radial 하이라이트 |
| **배경 효과** | `#F9E7FF`(핑크) + `#D1DFFF`(블루) 컬러 오브 페이드 인/아웃 (`mix-blend-mode: screen`) |
| **핵심 인터랙션** | 노드들이 Rsupport R 로고 형태로 수렴 |
| **힌트 호흡** | idle 시 20개 노드가 R 근처에서 수렴/이완 반복 |
| **반짝임** | 별빛 트윙클 (부드러운 십자 광선, 5~10초) |
| **노드 컬러** | 90% 블루(`#2564ED`) + 5% 퍼플(`#B668FF`) + 5% 시안(`#5AEDFB`) |
| **레이아웃** | 스플릿 — 좌측 비주얼 + 우측 로그인 폼 (580px) |
| **폼 스타일** | 글래스모피즘 (반투명 배경) |
| **로고** | UltraDesk (블루/퍼플 노드 심볼 SVG + 다크 텍스트) |
| **느낌** | 신뢰감, 연결, 깔끔한 B2B |

### B안 — Soft Orbs (유지)

| 항목 | 내용 |
|------|------|
| **톤** | 다크 프리미엄 |
| **배경** | `#09090B` 다크 |
| **배경 효과** | 블루/퍼플/시안 오브 3개가 3D 궤도 회전 (행성 공전) |
| **레이아웃** | 센터 — 글래스모피즘 카드 |
| **로고** | UltraDesk (버튼 그라데이션 `#3B82F6→#8B5CF6` 심볼+텍스트) |
| **느낌** | 프리미엄, 따뜻한 테크 |

---

## 4. A안 상세 사양

### 4.1 배경 시스템

```
배경 레이어 (아래→위):
1. CSS 그라데이션:
   radial-gradient(circle at 15% 25%, rgba(255,255,255,0.6), transparent 45%),
   radial-gradient(circle at 85% 65%, rgba(180,170,255,0.3), transparent 50%),
   radial-gradient(circle at 50% 85%, rgba(160,185,255,0.25), transparent 50%),
   linear-gradient(135deg, #E4E8FF, #D0D6FF, #C0C8FF, #B8BFFF)
2. 컬러 오브 레이어: #F9E7FF × 2 + #D1DFFF × 1
   - blur(80px), mix-blend-mode: screen
   - 18/22/25초 주기 페이드 인/아웃
3. 노드 캔버스 (투명 배경)
```

### 4.2 노드 시스템

| 구분 | 수량 | 동작 |
|------|------|------|
| 로고 노드 (idle) | 60개 (그 중 20개 힌트) | 항상 보임, 힌트 노드는 R 근처 호흡 |
| 로고 노드 (hidden) | 240개 | 호버 시 페이드인 → R 형태로 수렴 |
| 배경 노드 | 120개 | 항상 떠다님, 불투명도 85% |

- **노드 컬러**: 90% `#2564ED` + 5% `#B668FF`(퍼플) + 5% `#5AEDFB`(시안)
- **연결선**: 거리 85~125px 내 자동 연결, 투명도 거리 비례

### 4.3 R 로고 형성

- **소스**: 플랫 빨간 R 로고 이미지 → base64 임베드
- **감지**: `R>150, R>G×1.3, R>B×1.3`
- **배치**: 포아송 디스크 샘플링 (균일 간격)
- **재현성**: 시드 고정 PRNG (mulberry32, seed=42)
- **형성 속도**: 진입 0.012 (3~4초) / 해산 0.035 (1~2초) + 중심 push

### 4.4 힌트 호흡 (Hint Breathing)

- idle 노드 중 20개가 R 형태 근처에서 느슨하게 수렴/이완 반복
- sin 파형, 0↔25% 인력, ~8초 주기
- 자유 이동 속도 30% 감소 (R 근처에 더 오래 머무름)
- 호버 시 자연스럽게 전체 수렴으로 전환

### 4.5 반짝임 효과 (Star Twinkle)

- **방식**: 부드러운 sin² 파형, 천천히 밝아졌다 어두워짐
- **주기**: 5~10초
- **시각**: 밝은 화이트블루 글로우 + 수평/수직 십자 광선
- **갯수**: idle ~3개, bg ~6개, formed ~5개

### 4.6 로그인/회원가입 폼

```
[UltraDesk 로고 — 블루/퍼플 노드 심볼 + 텍스트]

=== Sign in 모드 (기본) ===
Welcome to UltraDesk
Connect to any device, anywhere
[Email]
[Password]
[Forgot password?]
[Continue] — gradient #5B8DEF → #7E6CF0
── or ──
[Sign in with Google]
Don't have an account? [Create account] ← 클릭 시 전환

=== Create account 모드 ===
Create your account
Start connecting to any device
[Name]
[Email]
[Password]
[Confirm Password]
[Create account]
── or ──
[Sign in with Google]
Already have an account? [Sign in] ← 클릭 시 전환
```

---

## 5. B안 상세 사양

### 5.1 배경 시스템

```
배경: #09090B (거의 검정)
오브 3개가 3D 궤도로 공전:
  - 블루 (#3B82F6): 반경 280px, 25초, 기울기 60도
  - 퍼플 (#8B5CF6): 반경 220px, 35초 역방향, 기울기 45도+30도
  - 시안 (#06B6D4): 반경 320px, 30초, 기울기 70도-20도
perspective: 800px, transform-style: preserve-3d
```

### 5.2 로그인/회원가입 폼

```
글래스모피즘 카드 (중앙 배치)
  backdrop-filter: blur(40px)
  border: 1px solid rgba(255,255,255,0.1)

[UltraDesk 로고 — 그라데이션 #3B82F6→#8B5CF6 심볼+텍스트]

=== Sign in / Create account 토글 (A안과 동일 구조) ===
다크 테마 메시지 색상: 성공 #93C5FD / 에러 #FCA5A5
```

---

## 6. Supabase 연동 상태

| 기능 | A안 | B안 |
|------|-----|-----|
| 이메일 로그인 (signInWithPassword) | O | O |
| 회원가입 (signUp + full_name) | O | O |
| 로그인 ↔ 회원가입 토글 | O | O |
| Google OAuth (signInWithOAuth) | 코드 O, Supabase 설정 필요 | 코드 O, Supabase 설정 필요 |
| 비밀번호 찾기 (resetPasswordForEmail) | O | O |
| 세션 체크 (getSession) | O | O |
| SDK 초기화 안전 처리 (try/catch) | O | O |

### Supabase 프로젝트 설정
- URL: `xxruetynrhqdpadsqboz.supabase.co`
- 이메일 자동확인: **꺼짐** (가입 후 이메일 인증 필요)
- Google OAuth: **미설정**

---

## 7. 배포

| 플랫폼 | URL | 상태 |
|--------|-----|------|
| GitLab | `gitlab.rsupport.com/yayeom/yayeom` | push 완료 |
| GitHub | `github.com/yayeom/ultradesk-login` | push 완료 |
| Vercel | 미배포 | vercel.json 설정 완료, Root: `login-page/prototype` |

---

## 8. 기술 결정 이력

| 날짜 | 결정 | 이유 |
|------|------|------|
| 03-26 | 3D 이미지 → 플랫 2D 이미지 | 그림자/반사로 색상 감지 실패 |
| 03-26 | 이미지 파일 → base64 임베드 | 로컬 HTML CORS 차단 |
| 03-26 | Math.random → 시드 고정 PRNG | 형태 재현성 확보 |
| 03-26 | WebGL 셰이더 → CSS 정적 그라데이션 | 참고 사이트 분석 결과 단순 CSS가 적합 |
| 03-26 | CSS 오브 float → 3D 궤도 회전 (B안) | 행성 공전 컨셉 요청 |
| 03-27 | 로그인만 → 로그인+회원가입 토글 | 같은 페이지 전환 (B2B SaaS 표준 패턴) |
| 03-27 | Supabase SDK try/catch 감싸기 | SDK 실패 시 UI 토글 안 되는 버그 수정 |

---

## 9. 참고 자료

- **배경 톤 참고**: Toss Insight 파스텔 블루 그라데이션
- **R 로고**: Rsupport 공식 로고 (플랫 2D 빨간색)
- **와이어프레임 R**: 3D 구리색 노드/선 와이어프레임 렌더링

---

## 10. 다음 단계

- [ ] Vercel 배포 연결
- [ ] Supabase Google OAuth 설정
- [ ] Supabase Site URL에 Vercel 도메인 추가
- [ ] 이메일 인증 테스트
- [ ] 폼 인터랙션 정교화 (로딩 스피너, 에러 상태 디자인)
- [ ] 반응형 레이아웃 (모바일)
- [ ] V0로 프로덕션 수준 정제
- [ ] B안 채택/폐기 결정
