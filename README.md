# skywind1000.github.io

`stock-data-blog-agent`(비공개 저장소)의 `blog/publisher.py` 가 발행하는
정적 사이트입니다. 이 저장소 자체가 Hugo 프로젝트 루트입니다. 테마 없이
`layouts/` 에 직접 만든 최소 템플릿만 사용합니다 (외부 테마·Hugo 모듈
의존성 없음).

GitHub Pages 로 서비스되며, 저장소 이름이 `skywind1000.github.io` 이므로
별도 설정 없이 `https://skywind1000.github.io/` 에서 바로 열립니다.

## 로컬에서 미리보기

```bash
hugo server -D
```

`http://localhost:1313` 에서 확인합니다.

## 발행 파이프라인과의 관계

이 저장소는 비공개 저장소 `stock-data-blog-agent` 의 `blog/` 파이프라인이
쓰는 결과물입니다. 실제 원본(수집 DB, 글감 발굴, 팩트체크 코드)은 그
저장소에 있고, 이 저장소는 **공개해도 되는 완성된 사이트**만 담습니다.

```
(비공개 저장소) blog/data_pack.py  →  blog/output/<날짜>/<슬러그>/pack.json + chart-*.png
(비공개 저장소) blog/publisher.py  →  이 저장소의 content/posts/<슬러그>/index.md + 이미지 복사 + hugo 빌드
```

`blog/publisher.py` 의 `--site-dir` 를 이 저장소 경로로 지정해 실행하면
(기본값은 `config.py` 의 `BLOG_SITE_DIR` 에 이미 이 경로로 설정돼 있습니다)
팩트체크를 통과한 글감만 여기 `content/posts/` 에 씁니다. 직접 `content/`
를 손으로 고쳐도 되지만, 다음 발행 때 `--force` 를 쓰면 데이터팩 기준으로
덮어씁니다.

새 글을 만든 뒤에는 이 저장소에서 커밋·push 해야 실제로 배포됩니다
(비공개 저장소 쪽 커밋과는 별개입니다):

```bash
cd C:\Users\daejung\Desktop\claude\skywind1000.github.io
git add content
git commit -m "글 추가: <날짜> <제목>"
git push
```

## 실제 배포 전에 해야 할 일

1. **`content/privacy.md`, `content/contact.md` 채우기** — 시행일과
   연락처 이메일이 placeholder(`_(...)_`) 로 남아 있습니다.
2. **저장소 Settings → Pages → Source 를 "GitHub Actions" 로 설정** — 그
   다음부터 `main` 브랜치에 push 하면 `.github/workflows/hugo.yml` 이
   자동으로 빌드·배포합니다.
3. **AdSense 승인 후** `hugo.toml` 의 `params.adsense.client` 에
   `pub-XXXXXXXXXXXXXXXX` 를 넣으면 `layouts/partials/adsense-*.html` 이
   자동으로 스크립트/광고 슬롯을 렌더링합니다. 빈 문자열인 동안은 아무것도
   렌더링되지 않습니다.
4. **`ads.txt`** — AdSense 승인 후 발급받은 publisher ID로
   `static/ads.txt` 를 만들어야 합니다 (형식:
   `google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0`). 승인 전에는
   만들지 않는 것이 안전합니다.
5. **커스텀 도메인을 쓰기로 하면** — `static/CNAME` 파일에 도메인 한 줄만
   써서 커밋하고, 도메인 등록기관에서 해당 도메인을 이 사이트로 연결하는
   DNS 레코드(A/CNAME)를 추가하세요. `hugo.toml` 의 `baseURL` 도 새
   도메인으로 바꿔야 합니다 (GitHub Actions 는 배포 시점의 실제 URL로
   자동 덮어써서 빌드하므로, 이 값은 로컬 미리보기용 fallback 입니다).

## 구조

```
hugo.toml              사이트 설정 (baseURL, 메뉴, AdSense 클라이언트 등)
layouts/                커스텀 템플릿 (테마 없음)
content/
  posts/<슬러그>/       leaf bundle — index.md + 그 글의 차트 PNG
  about.md, privacy.md, contact.md
static/css/main.css     전체 스타일 (외부 폰트·CDN 없음)
```
