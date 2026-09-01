# financial-landing

[financial-app](../financial-app) (**SEEDTICK**) 소개용 랜딩 페이지 + 기술블로그. 빌드 도구 없는 정적 HTML/CSS다.

## 구조

```
index.html          모든 섹션 (히어로 · 기능 · 화면 · 시작하는 법 · 설치 · 주의사항)
                    + 설정 시뮬레이터 · 항목별 설명 (#settings)
                    + 기술블로그 티저 (#blog-teaser)
                    + 계좌 개설 / AppKey 발급 가이드 시트(<dialog>, 인라인 스크립트)
styles.css          토스 디자인 토큰 기반 스타일 (nav · 카드 · post-card 공용)
blog.css            기술블로그 전용 (목록 · 본문 prose · 표 · callout · KPI)
blog/index.html     글 목록
blog/<slug>.html    글 (한 파일 = 한 글, 서로 상대경로로 링크)
assets/qr.svg       설치 페이지 QR 코드 (37×37 모듈, quiet zone 3)
assets/favicon.svg
assets/shots/*.png  앱 스크린샷
vercel.json         cleanUrls + assets 캐시 헤더
```

## 로컬 확인

```bash
npx serve .
```

## 배포 (Vercel)

빌드 단계가 없다. Vercel 프로젝트 생성 시 Framework Preset은 **Other**, Build Command는 비워 두고 Output Directory는 루트(`.`)로 둔다.

```bash
npx vercel        # 프리뷰
npx vercel --prod # 프로덕션
```

`cleanUrls`가 켜져 있어 `blog/trailing-exit.html`은 `/blog/trailing-exit`으로 열린다. 글 사이 링크는 확장자 없는 상대경로(`href="volatility-trap"`)를 쓴다.

## 수정할 때

- **설치 링크**: `index.html`에서 `expo.dev/.../builds/...` URL 두 곳(버튼 · QR 링크)을 함께 바꾼다. QR 이미지는 링크가 바뀌면 `assets/qr.svg`도 새로 만들어야 한다.
- **설정 섹션**: 기본값·상한·계산식이 전부 앱 코드에서 온다 — `app/settings.tsx`(상한·미리보기 식), `lib/appSettings.ts`(`DEFAULT_APP_SETTINGS`, `watchCount`·`minTickRate`), `features/scalper/autopilot.ts`(`MAX_GRIDS_LIMIT`, `WATCH_COUNT_LIMIT`), `features/scalper/martingaleMode.ts`(`MARTINGALE_BAR_MINUTES`, 모드 스위치), `core/martingale/index.ts`(`MARTINGALE_CONFIG` — 익절 +3% · 손절 −3% · 19:55 ET 마감, `MARTINGALE_MIN_BARS`, 진입 세션), `core/ranking/index.ts`(순위 원천 카탈로그·총 상한). **앱에서 이 값들이 바뀌면 여기도 같이 고쳐야 한다.** 시뮬레이터의 첫 진입 금액은 앱과 같은 `진입금액 × 동시 종목 수`(수량 모드면 `수량 × 현재가`)라 앱 화면의 미리보기 숫자와 일치해야 한다.
- **전략 변경 이력**:
  - 2026-08-18 — 변곡점(Savitzky–Golay)+물타기 그리드 → 추세(분봉 이동평균 4선). 이때 물타기 전용이던 `grid-risk.html`(그리드 리스크 계산표)과 호가 탭 스크린샷을 지웠다.
  - 2026-08-22 — 추세 → **모델**(LightGBM, 5분봉 Feature 33개, 확률 ≥ 학습 상위 1% 임계값 0.3767, 정규장 · 누적 거래대금 ≥ $2M · $1 초과). 앱 롤백 스위치는 `MODEL_MODE = false`.
  - 2026-08-24 — 청산을 **트레일링**으로(고점 −5% / 하드 손절 −2%, 익절 상한 없음) + **매수 후보 게이트**(틱/초 상위 `watchCount`종, 기본 5). 랜딩의 −7% 손절·5선 청산 문구는 전부 이때 걷어냈다.
  - 2026-09-01 — 모델 → **±3% 단타**(1분봉 4선 정배열 진입 · 익절 +3% · 손절 −3% · 19:55 ET 당일 청산 · 프리~애프터만, ADR 0007). 8-27~31에 돌던 배수 물타기 시험은 이날 제거(수익은 냈지만 한 종목 $1,000+ 노출 꼬리 위험 — `financial-app/docs/분석/2026-09-01_물타기-vs-손절-비교.md`). 랜딩의 모델·트레일링·상위 1% 문구는 전부 이때 걷어냈다. 앱 롤백 스위치는 `MARTINGALE_MODE = false`(모델로 복귀).
- **기술블로그**: 글 하나가 파일 하나다. 새 글은 기존 파일을 복사해 `<article class="post">` 안만 갈아 끼우고, `blog/index.html`의 카드와 앞뒤 글의 `.post-nav`, 그리고 `index.html`의 티저 카드 3장을 함께 손본다(최신 3편). 본문에 쓰는 조각은 `blog.css`에 있다 — `.table-wrap > table`(표는 가로 스크롤), `.callout` / `.callout-warn`, `.kpis > .kpi`, `pre > code`, `blockquote > cite`. 표에서 숫자 열은 `class="num"`, 강조 행은 `class="hl"`.
  - 글의 근거는 앱·분석 저장소 문서다: `financial-analyze/docs/analysis/*`, `financial-app/docs/domain/모델/*`, `financial-app/docs/분석/*`. **숫자를 새로 쓸 때는 그 문서에서 그대로 옮긴다.**
  - 성과 숫자를 쓰는 글에는 하단 `.disclaimer`(과거 데이터 · 수익 보장 아님)를 그대로 둔다.
- **가이드 시트**: "시작하는 법" 1단계의 칩 버튼(`[data-guide]`)이 여는 `<dialog id="guide">`. 절차는 위키독스 원문([계좌 개설](https://wikidocs.net/165209) · [API 신청](https://wikidocs.net/165188))을 참고해 **직접 다시 쓴 문장**이다. 원문 이미지는 저자 저작물이라 옮기지 않았고, 각 탭 하단에 원문 링크를 둔다. 캡처를 넣는다면 우리가 직접 찍은 것만 쓴다.
- **`<dialog>` 폴백**: 칩 버튼은 원래 위키독스로 향하는 `<a href>`다. `showModal`을 못 쓰는 브라우저에서는 스크립트가 빠지면서 링크 그대로 원문으로 이동한다.
- **스크린샷**: `assets/shots/`에 같은 이름으로 덮어쓰면 된다. 계좌 화면처럼 AppKey·계좌번호가 보이는 캡처는 올리지 않는다.
