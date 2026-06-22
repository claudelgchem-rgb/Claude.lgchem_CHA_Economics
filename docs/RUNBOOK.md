# RUNBOOK.md — UI 리디자인 단계별 실행 절차

이 하니스는 **기존 UI 로직을 일절 건드리지 않고** 레이아웃·비주얼만 고급스럽게
재설계하기 위한 멀티 에이전트 구성이다. 이 문서는 *어떻게 돌리는가*를 설명한다.

---

## 구성 요소

```
.claude/
  agents/
    ui-cartographer.md    # Phase 0: 프레젠테이션 vs 로직 경계 매핑
    design-director.md    # Phase 1: 디자인 토큰·안티-AI 방향 수립
    layout-architect.md   # Phase 2: 화면별 레이아웃 재구성 설계
    style-implementer.md  # Phase 3: 허용 영역에 실제 적용
    logic-guardian.md     # Phase 3: 로직 무결성 검증(거부권)
    visual-critic.md      # Phase 1·4: 미감 비평·반복
  commands/
    redesign.md           # /redesign 오케스트레이션
docs/
  DESIGN_PRINCIPLES.md    # 미적 기준(안티-AI + 프리미엄)
  EDIT_BOUNDARY.md        # 편집 허용/금지 영역
  RUNBOOK.md              # (이 문서)
```

---

## 사전 조건

1. **UI 코드가 저장소에 존재**해야 한다. 비어 있으면 Phase 0 에서 멈춘다.
2. 가능하면 앱을 로컬에서 구동할 수 있는 상태(의존성 설치)면 좋다 — 렌더 확인·스크린샷에 사용.
3. 작업 브랜치에서 진행한다(로직 불변 증빙을 위해 단위 커밋 권장).

---

## 실행 절차

### 1) 전체 오케스트레이션 (권장)
Claude Code 입력창에:

```
/redesign
```

→ Phase 0 → 5 를 순서대로 진행하며, **각 Phase 끝에서 멈추고** 다음 진행 여부를 묻는다.

### 2) 단계별 수동 실행 (세밀한 통제가 필요할 때)
필요한 에이전트를 직접 지목해 호출한다. 예:

- "ui-cartographer 로 저장소 UI 경계를 매핑해줘" (Phase 0)
- "design-director 로 디자인 토큰을 수립해줘" → "visual-critic 로 방향 점검" (Phase 1)
- "layout-architect 로 화면별 설계안을 만들어줘" (Phase 2)
- "이 단위를 style-implementer 로 적용하고 logic-guardian 로 검증해줘" (Phase 3)
- "visual-critic 로 결과를 비평해줘" (Phase 4)

---

## 단계별 게이트 (요약)

| Phase | 담당 | 산출물 | 게이트 |
|-------|------|--------|--------|
| 0 Discovery | ui-cartographer | EDIT_BOUNDARY 경계 맵 | 경계 요약 → 진행 질문 |
| 1 Direction | design-director → visual-critic | 토큰·방향 + 1차 점검 | 방향 요약 → 진행 질문 |
| 2 Layout | layout-architect | before→after 설계안 | 설계 요약 → 진행 질문 |
| 3 Implement | style-implementer + logic-guardian | 단위 커밋 + PASS/FAIL | 단위마다 검증 게이트 |
| 4 Critique | visual-critic | 비평 + 판정 | NEEDS-WORK 시 2~3 반복 |
| 5 Report | 오케스트레이터 | 변경요약·before/after·근거·증빙 | 종료 |

---

## 안전장치 (반드시 기억)

- **로직 불변**: 핸들러·상태·계산식·API/DB·라우팅·폼·조건식·위젯 key/value/on_change 는
  한 글자도 바꾸지 않는다. 손대야 풀리면 **멈추고 에스컬레이션**.
- **logic-guardian 거부권**: 금지 영역에 한 줄이라도 닿으면 차단·롤백.
- **안티-AI 미감**: `DESIGN_PRINCIPLES.md §1 BANNED` 위반은 visual-critic 이 FAIL 처리.
- **점진적 검증**: 작은 단위 적용 → 즉시 검증 → 통과 후 다음.

---

## 트러블슈팅

- *경계 표가 비었다* → 먼저 ui-cartographer(Phase 0) 실행.
- *적용했더니 기능이 깨졌다* → logic-guardian 로 diff 재검사 후 해당 단위 롤백.
- *여전히 AI 같다* → visual-critic 의 구체 지적을 받아 Phase 2~3 반복.
