# 세금계산서 발행요청 기능 추가 계획

## 목표
담당자가 수주현황 목록에서 "이 건 세금계산서 발행해달라"고 표시 → 경리가 목록에서 바로 확인 → 발행일 입력하는 흐름을 만든다.

## 결정 사항 (사용자 확정)
- **단위**: 수주건별 (order 단위, 결제항목별 아님)
- **권한**: 없음 (누구나 요청/취소 가능)
- **표시**: 수주현황 목록의 '발행' 컬럼에 배지로

## 설계

### 데이터 모델 변경
- `order` 객체에 `invoiceRequested: boolean` 필드 1개 추가
- 기존 오더에는 필드가 없음 → JS `falsy` 처리로 안전 (seed data 수정 불필요)
- Firebase/localStorage 동기화: 기존 saveO() 로직이 orders 전체를 저장하므로 새 필드도 자동 포함

### 배지 상태 로직 (기존 → 확장)
현재 (line 270 부근):
```
tot=0        → '—'
iss==tot     → '전체발행' (b-green)
0<iss<tot    → 'iss/tot' (b-amber)
iss==0       → '미발행' (b-gray)
```

변경 후:
```
tot=0        → '—' (변경 없음, 클릭 불가)
iss==tot     → '전체발행' (b-green, 클릭 불가)
0<iss<tot    → 'iss/tot' (b-amber, 클릭 불가)
iss==0 && req  → '요청됨' (b-amber-강조 or 새 색, 클릭 시 요청 취소)
iss==0 && !req → '미발행' (b-gray, 클릭 시 요청)
```

### 인터랙션
- 배지 클릭 시:
  - 요청 가능 상태(미발행)면 → `invoiceRequested = true`, 즉시 저장
  - 요청됨 상태면 → `invoiceRequested = false`, 즉시 저장 (취소)
  - 그 외(부분/전체발행)는 클릭해도 no-op
- 배지에 `cursor:pointer` + `title` 툴팁으로 힌트 (예: "클릭: 세금계산서 발행 요청")

### 발행 완료 시 자동 정리 (선택)
- 어떤 결제항목에 발행일이 입력되면 `invoiceRequested`를 자동으로 `false`로 리셋? 
  - **미포함** — 상태가 badge 우선순위로 이미 가려지므로 굳이 리셋 안 해도 됨. 단순 유지.

## 단계

- [x] 1. `OrderList`의 '발행' 배지 렌더 로직에 `요청됨` 상태 추가
- [x] 2. 배지 클릭 핸들러 추가 (upd → saveOrders)
- [x] 3. CSS: `.b-yellow` 클래스 신규 추가 (#fef08a bg / #854d0e text)
- [x] 4. td에 stopPropagation 걸어서 행 확장 이벤트와 충돌 방지
- [x] 5. 커밋 + push (commit 8f0140c)
- [x] 6. Cloudflare 배포 완료 (Version c4db3ce6, sukkuen3 계정으로 재로그인 후). 로고+발행요청 라이브 확인

## 위험 / 메모
- `saveOrders` 시그니처가 orders 전체를 받는지, diff만 받는지 확인 필요 → 코드 봐서 판단
- 배지 클릭이 행 클릭(있다면)과 이벤트 충돌 가능 → `e.stopPropagation()` 방어

## 향후 확장 (지금은 안 함)
- 요청 시각/요청자 기록 (권한 개념 도입 시)
- 대시보드에 "발행 대기 건수" 카운트
- 요청 상태 필터 (수주 목록에서 "요청된 것만 보기")
