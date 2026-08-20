# 수주현황 엑셀 내보내기 기능

## 목적
은행·타기관이 수주내역 자료를 요청할 때, 수주현황을 엑셀(.xlsx) 파일로 내려받아 전달.

## 현재 구조 (확인됨)
- 엑셀 라이브러리 없음 (다른 admin.html은 cdnjs xlsx 0.18.5 사용 이력 있음)
- OrderList: 연도 선택 → 검색/담당/상태 필터 → `filtered` 목록 렌더
- order 필드: no, orderNo, clientName, description, manager, startDate, endDate, projectAmount, currency, payments[], subcontractors[], orderType, note
- 파생값: oBalance(미수금), getSt(상태), invSummary(계산서 상태)

## 설계
- **SheetJS(xlsx)를 클릭 시 지연 로드**(lazy load) → 초기 페이지 로딩 안 무겁게. cdnjs에서 `<script>` 동적 삽입.
- 툴바 "+ 수주 추가" 옆에 **📊 엑셀 내보내기** 버튼 추가.
- 금액 칸은 **숫자 그대로 출력**(은행이 합계 계산 가능하게). 통화는 별도 칸.
- 파일명: `수주현황_{연도}_{YYYYMMDD}.xlsx`

### 컬럼(안)
No / 수주번호 / 거래처 / 수행내용 / 담당 / 시작일 / 완료예정 / 통화 / 수주금액 / 입금액 / 미수금 / 상태 / 계산서상태 / 비고

## 결정 필요 ⚠️
- Q1. **내보내기 범위**: 지금 화면의 필터 결과만 / 선택 연도 전체 / 전 연도 전부
- Q2. **상세 수준**: 수주 1건=1행(요약)만 / 결제항목(계약금·기성·잔금) 내역까지 별도 시트로 포함

## 결정 (확정)
- 범위: 현재 화면 필터 결과(filtered)
- 상세: 수주 1건 = 1행 요약

## 단계
- [x] 1. SheetJS 지연 로드 헬퍼 추가 (loadXLSX, cdnjs 0.18.5)
- [x] 2. 내보내기 함수 exportExcel (filtered → 시트 → writeFile). 금액=숫자로 출력
- [x] 3. 툴바에 📊 엑셀 내보내기 버튼 추가
- [x] 4. 로컬 검증 완료: 버튼 클릭→XLSX 로드 확인, 라운드트립으로 헤더/한글/숫자타입 검증, 콘솔 에러 없음
- [ ] 5. **[대기]** 사용자 확인 후 커밋/푸시/배포

## 배포
- 사용자 "배포해" 지시 후에만 배포.
