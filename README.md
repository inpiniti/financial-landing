# financial-landing

[financial-app](../financial-app) (**SEEDTICK**) 소개용 랜딩 페이지. 빌드 도구 없는 정적 HTML/CSS다.

## 구조

```
index.html          모든 섹션 (히어로 · 기능 · 화면 · 시작하는 법 · 설치 · 주의사항)
                    + 설정 시뮬레이터 · 항목별 설명 (#settings)
                    + 계좌 개설 / AppKey 발급 가이드 시트(<dialog>, 인라인 스크립트)
styles.css          토스 디자인 토큰 기반 스타일
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

## 수정할 때

- **설치 링크**: `index.html`에서 `expo.dev/.../builds/...` URL 두 곳(버튼 · QR 링크)을 함께 바꾼다. QR 이미지는 링크가 바뀌면 `assets/qr.svg`도 새로 만들어야 한다.
- **설정 섹션**: 기본값·상한·계산식이 전부 앱 코드에서 온다 — `app/settings.tsx`(상한·미리보기 식), `lib/appSettings.ts`(`DEFAULT_APP_SETTINGS`), `features/scalper/autopilot.ts`(`MAX_GRIDS_LIMIT`). **앱에서 이 값들이 바뀌면 여기도 같이 고쳐야 한다.** 시뮬레이터의 누적 낙폭은 앱과 같은 복리식 `1−(1−g)^n`이라 앱 화면의 미리보기 숫자와 일치해야 한다.
- **가이드 시트**: "시작하는 법" 1단계의 칩 버튼(`[data-guide]`)이 여는 `<dialog id="guide">`. 절차는 위키독스 원문([계좌 개설](https://wikidocs.net/165209) · [API 신청](https://wikidocs.net/165188))을 참고해 **직접 다시 쓴 문장**이다. 원문 이미지는 저자 저작물이라 옮기지 않았고, 각 탭 하단에 원문 링크를 둔다. 캡처를 넣는다면 우리가 직접 찍은 것만 쓴다.
- **`<dialog>` 폴백**: 칩 버튼은 원래 위키독스로 향하는 `<a href>`다. `showModal`을 못 쓰는 브라우저에서는 스크립트가 빠지면서 링크 그대로 원문으로 이동한다.
- **스크린샷**: `assets/shots/`에 같은 이름으로 덮어쓰면 된다. 계좌 화면처럼 AppKey·계좌번호가 보이는 캡처는 올리지 않는다.
