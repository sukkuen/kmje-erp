# kmje-erp GitHub Pages 배포 계획

## 목표
`C:\Users\User\Downloads\index.html` 을 `kmje-erp` GitHub 저장소에 올리고 GitHub Pages로 배포한다.

## 단계

- [x] 1. 로컬 폴더 생성: `C:\Users\User\kmje-erp\`
- [x] 2. `index.html` 복사 (93KB)
- [x] 3. `git init -b main` + local user.email/user.name 설정 (sukkuen3@gmail.com / sukkuen3)
- [x] 4. 초기 커밋 생성 (`e8b4bda Initial commit: add index.html`)
- [x] 5. **[사용자 작업]** github.com/new 에서 `kmje-erp` Public 저장소 생성 (사용자명: `sukkuen`)
- [x] 6. `git remote add origin https://github.com/sukkuen/kmje-erp.git`
- [x] 7. `git push -u origin main` 완료
- [x] 8. GitHub Pages 활성화 (REST API로 자동 활성화 완료, source=main:/)
- [x] 9. 배포 URL: https://sukkuen.github.io/kmje-erp/ (첫 빌드 1-2분)

## 불확실 항목 (사용자 확인 필요)
1. **GitHub 사용자명** — 이메일 prefix가 `sukkuen3`인데, GitHub 계정명도 동일한지?
2. **5단계 완료 여부** — 웹에서 저장소 생성이 끝났는지?

## 위험 / 메모
- 5단계에서 저장소를 빈 상태로 만들지 않으면 첫 push에서 충돌 발생 → README 등 옵션 모두 OFF여야 함.
- HTTPS push 시 처음이면 Windows Credential Manager가 브라우저 인증창을 띄움.
- main 푸시 시 GitHub Pages는 약 1-2분 후 라이브.
