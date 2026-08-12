# financial-landing

[financial-app](../financial-app) (**SEEDTICK**) 소개용 랜딩 페이지. 빌드 도구 없는 정적 HTML/CSS다.

## 구조

```
index.html          모든 섹션 (히어로 · 기능 · 화면 · 시작하는 법 · 설치 · 주의사항)
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
- **스크린샷**: `assets/shots/`에 같은 이름으로 덮어쓰면 된다. 계좌 화면처럼 AppKey·계좌번호가 보이는 캡처는 올리지 않는다.
