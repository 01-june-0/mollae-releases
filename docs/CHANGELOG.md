# CHANGELOG

Mollae (molstagram) 의 버전별 변경사항. 최신순.

날짜는 마일스톤 완료 기준이며, 정식 배포 태그(`v*`)가 찍힌 시점과는 다를 수 있다.

---

## v0.8.5 — CHANGELOG/스크린샷 sync 자동화 (2026-06)

v0.8.4 의 two-repo split 후속 — private `mollae` 의 문서를 public `mollae-releases`
로 release 때마다 자동 동기화.

### CI (build.yml)
- 신규 \`sync-docs\` job — tag push 시:
  - private \`mollae\` (이 repo) checkout
  - public \`mollae-releases\` checkout (RELEASES_TOKEN)
  - \`docs/CHANGELOG.md\` + \`docs/screenshots/*.png\` 복사 → 변경 있으면 commit + push
- \`release\` job 의 needs 를 \`[build, sync-docs]\` 로 — sync 가 먼저 끝나 tag 가 sync 반영된 HEAD 를 가리킴
- **README 는 sync 제외** — private(개발용)·public(다운로드용) 이 의도적으로 다름

### 효과
이제 릴리즈 시 mollae-releases 의 CHANGELOG·스크린샷이 자동으로 최신화됨.
v0.8.4 까지는 수동 1회 복사였으나 v0.8.5 부터 무인 동기화.

배포 인프라 변경만 — 앱 데이터·기능 변경 없음.

---

## v0.8.4 — 소스/릴리즈 분리 (two-repo split) (2026-06)

소스 코드는 비공개로 유지하면서 릴리즈·문서는 공개하기 위한 인프라 변경.

### 구조
```
01-june-0/mollae          → PRIVATE (소스 + 히스토리)
   CI 가 빌드 → cross-repo push ↓
01-june-0/mollae-releases → PUBLIC (README · 스크린샷 · CHANGELOG · Releases)
```

### CI (build.yml)
- release job 이 \`softprops/action-gh-release@v2\` 의 \`repository: 01-june-0/mollae-releases\` + \`token: secrets.RELEASES_TOKEN\` 으로 **cross-repo push**
- Actions 기본 \`GITHUB_TOKEN\` 은 자기 repo 권한만 있어 PAT (mollae-releases Contents:write) 필요
- 태그(v*)는 mollae-releases 에 자동 생성, draft release 로 산출물 업로드

### 자동 업데이트 (electron-builder.yml)
- \`publish.repo\`: \`mollae\` → \`mollae-releases\`
- 자동 업데이트가 public 인 mollae-releases 의 latest release 를 익명 접근으로 봄
- **기존 v0.8.3 이하 설치본은 새 채널로 자동 전환되지 않음** (알파 단계라 transition 생략) — 기존 사용자는 mollae-releases 에서 한 번 수동 재설치 필요

### 마이그레이션
앱 데이터(settings/notes/PIN) 변경 없음. 배포 인프라만 변경.

---

## v0.8.3 — README hotfix (Boss Key 단축키 표기 통일) (2026-06)

v0.8.1 의 README 작성 중 disguise 별 섹션 (VSCode/Terminal/Calendar/Gmail) 4 곳에서 Boss Key 단축키를 \`Cmd+B\` 로 잘못 표기. 실제 단축키 \`\` Cmd+\` \`\` (백틱) 으로 통일.

- 단축키 표 (line 80) · 컨셉 섹션 (line 69) 은 처음부터 정확했음
- disguise 별 main+Boss 2-shot 표의 Boss 셀 4 곳만 수정 — UI 동작·코드 변경 없음

마이그레이션 없음.

---

## v0.8.2 — 스크린샷 자동 캡처 + README 실제 이미지 반영 (2026-05)

D1 의 docs 정착 후속 — README 의 이미지 reference 들을 실제 캡처본으로 채움.

### 자동 캡처 — \`scripts/capture-screenshots.mjs\`
- **Playwright + Electron** 으로 Mollae 앱을 띄우고 disguise/scenario 별로 자동 전환하며 캡처
- 임시 \`userData\` 디렉토리 사용 → 실제 사용자 settings 안 건드림
- 15 장 캡처 — 5 disguise × 3 화면 (main / Boss / Cmd+P)
- \`docs/screenshots/\` 에 \`{disguise}-{screen}.png\` 형식으로 저장
- \`npm run screenshots\` 로 한 번에 빌드 + 캡처

### Playwright devDep 추가
- \`playwright@^1.60.0\` (devDependency only — 빌드 산출물에는 안 들어감)

### README hero 섹션
- 가상 \`hero-disguises-grid.png\` 합성 이미지 reference 제거
- 실제 캡처본 5 장을 3+2 grid HTML \`<table>\` 로 직접 배치
- Excel / VSCode / Terminal (상단 3) · Calendar / Gmail (하단 2)

### 캡처 가이드 갱신 의도
\`docs/screenshots/README.md\` 의 수동 캡처 가이드는 유지 — 자동 캡처가 안 되는 환경 (특정 OS 권한·dialog 캡처 등) 에서의 폴백.

코드·persistence 변경 없으므로 마이그레이션 없음.

---

## v0.8.1 — README + 스크린샷 정착 (D1) (2026-05)

코드 변경 없음. 5 disguise 라인업 갖춰진 시점에 외부 공유용 docs 정착.

### README 전면 개편
- 5 disguise 라인업 hero 섹션 (각 disguise 의 main + Boss 2-shot)
- 페르소나 매핑 표 (사무직·개발자·데브옵스·PM·매니저)
- 단축키 표에 `Cmd+P` disguise별 분기 동작 명시
- 보안 섹션 강화 — PIN 잠금 3 트리거·master reset·데이터 저장 정책·면책
- 자동 업데이트 안내·설치 가이드 (Unsigned 우회)
- 버전별 기능 요약 표 (v0.1 ~ v0.8) + 프로젝트 구조

### 스크린샷 디렉토리 — \`docs/screenshots/\`
- 15-20 개의 스크린샷 슬롯 정의 (5 disguise × 3 화면 + 부가)
- 캡처 가이드 (\`docs/screenshots/README.md\`) — 권장 해상도·캡처 절차 (macOS/Win)·후처리·라이선스·갱신 빈도
- 파일명 규칙: \`{disguise}-{screen}.png\`
- 실제 이미지는 사용자가 직접 캡처해서 추가 (외부 공유 시점에)

코드·persistence 변경 없으므로 마이그레이션도 없음.

---

## v0.8 — Google Calendar + Gmail 위장 (2026-05)

두 새 disguise 를 한 사이클로 추가 — **Google Calendar** (사무직·PM·일정 헤비) + **Gmail** (이메일 헤비·매니저·CS). 둘 다 Google 제품이라는 점을 활용해 Material Design CSS 변수 + 공통 GoogleHeader 컴포넌트를 \`shared/\` 로 빼고, 그 위에 두 disguise 를 차례로 올림. 단일 disguise 보다 30~40% 비용 절감.

타겟 페르소나 확장:
- v0.4 VSCode → 개발자
- v0.6 Terminal → 데브옵스·SRE
- **v0.8 Calendar → 사무직·PM·영업** (일정 자주 보는 직군)
- **v0.8 Gmail → 매니저·CS·세일즈** (이메일 헤비)

### GG0 — Google shared infra + 두 disguise stub

\`disguises/google/\` 디렉토리 신설:
- \`shared/GoogleHeader.tsx\` — 64px Google 헤더 (햄버거 + 로고 슬롯 + 검색바 + 9-dot + profile circle), \`WebKitAppRegion: drag\` 처리
- \`shared/dateShift.ts\` — Calendar/Gmail 공용 날짜 shift 헬퍼 (자세히는 CAL2.1 에서 활용)
- \`google/calendar/\` 와 \`google/gmail/\` 각각 chrome 4종 + scenarios

CSS 변수 (\`--gg-*\` 시리즈, 라이트·다크 모두) — Google 의 정식 톤과 동일:
\`\`\`
--gg-bg / --gg-surface / --gg-surface-alt / --gg-border
--gg-text / --gg-text-dim / --gg-text-faint
--gg-primary (Google Blue) / --gg-primary-hover
--gg-accent-red / --gg-accent-green / --gg-accent-yellow
--gg-hover-bg / --gg-shadow
\`\`\`

스키마 5종으로 확장:
- \`DisguiseId\`: \`... | 'gcalendar' | 'gmail'\` (총 5종)
- \`SheetState.scenarios\` / \`customScenarioIds\`, \`Notes\`, \`favoriteScenarios\` 모두 5 슬롯
- 마이그레이션: 누락된 두 슬롯이 default spread 로 자동 채워짐 (메모·CSV·PIN 보존)
- 5종 분기 갱신: 설정 모달 dropdown / MemoSearch 필터 chip / LockScreen primaryTitle / ShortcutHelpModal printPreview / SettingsModal 단축키 hint

### Google Calendar 4단계 (CAL1~CAL4)

**CAL1 — chrome 정교화**
- Titlebar: GoogleHeader + 컬러 27 풍 캘린더 아이콘
- Toolbar: 오늘 / ‹ / › / 월 라벨 / 월·주·일 토글 (월만 활성) / ⚙
- Body: 월간 6×7 grid (\`buildGrid(year, month)\`) — 일요일 시작, 이벤트 chip, 셀당 3개 + "+N개 더 보기"
- StatusBar: 시트 전환

\`displayState.ts\` (singleton store): 월간 displayMonth 를 Toolbar 와 Body 가 공유. \`useDisplayMonth\` 훅 + setter. React Context 대신 module-level 로 disguise abstraction 손대지 않음.

**CAL2 — 시나리오 4종**
| id | persona | 핵심 흐름 | 이벤트 수 |
|---|---|---|---|
| corporate-busy | 임원·PM | 임원회의 + 1on1 + Q2 KPI + 고객사 + 워크샵 | 19 |
| dev-sprint | 개발팀 | 매일 standup + Sprint plan/demo/retro + 1on1 | ~33 |
| consulting | 컨설턴트 | Acme/Hanwha + 부산 출장 + 제안서 deep work | 18 |
| academic | 교수·연구원 | 월수금 CS-321 + lab + ICML + 박사 졸업심사 | ~32 |

\`buildStandups\` / \`buildLectures\` / \`buildLabMeetings\` / \`buildOfficeHours\` 헬퍼로 평일·요일별 반복 이벤트 자동 생성.

**CAL2.1 (hotfix) — dateShift 자동 적용**
시나리오 데이터가 \`2026-05-27\` 로 하드코딩된 채로 두면 한두 달 지나서 캘린더에 옛 5월만 보이는 위장 깨짐. \`getCalendarScenarioById\` 가 \`anchorDateIso\` 와 실제 today 의 diff (일 단위) 만큼 모든 이벤트 ISO 를 자동 shift. Gmail 도 같은 패턴 (anchorDateIso + receivedIso shift).

\`scripts/verify-backup-roundtrip.mjs\` 와 별도로 \`dateShift.ts\` 의 unit 동작은 build-time 안전성으로 보장.

**CAL3 — Boss overlay (Meet) + Cmd+P (이벤트 검색)**
- \`GCalendarBossOverlay\`: 풀스크린 가짜 Google Meet 회의 화면
  - 9-tile video grid (3×3, 모두 카메라 off, 한 명만 speaking ring)
  - 회의 제목 = 시나리오의 "지금 진행중일 법한" 이벤트 자동 픽
  - 컨트롤 바 (음소거·카메라·자막·반응·화면공유·손들기·나가기 빨강)
  - 우측 채팅 패널 (빈 상태)
  - \`● REC mm:ss\` 1초마다 갱신 → "진짜 회의 중" 인상
- \`GCalendarEventSearch\`: Google 검색바 풍 fuzzy 모달
  - 제목 + 위치 substring 매칭
  - 시나리오 chip + 색 dot
  - 선택 시 다른 시나리오면 자동 전환 + displayMonth 도 그 이벤트 월로 점프

**CAL4 — 이벤트 메모 (Google 풍 dialog)**
- \`GCalendarEventDialog\` (440px) — 이벤트 chip 더블클릭 시 열림
- 색 dot + 제목 + 시간 + 위치 + 참석자 + **메모 textarea**
- 메모 흐름: Cmd+Enter / Tab / blur / Esc / 바깥 클릭 모두 저장
- key 형식 \`E<eventId>\`, \`notes['gcalendar'][scenario]\` 슬롯
- 메모 있는 chip 에 📝 emoji 표시
- \`focusCell\` \`E<id>\` 매칭 시 자동 dialog 열림 (MemoSearch 점프 지원)

### Gmail 4단계 (MAIL1~MAIL4)

**MAIL1 — chrome 정교화**
- Toolbar: 동적 카운트 (\`1-N / N\`) — \`activeSheetId\` + settings 구독
- Body 카테고리 탭 추가 — 기본·소셜·프로모션·업데이트 (기본 활성, 파란 3px 하단 border)
- Sidebar 확장 — 7개 라벨 (받은편지함·별표·보낸·임시·전체·스팸·휴지통), 받은편지함 활성 + unread 카운트 배지 동적
- 메일 행 single-select 토글 + group hover 효과 + 별표 색 변화
- \`formatRelativeTime\` 개선: 오늘 \`HH:MM\` / 어제 \`어제\` / 올해 \`M월 D일\` / 다른 해 \`YYYY. M. D.\`
- unread/read/selected 시각 분리 (각각 white / surface / 파란 bg)

**MAIL2 — 시나리오 4종**
| id | persona | 핵심 톤 | 메일 수 |
|---|---|---|---|
| work-busy | 사무직 (확장) | 협업·회의·HR·결재·OOO | 15 |
| saas-notifications | 개발자·DevOps | GitHub/Jira/Sentry/PagerDuty/AWS 자동 알림 | 16 |
| newsletter | 정기 구독 | Pragmatic Engineer/TechCrunch/Stripe 등 | 15 |
| sales-pipeline | 영업·CS | HubSpot/Salesforce/DocuSign + 클라이언트 응답 | 15 |

**MAIL3 — Boss overlay (compose) + Cmd+P (메일 검색)**
- \`GmailBossOverlay\`: 풀스크린 가짜 메일 작성 화면 — "메일 쓰느라 바쁨" 시그널
  - 시나리오별 답장 대상 자동 픽 (starred > unread > 첫 메일)
  - 시나리오마다 다른 작성 톤 (협업 답장 / 운영 리포트 / 메모 / 계약 응답)
  - 본문 + 깜빡이는 caret + "X초 전 임시보관함에 저장됨" 1초마다 갱신
- \`GmailMailSearch\`: Gmail 검색바 풍 fuzzy 모달
  - from / subject / preview / label 모두 매칭
  - 아바타 + 발신자 + 별표 + 시각 + 제목 + preview + 시나리오 chip
  - 선택 시 다른 시나리오면 자동 전환

**MAIL4 — 메일 메모 (Gmail 풍 dialog)**
- \`GmailMessageDialog\` (640px, max-h 80vh) — 메일 row 더블클릭 시 열림
- 상단 액션 바 (답장·전달·별표·휴지통·더보기·닫기)
- 제목 + 라벨 + 발신자 (40px 아바타) + 받는사람 + 시각
- **본문 자동 확장**: preview → 4 단락 (opening + 추가 + 인사 + 서명)
- 첨부 박스 (hasAttachment 일 때)
- 메모 textarea — 모든 dismiss 경로에서 자동 저장
- key 형식 \`M<threadId>\`, \`notes['gmail'][scenario]\` 슬롯
- 메모 있는 row 에 📝 emoji (노랑색, timestamp 옆)
- \`focusCell\` \`M<id>\` 매칭 시 자동 dialog 열림

### 마이그레이션 안전성

v0.7 → v0.8 자동:
- \`sheets[*].scenarios.gcalendar\` → \`'corporate-busy'\` 기본
- \`sheets[*].scenarios.gmail\` → \`'work-busy'\` 기본
- \`sheets[*].customScenarioIds.gcalendar\` / \`.gmail\` → \`null\` (미사용)
- \`favoriteScenarios.gcalendar\` / \`.gmail\` → \`[]\`
- \`notes.gcalendar\` / \`.gmail\` → \`{}\` (lazy create)
- 모든 메모·CSV·시트 매핑·PIN 보존
- v0.6 의 backup roundtrip 검증 (scripts/verify-backup-roundtrip.mjs) 은 5 disguise 환경에서도 통과 (Map 키만 동적)

### v0.4 → v0.8 disguise 라인업
| disguise | persona | v |
|---|---|---|
| Excel | 사무직·마케팅 | v0.1 |
| VSCode | 개발자 | v0.4 |
| Terminal | 데브옵스·SRE | v0.6 |
| **Google Calendar** | 사무직·PM·영업 | **v0.8** |
| **Gmail** | 매니저·CS·세일즈 | **v0.8** |

---

## v0.7 — 일관성 정착 (2026-05)

세 disguise (Excel / VSCode / Terminal) 를 모두 갖춘 직후, **사이사이의 일관성** 을 끌어올린 패치성 릴리즈. 사용자 입장에서 "왜 이건 disguise 따라 안 변하지?" 싶었던 자잘한 부분들을 한 번에 정리. 새 기능보다는 *기존 기능의 매끄러움* 에 집중.

### G1 — VSCode TabBar 활성 시나리오 동기화

v0.6 T3 에서 발견한 TerminalTabBar 의 활성 시나리오 동기화 패턴을 VSCodeTabBar 에도 이식.

이전:
- VSCodeTabBar 가 \`REACT_APP_SCENARIO\` 하드코딩 → 시나리오 변경 / 시트 전환 시 활성 탭의 파일명이 \`Counter.tsx\` 로 고정. 본문은 변하는데 탭 라벨이 그대로라 시각 불일치.

변경:
- \`ToolbarProps.activeSheetId\` prop 사용 + \`settings.sheets[activeSheet].scenarios.vscode\` 동기화 (T3 의 TerminalTabBar 와 동일 패턴)
- \`settings.onChange\` 구독으로 시나리오 변경 즉시 갱신
- 활성 시나리오의 \`fileTree\` 에서 sibling 파일 2개를 자동 추출해 inactive 더미 탭으로 표시 — 활성 시나리오가 Python (data-pipeline) 일 때 더미 탭이 \`App.tsx\` (.tsx) 가 아니라 같은 프로젝트의 \`.ipynb\` 가 되는 식.
- \`getSiblingFiles(scenarioId, count)\` 헬퍼 — 우선순위: 같은 폴더 → 같은 확장자 (다른 폴더) → 그 외.
- \`fileTypeLabel\` 확장 — TSX/JSX/IPY/JSON/CSS/SVG 추가.

### G2 — LockScreen primaryTitle disguise 분기

기존: 어떤 disguise 든 \`fakeFilename\` (예: "통합 문서1") 만 표시. VSCode 나 터미널일 때 어색.

| disguise | 표시 |
|---|---|
| excel | \`{fakeFilename}\` (예: "통합 문서1") |
| vscode | \`{fakeFilename} — VSCode 워크스페이스\` |
| terminal | \`{fakeFilename} — 터미널 세션\` |

\`reason\` 메시지 (autoLock / appStart / bossKey / manual) 는 트리거 설명하는 보조 라벨로 그대로 유지. App.tsx 가 \`disguise={settings.disguise}\` 전달.

### G4 — 단축키 도움말 disguise 분기

\`printPreview\` 단축키가 disguise 마다 다른 동작을 하는데 도움말이 그 사실을 묶어서 표시하고 있었음.

ShortcutHelpModal (Cmd+/) — 활성 disguise 만 보여주면 충분:
| disguise | 설명 |
|---|---|
| excel | 인쇄 미리보기 위장 — A4 페이지 형식 |
| vscode | 파일 Quick Open 모달 — 시나리오 점프 가능 |
| terminal | 명령 history 검색 모달 (fzf 풍 — Ctrl+R) |

SettingsModal 의 단축키 설정 화면 (옵션 → 단축키) — 한 줄로 세 종 다 나열 ("어떤 disguise 에서 어떤 동작에 매핑될지" 정직하게).

\`lockNow\` 설명도 v0.5 PIN 어휘로 통일 ("비밀번호" → "PIN 잠금"), \`memoSearch\` 설명에 "disguise 자동 전환" 명시.

### H1 — MemoSearch 점프 시 disguise 전환 토스트

메모 검색 결과 클릭 시 그 메모가 다른 disguise 에 속해 있으면 \`handleMemoJump\` 가 disguise 를 자동 전환하는데, 사용자 입장에선 "왜 화면이 갑자기 바뀌었지" 싶은 순간이 생김. 짧은 토스트로 해소.

- 신규 \`Toast\` 컴포넌트 (\`components/Toast.tsx\`) — 화면 하단 가운데 검은 반투명 pill + ↪ 초록 아이콘
- 신규 \`mollae-toast\` 키프레임 — 2.6s 총 길이 (fade-in 8% → 유지 80% → fade-out 12%)
- \`onAnimationEnd\` 콜백 → 자동 언마운트
- \`z-[70]\`, \`pointer-events-none\`, \`aria-live="polite"\` 접근성/UX 디테일

### H2 — backup/restore 무결성 검증

v0.6 의 terminal 데이터가 backup·restore 사이클에서 잘 살아남는지 확인. 정적 분석 + 자동화 smoke test 결과 **기존 코드가 설계상 disguise-agnostic** 으로 작동함을 확인 — 동작 코드 변경 없음.

- \`scripts/verify-backup-roundtrip.mjs\` 추가 — Node 만으로 실행되는 smoke test
- Case 1: v0.6+ 풀 데이터 라운드트립 → terminal 메모·즐겨찾기·시나리오 그대로 복원 (12개 assert)
- Case 2: v0.5 backup (terminal 슬롯 없음) 마이그레이션 → 누락 슬롯 자동 기본값 채움
- \`writeBackupIfEnabled\` jsdoc 에 설계 보장 + 검증 스크립트 경로 명시

### 회귀 영향

코드 흐름이 바뀐 곳:
- VSCodeTabBar — REACT_APP_SCENARIO 하드코딩 제거 (G1)
- LockScreen — \`disguise?\` 옵셔널 prop 추가 (G2)
- ShortcutHelpModal — \`ROWS\` 상수가 \`rowsFor(disguise)\` 함수로 변경 (G4)
- App.tsx — \`toast\` state + \`Toast\` 렌더 (H1)

마이그레이션·persistence 영향 없음. v0.6 → v0.7 자동 무중단.

---

## v0.6 — 터미널 위장 모드 (2026-05)

세 번째 disguise — **터미널 에뮬레이터** 추가. v0.4 의 DisguiseProfile 추상화 위에 새 위장을 올리는 첫 사례로, D2~D5 패턴 (chrome 컴포넌트 4종 + 시나리오 데이터 + Boss/Cmd+P 분기 + 라인 메모) 을 그대로 재활용해 비용을 절반으로 줄임.

타겟 페르소나: **데브옵스·SRE·플랫폼 엔지니어** — VSCode 모드로는 잘 안 잡히던 "터미널에 늘 띄워두고 사는" 직군. macOS / Linux / WSL 어디서나 자연스럽게 보이도록 cross-platform 중립 디자인 (Warp/Hyper/Win Terminal 류 다크 + monospace + 둥근 탭) 으로 결정.

### T1 — DisguiseProfile stub + 레지스트리

- \`DisguiseId\`: \`'excel' | 'vscode'\` → \`+'terminal'\` (renderer/preload/main/App/SettingsModal/MemoSearch 일괄 확장)
- \`SheetState.scenarios\` / \`customScenarioIds\` 에 terminal 슬롯 추가
- \`DEFAULT_SHEETS\`: 세 시트 모두 \`terminal: 'dev-workflow'\` 로 초기화
- \`Notes\` namespace 와 \`favoriteScenarios\` 에 terminal 슬롯
- v0.5 → v0.6 자동 마이그레이션: 누락된 terminal 키를 default spread 로 자동 보정. 메모·CSV·PIN 보존.
- 설정 → 일반 → 위장 dropdown 에 "터미널 에뮬레이터 (Beta)" 옵션 추가
- MemoSearch 의 disguise 필터에 "Terminal" chip + 결과 badge 라벨 분기

### T2 — Terminal chrome 4종 정교화

| 컴포넌트 | 핵심 |
|---|---|
| TerminalTitlebar | 24px 슬림 다크 + \`user@host: cwd\` 가운데 정렬 + \`WebKitAppRegion: 'drag'\` (frameless dragging) |
| TerminalTabBar | 모든 시나리오를 탭으로 렌더, 활성 탭은 하단 초록 2px + 펄스 도트 (1.1s) |
| TerminalBody | prompt symbol \`❯\` (Starship 풍), \`git:(branch)\` inline, 빈 prompt 끝 깜빡이는 block cursor, 시나리오 전환 시 자동 스크롤 |
| TerminalStatusBar | powerlevel10k / starship 풍 — shell pill / 세션 (⎇) / branch / exit code · 우측 필터·줌·UTF-8·LF·시계 (1s tick) |

VSCode StatusBar 와 동일한 IPC 와이어링 (\`insta.switchSheet\` / \`setFilterMode\` / \`setZoom\` + \`shortcuts.onSwitchSheet\` listener) 재사용.

### T3 — Cross-platform 시나리오 6종

| id | 도메인 | shell | branch | 핵심 명령 흐름 |
|---|---|---|---|---|
| \`dev-workflow\` | 개발 워크플로우 | zsh | feature/counter-step | \`git status\` + \`npm test\` |
| \`docker-ops\` | 도커 운영 | bash | — | \`docker ps\` / \`logs\` / \`compose ps\` |
| \`k8s-debug\` | 쿠버네티스 디버깅 | zsh | cluster/prod-us-east | \`kubectl get\` / \`logs\` / \`describe\` (CrashLoopBackOff) |
| \`data-analysis\` | 데이터 분석 | bash | — | \`curl + jq\` / \`awk\` / \`python pandas\` |
| \`server-admin\` | 서버 운영 | bash | — | \`ssh\` / \`systemctl\` / \`journalctl\` / \`df -h\` |
| \`git-archaeology\` | Git 추적 | zsh | investigation/auth-bug | \`git log\` / \`blame\` / \`bisect\` |

각 시나리오 ~30~50 라인, prompt + 다중행 output + 의미있는 색상 (success green / warning yellow / error red / dim 보조). 각 시나리오의 \`history\` 배열은 Cmd+P fzf 풍 history search 인덱스로 사용.

**활성 시나리오 wiring 보강** — \`TerminalTabBar\` 가 단순히 sheet1 만 가리키던 버그 수정. \`ToolbarProps.activeSheetId?\` 옵셔널 prop 도입 + App.tsx 가 single source of truth 로 내려보냄. 키보드든 session 버튼 클릭이든 양쪽 모두 활성 탭이 올바르게 갱신.

### T4 — Boss overlay + Cmd+P history search

**TerminalBossOverlay** (Boss key 시):
- 시나리오 도메인에 맞는 long-running 명령의 stdout 흉내 — \`npm install\` / \`docker compose pull\` / \`kubectl rollout\` / \`pip install\` / \`apt upgrade\` / \`git bisect run\`
- VSCode 의 정적 로그와 달리 **라인을 시간차 (60~800ms) 로 한 줄씩 누적** → "지금 실행 중" 느낌
- 완료 후 새 prompt + 깜빡이는 커서

**TerminalHistorySearch** (Cmd+P):
- fzf 풍 fuzzy search 모달 — 모든 시나리오 \`history\` 평탄화 인덱스에서 substring 매칭
- 결과의 시나리오 chip 표시 (현재 활성 = 초록 강조, 다른 = 회색)
- Enter / 클릭 시 그 명령이 속한 시나리오로 활성 시트 점프 → TabBar / Body / StatusBar 모두 갱신
- 빈 query 상태: 현재 시나리오 history 우선 + 다른 시나리오 첫 명령

App.tsx 의 \`printPreviewActive\` 분기에 \`disguise === 'terminal'\` 케이스 추가 — VSCode QuickOpen 과 동일한 onJumpScenario 패턴.

### T5 — 라인 메모 (VSCode 패턴)

- 라인 (prompt / output / blank 무관) **더블클릭** → inline 입력 박스가 라인을 덮음
- prefix \`# \` + 옅은 녹색 (\`#7B9468\`) — 셸 주석 풍
- Enter / Tab / blur 저장, Esc 취소
- 저장 IPC: \`notes.set('terminal', scenario.id, 'L<n>', text)\` — VSCode 와 동일한 key 형식, 같은 핸들러 재사용
- 메모 있는 라인은 본문 뒤 \` # 메모\` 인라인 노출
- 단일 클릭 = activeLine 강조 (white 5% bg + 초록 inset 보더)
- Boss 활성 시 더블클릭 비활성

**focusCell 점프** (메모 검색 결과 클릭): \`L<n>\` 파싱 → flash + activeLine 강조 + \`scrollIntoView\` 로 자동 스크롤.

### 마이그레이션 안전성

v0.5 → v0.6 자동:
- 누락된 \`sheets[*].scenarios.terminal\` 키 → 기본값 \`'dev-workflow'\`
- 누락된 \`sheets[*].customScenarioIds.terminal\` → \`null\`
- 누락된 \`favoriteScenarios.terminal\` → \`[]\`
- 누락된 \`notes.terminal\` → \`{}\` (lazy create)
- 모든 메모·CSV·시트 매핑·PIN 보존

---

## v0.5 — PIN 잠금 + 시나리오 즐겨찾기 (2026-05)

자동 잠금에 진짜 자물쇠를 달고, 자주 쓰는 시나리오를 별표할 수 있게 한 UX·보안 강화 릴리즈. v0.2 부터 있던 "비밀번호로 풀리는 자동 잠금" 을 4자리 PIN UI 로 재단장하고, 잠금이 걸리는 시점을 사용자가 선택할 수 있게 세 트리거로 확장. 새로 도입한 master reset 흐름으로 "PIN 잊어버림 → 데이터 초기화 → 다시 시작" 까지 한 화면에서 완결되도록.

### E1 — PIN 잠금 패키지 (5단계)

기존 \`security.enabled\` 단일 토글을 \`triggers: { autoLock, appStart, bossKey }\` 객체로 분리해 잠금이 걸리는 *시점* 을 사용자가 각각 선택할 수 있게 만들었다.

#### E1-1 스키마 + master reset IPC

\`\`\`ts
// 이전 (v0.4.x)
SecuritySettings = { enabled, idleLockMinutes, passwordHash, passwordSalt }

// v0.5
SecuritySettings = {
  idleLockMinutes, passwordHash, passwordSalt,
  triggers: { autoLock, appStart, bossKey }
}
\`\`\`

- 마이그레이션: \`parsed.security.enabled\` 가 있으면 \`triggers.autoLock\` 으로 자동 이동. 새 두 트리거는 기본 false.
- IPC \`security:setOptions\` 가 \`{ triggers?: Partial<...> }\` 로 변경. PIN 미설정 시 트리거 true 는 거부.
- IPC \`security:getState\` 가 \`triggers\` 와 \`lockReason\` (현재 잠금 사유) 함께 반환.
- 신규 IPC **\`security:masterReset\`** — settings.json + backup 폴더 wipe 후 \`app.relaunch + exit(0)\`. E1-5 의 잠금 화면에서 호출.

#### E1-2 설정 모달 보안 섹션 UI

기존 임의 길이 비밀번호 input 두 개를 **4자리 숫자 PIN** 입력으로 재설계. \`inputMode=numeric + maxLength=4\` + 가운데 정렬 + letter-spacing 으로 4-dot 풍 시각화. \`sanitizePin\` 헬퍼가 \`\\D\` 제거 + 4자리 절단으로 붙여넣기·한글 입력 자동 정제. 세 트리거를 같은 섹션에 묶고, \`idleLockMinutes\` 입력은 \`autoLock\` 활성일 때만 사용 가능. PIN 삭제 시 모든 트리거 자동 해제.

#### E1-3 PIN 잠금 오버레이

\`LockScreen\` 을 텍스트 비밀번호 form 에서 **4-digit PIN 입력 UI** 로 전면 교체. 풀스크린 검은 배경 + 🔒 + 위장 파일명 + 사유 라벨 + 4개 dot + 화면 키패드 (3×4 grid: 1,2,3 / 4,5,6 / 7,8,9 / —,0,⌫). 키보드 입력 동시 지원 (0–9 + Backspace + Enter). 4자리 채워지면 자동 \`verifyPassword\`. 실패 시 \`mollae-shake\` (420ms cubic-bezier 좌우 6px) + 초기화 + 카운트 증가. **5회 연속 실패 → 30초 입력 차단**, 카운트다운 표시.

#### E1-4 세 트리거 wiring + lockReason

- **autoLock**: 기존 idle check 로직이 \`sec.enabled\` 대신 \`sec.triggers.autoLock\` 검사.
- **appStart**: \`app.whenReady()\` 에서 \`loadSettings\` 직후, PIN 설정 + 트리거 true 면 \`locked=true / lastLockReason='appStart'\`. 렌더러가 \`security:getState\` 응답으로 첫 프레임에 LockScreen 표시.
- **bossKey**: \`setBossActive(true→false)\` 시 (위장 → 원래 화면 복귀) PIN 잠금 요구.
- \`setLocked(newLocked, reason)\` 시그니처로 모든 잠금 진입에 사유 부여. \`lock:state\` 이벤트가 \`(locked, reason)\` 전송 → App 이 \`lockReason\` state 로 보관 → LockScreen 의 \`reasonLabel\` 이 사유별 안내 문구 표시 ("일정 시간 입력이 없어 잠겼습니다." / "잠금 상태입니다." / "화면을 복원하려면 PIN 을 입력하세요.").

#### E1-5 Master reset 흐름

LockScreen 안에 \`resetMode\` 상태를 두고 **키패드 화면과 토글되는 인라인 reset 패널** 구현. 별도 다이얼로그 없이 같은 풀스크린 오버레이 안에서 처리. 잠금화면 하단 "PIN 을 잊으셨나요?" 링크 → ⚠️ + 빨간 경고 박스 + 확인 문자열 (**RESET**) + 취소/모든 데이터 삭제 버튼. "RESET" 정확히 입력했을 때만 삭제 버튼 활성. 누르면 \`security.masterReset()\` 호출 → main 이 \`settings.json + backup\` 폴더 wipe → \`app.relaunch + exit(0)\` → 재시작 후 초기 상태.

### B6 — 시나리오 즐겨찾기

자주 쓰는 시나리오를 disguise 별로 별표하면 시트 시나리오 dropdown 상단에 별도 그룹으로 노출된다.

\`\`\`ts
Settings.favoriteScenarios: Record<DisguiseId, string[]>
// e.g. { excel: ['sales', 'kpi', 'custom'], vscode: ['react-app'] }
\`\`\`

- 신규 IPC \`favorites:toggle(disguise, scenarioId)\` — 토글 + 갱신 배열 반환.
- 설정 모달 시트 표 아래에 새 섹션 "⭐ 즐겨찾기 시나리오 (Excel/VSCode)" — 활성 disguise 시나리오 chip 목록 + ⭐/☆ 토글.
- 시트 시나리오 dropdown 을 \`<optgroup>\` 으로 분리: 즐겨찾기 그룹 (있을 때만) + 전체 그룹.
- 디스가이즈 전환 시 각자 즐겨찾기 따로 유지.

### 마이그레이션 안전성

- v0.4.x → v0.5 자동:
  - \`security.enabled\` (boolean) → \`security.triggers.autoLock\` 으로 이동, 나머지 두 트리거는 false
  - \`favoriteScenarios\` 누락 시 빈 객체로 초기화
  - 모든 메모·CSV·시트 매핑·PIN 보존

---

## v0.4.1 — VSCode 디테일 + per-disguise 시나리오 + 검색 필터 (2026-05)

v0.4 의 VSCode 모드 출시 직후 dogfood 라운드에서 잡힌 디테일·UX 개선 패치. 4건의 작은 개선을 한 번의 릴리즈로 묶음. 위장 표면 자체보다는 "각 위장이 독립적으로 살아 있다" 는 감각을 끌어올리는 데 집중.

### B1 — per-disguise sheet scenario 저장

기존: 시트 1·2·3 의 `dummyScenario` 가 위장 무관하게 단일 슬롯이라, Excel 에서 시트 1 을 "영업 실적" 으로 두고 VSCode 로 전환하면 그 영업 실적이 VSCode 의 (의미 없는) 시나리오 id 로 그대로 흘러갔다. 결과: VSCode 모드 진입 시 sidebar 가 비어 보이거나 fallback react-app 으로 강제 전환되는 일관성 깨짐.

변경:

```ts
// 이전
SheetState = { ..., dummyScenario: string, customScenarioId: string | null }

// v0.4.1
SheetState = {
  ...,
  scenarios:        { excel: string;        vscode: string }
  customScenarioIds:{ excel: string | null; vscode: string | null }
}
```

- Excel 시트별 시나리오와 VSCode 시트별 시나리오를 각각 저장
- 위장 전환 시 그 위장의 슬롯 값을 그대로 불러옴 (= 시트 1 의 Excel = "영업 실적", 시트 1 의 VSCode = "React 컴포넌트" 가 동시에 살아 있음)
- v0.4.0 → v0.4.1 자동 마이그레이션: 기존 단일 `dummyScenario` 를 활성 disguise 슬롯으로, 반대편은 그 disguise 의 `defaultScenario` 로 초기화
- 옵션 → 시트 표의 시나리오 dropdown 도 활성 위장의 시나리오 목록으로 동적 전환 (`DISGUISES[disguise].scenarios`)

### B5 — VSCode Quick Open 실제 점프

기존: Cmd+P 가 위장용 파일 검색 모달을 열어주지만 결과를 선택해도 아무 동작이 없었음 (시각적 chrome 만).

변경:

- 모든 VSCode 시나리오의 `fileTree` 를 평탄화해서 단일 검색 인덱스 구축
- 결과 항목에 **시나리오 chip** 표시 (현재 활성 = 파란 강조, 다른 시나리오 = 회색)
- Enter / 클릭 시 해당 파일이 속한 시나리오로 활성 시트의 시나리오 점프 → 사이드바 트리·코드·branch·tab 모두 그 시나리오로 일제히 갱신
- 빈 query 상태에서는 "현재 시나리오의 파일들 + 다른 시나리오의 활성 파일들" 을 *recently opened* 영역에 노출

### B4 — 메모 검색 advanced filters

검색 결과가 위장 두 종류 × 시나리오 6+ × CSV N 개로 늘어나면서 단순 substring 매칭만으로는 노이즈가 컸음. MemoSearch (`Ctrl/⌘+Shift+F`) 에 3 종 필터 추가:

| 필터 | 값 | UI |
|---|---|---|
| 위장 | 전체 / Excel / VSCode | chip |
| 시나리오 | 전체 / 활성 disguise 의 시나리오 + custom | select dropdown (위장 필터에 따라 옵션 동적 변경) |
| 종류 | 전체 / 메모 / CSV | chip |

- 위장 필터를 바꾸면 시나리오 필터는 `전체` 로 자동 리셋 (시나리오 리스트가 달라지므로)
- 필터가 하나라도 활성화되면 우측에 "초기화" 버튼 노출
- CSV 결과는 disguise 무관이지만 `filterDisguise === 'all' || activeDisguise` && `filterScenario === 'all' || 'custom'` 일 때만 포함되도록 가드

### A5 — VSCode 디테일 강화 (bracket pair / indent guide / active line)

VSCode 위장 정확도를 끌어올리는 세 가지 디테일.

**Bracket pair colorization**
- `highlight.ts` 에 `bracket` 토큰 타입 + `tokenizeFile` 추가. 파일 전체를 라인 단위 토큰화 후 후처리로 `() [] {}` 를 stack 기반 nesting 추적, depth 부여
- 라이트/다크 각각 3색 순환 팔레트 (`--vsc-bracket-1/2/3`)
- string / comment 안의 bracket 은 무시. unmatched 는 빨강 (`--vsc-bracket-unmatched`)

**Indent guides**
- `computeIndentLevels`: 라인별 leading whitespace 를 indent unit 으로 나눠 nesting depth 산출
- 빈 라인은 앞뒤 비빈 라인 중 더 깊은 indent 를 따라감 (실제 VSCode 동작 동일)
- 언어별 indent unit 자동: Python/Bash = 4, TS/JS/YAML = 2
- 각 라인에 absolute 1px vertical span 을 `ch` 단위로 컬럼 정렬해서 렌더

**Active (cursor) line**
- 라인 클릭 → `activeLine` 마킹 → 배경 강조 + 1px inset 테두리 + 라인 끝에 깜빡이는 가상 커서
- gutter 의 라인 번호도 강조색 + bold
- 메모 검색 / Quick Open 점프 (`focusCell`) 시 자동으로 active 지정

### 마이그레이션 안전성

- v0.4.0 → v0.4.1 자동:
  - 기존 `sheet.dummyScenario` (string) → `sheet.scenarios[activeDisguise]` 로 이동, 반대편 disguise 슬롯은 그 disguise 의 `defaultScenario`
  - 기존 `sheet.customScenarioId` → `sheet.customScenarioIds[activeDisguise]` 동일하게
  - 모든 메모·CSV·시트 매핑 보존

---

## v0.4 — 위장 추상화 + VSCode 위장 모드 (2026-05)

개발자 친구의 dogfood 피드백 — *"Excel 외에 VSCode 같은 IDE 위장도 있으면 좋겠다"* — 를 반영한 라인업. 타겟 페르소나를 사무직 + 마케팅 직군에서 개발자 직군까지 확장.

### 위장 추상화 (D1)

#### DisguiseProfile 인터페이스 (D1a)
- 모든 chrome 컴포넌트를 프로필 단위로 묶음:
  ```ts
  interface DisguiseProfile {
    id: 'excel' | 'vscode'
    label, description, fakeWindowTitle
    TitleBar, Toolbar, Body, StatusBar  // React 컴포넌트
    scenarios, defaultScenario
  }
  ```
- 기존 Excel 위장 컴포넌트들을 `src/renderer/src/disguises/excel/` 로 이동 + 인터페이스에 매핑
- 동작 변경 없음 (regression 0). 이후 D2~D5 작업의 토대.

#### settings.disguise + notes namespace (D1b)
- `Settings.disguise: 'excel' | 'vscode'` 필드 (기본 `'excel'`)
- `Notes` 구조: `Record<DisguiseId, Record<scenarioId, Record<cellKey, text>>>`
- 기존 v0.3.x 의 평탄 notes 구조는 `loadSettings` 에서 자동으로 `notes.excel` 으로 마이그레이션
- `notes:set` / `notes:clear` IPC 에 `disguise` 인자 추가
- MemoSearch 가 모든 disguise 가로질러 검색 + 결과에 disguise badge + 점프 시 자동 disguise 전환

#### VSCode stub + 전환 UI (D1c)
- 옵션 → 일반 → **위장 테마** dropdown 추가 (Excel 스프레드시트 / VSCode 에디터)
- 선택 즉시 적용 (settings.update → settings:changed 이벤트로 App 자동 re-render)
- D1c 시점엔 VSCode 가 Excel chrome 을 stub 으로 재사용 (D2 부터 본격 교체)

### VSCode chrome 컴포넌트 (D2)

신설 7개 컴포넌트 (`src/renderer/src/disguises/vscode/`):

- **VSCodeTitlebar** — 어두운 #3C3C3C (dark) / #DDDDDD (light), 가운데 정렬 "filename — workspace"
- **VSCodeTabBar** — Toolbar 슬롯, 가짜 열린 파일 탭 3개 + 활성 탭 상단 파란 강조
- **VSCodeActivityBar** — Body 내부 좌측 48px 세로 아이콘 (Explorer/Search/Git/Run/Extensions + Accounts/Manage). 하단 ⚙ 가 옵션 모달 진입.
- **VSCodeSidebar** — 220px Explorer, 시나리오의 fileTree 재귀 렌더 + git 변경 표시 (M/A/U)
- **VSCodeEditor** — 줄번호 gutter + syntax highlighted 코드 + 우측 하단 SNS view embed + 라인 더블클릭 inline 메모 (`L<lineNumber>` key)
- **VSCodeBossOverlay** — Boss Key 시 표시할 가짜 빌드/테스트 로그
- **VSCodeStatusBar** — 파란 #007ACC, 시트=git branch, problems, 줌, 필터, 인코딩 통합

VSCode Light+/Dark+ 색상 변수 (`--vsc-bg / --vsc-statusbar / --vsc-keyword` 등) — 라이트/다크 자동 전환.

### VSCode 시나리오 6종 (D3)

| ID | 도메인 | 언어 | 활성 파일 |
|---|---|---|---|
| `react-app` | 프론트 | TS | Counter.tsx |
| `backend-api` | 백엔드 | TS | users.ts (Express + zod) |
| `data-pipeline` | 데이터 | Python | monthly_report.py (pandas) |
| `ops-scripts` | 인프라 운영 | Bash | deploy.sh (blue/green) |
| `infrastructure` | IaC | YAML | api-deployment.yaml (k8s) |
| `mobile-app` | 모바일 | TS | ProductDetail.tsx (RN) |

각 시나리오 50~85 lines 그럴듯한 코드 + 워크스페이스명 + 파일 트리 (3~5 깊이) + branch + problems 카운트.

Syntax highlighter (`scenarios/highlight.ts`):
- 정규식 기반 단순 토크나이저 (parser 없음)
- TS/JS/Python/Bash 키워드 인식
- YAML 전용 케이스 — key/value 패턴에서 key 강조 + `#` 주석 녹색
- 토큰: comment / string / number / keyword / type (PascalCase) / function (호출) / plain

### 단축키 disguise 분기 (D4)

#### 옵션 시나리오 dropdown 동적화
- 옵션 → 서비스 → 시트별 시나리오 dropdown 이 활성 disguise 의 scenarios 동적 노출
- 위장 테마 전환 시 모든 시트의 dummyScenario 가 새 disguise 의 defaultScenario 로 reset (customScenarioId 도 null)

#### Cmd+P 재매핑
- Excel 모드 → 인쇄 미리보기 (기존)
- VSCode 모드 → **VSCodeQuickOpen** (Command Palette 풍 파일 검색 위장)
  - 화면 상단 가운데 600px 검색 박스
  - 시나리오의 fileTree 를 평탄화해 검색 대상
  - "recently opened" + 상위 10개 (빈 query)
  - 검색어 substring 매칭 + 상위 15개
  - Esc / Enter / 외부 클릭으로 닫힘

라벨 갱신: `'인쇄 미리보기'` → `'위장 빠른 보기'`.

### Boss Key 정제 (D5)

VSCode 위장의 Boss Key 화면이 활성 시나리오에 맞는 빌드/테스트/배포 로그 표시:

| 시나리오 | Boss 로그 |
|---|---|
| react-app | `npm run build` (Vite) + `vitest run` · 32 tests passed |
| backend-api | `npm test` (Jest + supertest) + coverage table |
| data-pipeline | `make ci` (black + mypy + pytest --cov) · 47 tests · 98% cov |
| ops-scripts | `./deploy.sh` (docker build + push + kubectl rollout blue) |
| infrastructure | `kubectl apply -k` + rollout + get pods/top/logs |
| mobile-app | `expo run:ios` (pod install + Metro + simulator logs) |

각 30~50줄. 상단 Terminal 탭 풍 헤더 (PROBLEMS / OUTPUT / **TERMINAL** / DEBUG CONSOLE). 라인별 자동 색상 (success 녹색 / error 빨강 / warn 주황 / command 파랑 / time prefix 옅음). 하단 프롬프트 + 깜빡이는 커서 (`@keyframes mollae-cursor-blink`).

### 마이그레이션 안전성

- v0.3.x → v0.4 자동:
  - `settings.disguise` 누락 시 `'excel'` 기본값
  - 평탄 `notes` 구조 → `notes.excel` 으로 자동 이동
  - 모든 메모·CSV·시트 매핑 보존

### 기타 hotfix (v0.4 진행 중 발견·수정)

- `settings:update` IPC 가 `partial.disguise` 같은 신규 필드 변경에 대해 `settings:changed` 이벤트 발화 안 하던 문제 — 모든 변경에 대해 1회 발화로 통일
- VSCode 옵션 진입 경로 명확화 — ActivityBar 하단 ⚙ 하나로 일원화
- VSCode 사용자 메모와 위장 주석 폰트 일치화 (italic 제거, 동일 모노스페이스)

---

## v0.3.2 — 자동 업데이트 검증 + 버전 표시 (2026-05)

C1 자동 업데이트 메커니즘이 실제 사용자 환경에서 동작하는지 v0.3.1 → v0.3.2 라운드로 검증. 변경 최소화 — 위장 표면에는 영향 없음.

### 추가

- **옵션 → 일반 → 자동 업데이트** 섹션 헤더 우측에 **현재 버전 표시** (예: `현재 버전: v0.3.2`) — 자동 업데이트 후 어느 버전에 있는지 한눈에 확인
- `window.mol.app.getVersion()` API + `app:getVersion` IPC
- autoUpdater 상태 변화 시 main 콘솔에 `[updater]` 태그로 로그 출력 (cmd `--enable-logging` 으로 추적 가능)

### v0.3.1 → v0.3.2 자동 업데이트 흐름 검증 시나리오

1. v0.3.1 설치본의 옵션 → 일반 → 자동 업데이트 → "지금 확인" 클릭
2. 상태가 `checking` → `available` → `downloading X%` → `downloaded` 전이
3. "재시작 + 적용" 버튼 클릭
4. 앱 자동 재시작 → 옵션 → 일반에서 "현재 버전: v0.3.2" 확인

---

## v0.3.1 — 창 표시 가드 (hotfix) (2026-05)

v0.3.0 윈도우 알파에서 발견된 "프로세스는 떠있는데 창이 보이지 않음" 케이스 가드.

원인 추정: 일부 윈도우 11 환경 (다중 모니터 분리, DPI 스케일링, OS 자동 배치 버그) 에서 첫 실행 시 창이 가시 영역 밖에 위치하거나 `ready-to-show` 이벤트 발화가 지연됨.

### Fixes (`src/main/index.ts`)

- `createWindow`: `center: true` 명시 — OS 자동 배치가 화면 밖에 두는 케이스 1차 방지
- `ensureWindowOnScreen(win)` 헬퍼 신설 — 창 중심점이 어떤 디스플레이 work area 안에도 들어있지 않으면 primary display 중앙으로 강제 복구
- `ready-to-show` 콜백 첫 줄에서 `ensureWindowOnScreen` 호출
- **ready-to-show 타임아웃 안전망**: 4초 후에도 `isVisible()` false 면 강제로 `show()` 호출 (paint timing 이슈 가드)

### 영향

- 코드 동작 변경 없음. 정상 환경에서는 차이 없음.
- 비정상 환경에서 창이 안 보이는 케이스를 자동 복구.

---

## v0.3 — 위장 심화 + 데이터 확장 + 자동 업데이트 (2026-05)

v0.2의 dogfood 결과 도출된 3대 pain point — **위장 효과 부족 · 메모/CSV 관리 불편 · 자동 업데이트 없음** — 을 해소하는 라인업.

### 위장 강화

#### 인쇄 미리보기 위장 모드 (A1)
- 단축키 `Cmd/Ctrl+P` → 풀스크린 Excel 인쇄 미리보기 풍 화면 (`PrintPreview.tsx`)
- Boss Key와 별도의 "두 번째 위장 모드" — 누가 "그 자료 출력해줘" 같은 자연스러운 요청을 의식할 때
- 진한 회색 배경 + 중앙 A4 비율 흰 페이지 + 상단 녹색 타이틀바 + 툴바 (인쇄/설정/여백표시/페이지 이동/줌 40–150%)
- 페이지 머리글: 위장 파일명 + 인쇄일 / 꼬리말: "대외비 · 인쇄 금지" + 페이지 번호
- 시나리오별 print-ready 표 (`dummies/printData.ts`), 페이지당 24행, 자동 페이지네이션
- 키보드: `Esc` 종료, `←/→`/`PageUp/Down` 페이지 이동
- 우선순위: Boss Key > Print Preview (boss 진입 시 preview 자동 종료)
- 잠금 상태에서 단축키 차단

#### 차트 셀 (A3 + A3.2)
- `dummies/MiniCharts.tsx`: `<Sparkline>` · `<DataBar>` · `<SparkColumn>` SVG 컴포넌트
- CSS 변수로 라이트/다크 색상 분리
- 7개 시나리오 + Custom CSV 모두 적용:
  - Sales / Projects / Budget / HR / Meetings / Inventory: DataBar 셀 배경
  - KPI: Sparkline (12개월 추세)
  - Budget: DataBar + 1~12월 셀 위에 얇은 Sparkline 오버레이 (opacity 0.35)
  - Custom CSV: 80%+ 숫자 컬럼 자동 감지, % 컬럼 인식 (`1,234.5` / `12%` / `+8%` / `100원` 한국식 표기 처리)

#### 위장 알림음 (A2)
- 검토 후 v0.3에서 제외 (보류) — 청각 위장의 효과 vs 사용자 인지 마찰 trade-off 재검토 필요. v0.4 후보.

### 데이터·생산성

#### 메모 통합 검색 (B1)
- 단축키 `Cmd/Ctrl+F` → floating 검색 모달 (`MemoSearch.tsx`)
- 모든 시나리오 메모 + Custom CSV의 headers/rows 통합 인덱싱
- 실시간 매칭 (case-insensitive), 200건 컷오프
- 결과: `[메모|CSV] [시나리오] [셀좌표]` + 매칭 텍스트 하이라이트
- 키보드: ↑↓ 이동 · Enter 점프 · Esc 닫기 · 한글 IME 가드
- 점프 시: 시나리오 매핑된 시트로 자동 전환 (없으면 시트1 시나리오 자동 변경) + 셀로 선택 이동 + 1.7초 노란 깜빡임
- 잠금 상태에서 단축키 차단

#### 메모/CSV 내보내기 (B2)
- 옵션 → 새 카테고리 **데이터** 추가
- 메모 내보내기:
  - 범위: 전체 시나리오 / 특정 시나리오
  - 포맷: CSV (Excel 호환, UTF-8 BOM + RFC 4180 escape) / Markdown / JSON
  - JSON 포맷은 전체 백업 (메모 + customScenarios), 이식·복원용
- 사용자 정의 CSV 다시 내보내기: 개별 .csv 저장

#### 자동 백업 (B3)
- `settings.json` 변경 시마다 `userData/backups/settings_YYYYMMDD_HHMMSS.json` 자동 생성
- 유지 개수 (1–30, 기본 10) 초과 시 mtime 기준 오래된 것부터 prune
- 옵션 → 데이터 → 자동 백업 섹션: on/off · 유지 개수 · "지금 백업" · "폴더 열기" · 목록 · 개별 복원/삭제
- 복원 직전 현재 상태 자동 백업 (실수 롤백 가능)
- path traversal 가드 (`^settings_[\d_]+\.json$`)

### 인프라

#### 자동 업데이트 — electron-updater (C1)
- `electron-updater@^6.8.3` 통합
- `electron-builder.yml` publish provider: github (owner: 01-june-0, repo: mollae)
- 상태 머신: `idle` / `checking` / `available` / `not-available` / `downloading` / `downloaded` / `error`
- 시작 후 5초 + 매 6시간 자동 체크 (dev 모드 skip)
- 옵션 → 일반 → 자동 업데이트 섹션: 자동 확인 on/off · "지금 확인" · 상태 표시 · 다운로드 완료 시 "재시작 + 적용"
- **publish 채널**: v0.3.0 출시 직후 저장소를 public 으로 전환 (A안) — 익명 접근으로 latest release 조회 가능. v0.3.1 부터 실 동작 시작.

### 기타

- 설정 마이그레이션: `backup`, `update` 필드 신설 (기본값으로 채워서 자동 로드)
- README의 단축키 표 + 주요 기능 섹션을 v0.3 라인업으로 갱신
- ShortcutHelpModal · SettingsModal 단축키 카테고리에 `printPreview`, `memoSearch` 추가

---

## v0.2 — 위장 강화 + 단축키 + 데이터 + 보안 (2026-05)

v0.1 MVP 의 사용성·은닉성·신뢰성을 끌어올리는 누적 패치 라인업.

### 위장 강화 (1b · 1c · 1d)
- 시트마다 별도의 위장 시나리오 매핑 (옵션 → 서비스).
- 상태바에 3–12분 간격으로 가짜 활동 메시지 표시 (`자동 저장됨 ✓`, `계산 중...`, `저장 중...`, `동기화 완료 ✓`).

### 단축키 패키지 (2a · 2c · 2d)
- 시트 전환: `Ctrl+1` / `Ctrl+2` / `Ctrl+3`.
- 필터 토글: macOS `Cmd+1/2/3`, Windows `Alt+1/2/3` (일반/차분/흑백).
- 도움말 모달: `F1`.
- 모든 단축키는 설정 → 단축키 카테고리에서 변경 가능 (Electron accelerator 문법).

### 데이터 · 생산성
- **위장 시나리오 4종 추가** (3c): HR 평가 · KPI 대시보드 · 회의 기록 · 재고 관리. 기존 영업/프로젝트/예산 포함 **총 7종**, 모두 A–Z 열까지 채움.
- **셀 메모** (3a): 임의 셀을 더블클릭 → 텍스트 편집 → Enter/Tab 저장. 한글 IME 조합 중 Enter 키가 저장 트리거 되지 않던 버그 수정 (`isComposing` 가드).
- **CSV 사용자 정의** (3b): 옵션 → 서비스 → CSV 가져오기. RFC 4180 준수 파서 (다중라인 quoted 필드 지원). 다중 import, 이름 변경, 시트별 매핑.
- ~~3d 페이지 메모~~ 는 검토 후 제외.

### 알림 UX (5c + 옵션 A)
- 다중 view 유지: 시작 시 3개 시트 WebContentsView 를 모두 생성 (`setVisible` 토글만). 비활성 시트도 백그라운드에서 알림 카운트 추적.
- 비활성 시트 알림 도착 시 시트 탭이 1.7초간 빨간색 깜빡임 (`mollae-flash` CSS 키프레임, 라이트/다크 분리).

### 보안 세션 (6c + 6a)
- **Idle Lock (자동 잠금)**: 옵션 → 보안 → 비밀번호 설정 (SHA-256 + 16바이트 random salt). `powerMonitor.getSystemIdleTime()` 5초 간격 폴링, 1–120분 임계치 (기본 10분). 잠금 시 풀스크린 LockScreen + 모든 단축키 가드.
- **즉시 잠금** 단축키: `CommandOrControl+L`.
- **긴급 로그아웃** 단축키: `CommandOrControl+Shift+L` → confirm → 모든 partition (`instagram-1/2/3`, `threads-1/2/3`, `x-1/2/3`) 의 `clearStorageData()`. 되돌릴 수 없음.
- ~~6b 시트별 비밀번호 잠금~~, ~~4c 회의 자동 잠금~~, ~~5b 방해금지 시간대~~ 는 검토 후 제외.

### 기타
- macOS · Windows 다크 모드에서의 시인성 보강 (cell-bg / cell-zebra / grid-line CSS 변수).
- 설정 파일 마이그레이션: 기존 `accountSettings` → `sheets` 구조, `customScenario` → `customScenarios` 배열, `security` 필드 신설.

---

## v0.1 — MVP + 첫 확장 (2026 상반기)

MVP 부터 SNS 확장·다크모드까지의 첫 출시 라인업.

### M0 — M7 (MVP)
- M0. Electron + React + TS + Vite 보일러플레이트 (electron-vite).
- M1. 위장 chrome 정적 목업: frameless window, FakeTitlebar (titleBarOverlay on Windows · hiddenInset on macOS), FakeRibbon, CellGrid, StatusBar, SheetTabs.
- M2. instagram.com WebContentsView 임베드 (`partition: 'persist:account-N'`).
- **M2.1**. 위장 강화: 영역 축소 (small/medium/large) + 줌 50–150% + 색 필터 (normal/mute/gray).
- **M2.2**. 호버 펼침: 기본 상태에서는 위장 데이터만 보이고 마우스 진입 시 SNS view 펼침. 흑백 필터는 preload `sendSync` 로 페이지 로드 직후 적용 — 컬러 플래시 제거.
- M3. 멀티계정 partition + 시트 탭 UI (최대 3개).
- M4. Boss Key + 일정표 위장 오버레이 (`Ctrl/Cmd+` `` ` ``).
- M5. 설정 화면 + 영구 저장 (`app.getPath('userData')/settings.json`).
- M6. mac/win 빌드 파이프라인 (GitHub Actions, unsigned).
- M7. 알파 자가 테스트.

### SNS 확장 (v0.1 추가)
- `src/main/services/{instagram,threads,x}/` 서비스 추상화. partition 네이밍: `persist:{sns}-{accountIndex}`.
- 시트별로 SNS · 계정 · 영역 크기 · 추천 줌 자유 매핑.
- **D1+D5**. 위장 시나리오 3종 (영업 / 프로젝트 / 예산), 시트별 매핑.
- **D2**. 다크모드: `system` / `light` / `dark`. `nativeTheme.themeSource` 동기화로 SNS WebContentsView 까지 따라감.
- **D3**. 알림 카운트: `webContents.on('page-title-updated')` 으로 타이틀 prefix 파싱, 시트 탭 빨간 뱃지 (`99+` 컷오프).

### 출시명 결정
- 코드명 `molstagram` 유지, 출시명 **Mollae** 확정. `appId: com.mollae.app`.

### 알려진 제한
- 코드사인 미적용 (Gatekeeper / SmartScreen 경고).
- 자동 업데이트 미적용 (GitHub Releases 수동 다운로드).
