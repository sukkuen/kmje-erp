# Cloudflare Workers 자동 배포 계획 (정정판)

## 확정 사실
- 기존 배포는 **Cloudflare Workers** (Pages 아님). URL: `shiny-wood-15b6.sukkuen3.workers.dev`
- 기존 Worker 이름: `shiny-wood-15b6`, 계정 subdomain: `sukkuen3`
- 따라서 올바른 명령은 `wrangler deploy` (스크린샷의 `wrangler pages deploy`는 틀림 — Pages 프로젝트를 새로 만들어버림)
- 로컬에 wrangler.toml 없음 → 새로 만들어야 함

## 추천 구조

```
C:\Users\User\kmje-erp\
├── wrangler.toml          # 신규
├── src\
│   └── index.js           # 신규 (ASSETS 바인딩으로 정적 파일 서빙)
├── public\
│   └── index.html         # 기존 index.html을 여기로 이동
├── plan-*.md              # 배포 대상 아님
└── .git\
```

### wrangler.toml
```toml
name = "shiny-wood-15b6"
main = "src/index.js"
compatibility_date = "2025-01-01"

[assets]
directory = "./public"
binding = "ASSETS"
```

### src/index.js
```js
export default {
  async fetch(request, env) {
    return env.ASSETS.fetch(request);
  }
};
```

## 결정 (확정)
- GitHub Pages: 끔 (Cloudflare만 사용)
- 배포: 수동 (`npx wrangler deploy`)

## 단계

- [x] 0. GitHub Pages 비활성화 (API)
- [x] 1. 폴더 재구성: `index.html` → `public/index.html`, `src/index.js` 생성
- [x] 2. `wrangler.toml` 작성
- [x] 3. `.gitignore` 작성 (node_modules, .wrangler, .dev.vars)
- [x] 4. 변경사항 커밋 + push (commit 5a3ea56)
- [x] 5. **[사용자]** `npx wrangler login` 완료
- [x] 6. (스킵) dry-run 생략하고 바로 deploy
- [x] 7. **[사용자]** `npx wrangler deploy` 로 기존 Worker 덮어쓰기 완료
- [x] 8. https://shiny-wood-15b6.sukkuen3.workers.dev/ 200 OK + 93,433 bytes 일치 확인

## ⚠️ 위험 / 알아둘 점
1. **기존 Worker 덮어쓰기** — 대시보드에서 설정해두신 환경변수, KV/D1 바인딩, 커스텀 도메인이 있다면 wrangler.toml에 명시되지 않은 항목은 그대로 유지될 수도/사라질 수도 있음. 단순 HTML만 서빙하는 Worker라면 영향 없음.
2. **GitHub Pages가 깨질 수 있음** — 현재 GitHub Pages는 `main` 브랜치 루트에서 `index.html`을 찾는데, `public/index.html`로 옮기면 404. 해결 방법:
   - (a) 루트 `index.html`을 그대로 두고 `public/`에 복사본 유지 (수동 동기화 부담)
   - (b) GitHub Pages 끄기 (Cloudflare 한 곳만)
   - (c) GitHub Pages 소스를 `/docs`로 바꾸고 `docs/index.html` 사용 (또는 GitHub Actions로 자동 복사)
3. `npm install -g wrangler`는 Windows에서 권한 문제 가능 → `npx wrangler` 사용 권장 (매번 자동 다운로드/캐시)

## 결정 필요 ⚠️
**Q1. GitHub Pages 어떻게?**
- (a) 같이 살려두고 루트+public 양쪽에 index.html 유지 (sync 부담)
- (b) GitHub Pages 끄기, Cloudflare만 사용
- (c) 일단 그대로 두고 나중에 결정 (지금은 Cloudflare 셋업만)

**Q2. 자동 배포까지 갈까?**
- (a) 일단 수동 (`npx wrangler deploy` 사용자가 직접 실행)
- (b) GitHub Actions로 push 시 자동 배포 (Cloudflare API 토큰 발급 + GitHub Secrets 등록 추가 단계)
