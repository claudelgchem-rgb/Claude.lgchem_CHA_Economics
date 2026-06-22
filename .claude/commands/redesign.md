---
description: 기존 로직을 일절 건드리지 않고 UI의 레이아웃·비주얼만 고급스럽게 재설계하는 멀티 에이전트 워크플로를 오케스트레이션한다.
---

# /redesign — UI 리디자인 오케스트레이터

경제성 분석 플랫폼의 UI를 **로직 불변(Logic Immutability)** 원칙 아래
**프레젠테이션만** 고급스럽게 재설계한다. 아래 Phase 를 **순서대로** 진행하되,
**각 Phase 종료 시 요약을 출력하고 다음 진행 여부를 사람에게 묻는다(기본은 멈춤)**.

## 불변 원칙 (매 Phase 적용)
- `docs/DESIGN_PRINCIPLES.md`(미감) 와 `docs/EDIT_BOUNDARY.md`(편집 경계) 를 준수한다.
- 로직(핸들러/상태/계산/호출/라우팅/폼/조건식/위젯 key·value·on_change)은 한 글자도 바꾸지 않는다.
- 한국어로 소통, 코드/경로/토큰/에이전트명은 영어.

---

## Phase 0 — Discovery
- **ui-cartographer** 서브에이전트를 호출한다.
- 산출: `docs/EDIT_BOUNDARY.md` 의 프레임워크 확정·테마 주입 지점·파일 분류표·mixed 라인 맵.
- ⏸ **게이트**: 경계 맵 요약 출력 → 다음 진행 여부 질문.
  - 저장소가 비어 있거나 UI 코드가 없으면 여기서 멈추고 사용자에게 알린다.

## Phase 1 — Direction
- **design-director** 호출 → 컬러/타이포/스페이싱/보더/상태 토큰 + 숫자 규칙 + 선택 근거 수립.
- **visual-critic** 호출 → 방향이 *일반적/AI스럽지 않은지* 1차 점검(BANNED §1 위반 여부).
- ⏸ **게이트**: 토큰·방향 요약 + critic 판정 출력 → 진행 여부 질문. NEEDS-WORK 면 방향 보완 후 재점검.

## Phase 2 — Layout
- **layout-architect** 호출 → 화면별 before→after 설계안 + 변경 대상 파일·영역 목록 +
  순서화된 적용 체크리스트(작은 단위).
- ⏸ **게이트**: 설계안 요약 출력 → 진행 여부 질문.

## Phase 3 — Implement (단위 반복 + 검증 게이트)
설계 체크리스트의 **각 작은 단위마다**:
1. **style-implementer** 가 허용 영역만 수정 → 의미 단위로 커밋.
2. **logic-guardian** 가 diff 검증(거부권). **PASS 해야** 다음 단위로 진행.
   - **FAIL** 이면 해당 단위를 되돌리고(rollback) 사유를 기록한 뒤 재시도.
3. 가능하면 앱 렌더/구동으로 깨짐 확인.
- ⏸ **게이트**: 적용 단위 묶음 요약(커밋·PASS/FAIL) 출력 → 진행 여부 질문.

## Phase 4 — Critique & Iterate
- **visual-critic** 가 렌더 결과를 DESIGN_PRINCIPLES 대비 비평 → PASS / NEEDS-WORK.
- NEEDS-WORK 면 **Phase 2~3 를 반복**(설계 보완 → 재적용 → 재검증).
- ⏸ **게이트**: 비평 결과 출력 → 반복/종료 여부 질문.

## Phase 5 — Report
다음을 정리해 출력한다:
- 변경 요약(파일·단위·커밋 목록).
- **Before / After**(가능하면 스크린샷, 아니면 구조 비교).
- 디자인 의사결정 근거(토큰·레이아웃 선택 이유).
- **로직 불변 증빙**(logic-guardian 의 단위별 PASS 기록, diff 가 프레젠테이션 한정임).

---

### 진행 규칙
- 각 ⏸ 게이트에서 **기본 동작은 멈춤**이다. 사용자의 명시적 승인 후 다음 Phase 로 간다.
- 어느 단계든 로직을 건드려야만 풀리는 문제가 생기면 **즉시 멈추고** 사용자에게 보고한다.
