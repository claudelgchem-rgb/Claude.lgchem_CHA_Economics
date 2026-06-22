# EDIT_BOUNDARY.md

이 문서는 **무엇을 수정해도 되고(허용), 무엇을 절대 건드리면 안 되는지(금지)** 를 정의한다.
`style-implementer` 는 이 문서의 **허용 영역만** 수정할 수 있고, `logic-guardian` 는
이 문서를 기준으로 모든 변경을 검증한다.

> 이 문서의 "파일 인벤토리"와 "편집 허용 라인 맵"은 **ui-cartographer** 가
> 실제 코드 스캔 후 채운다. 아래 §3은 현재 저장소 스캔 결과 기반 **초안**이다.

---

## 1. 편집 허용 영역 (PRESENTATION ONLY)

다음만 수정 가능하다:

- **스타일 정의**: CSS / SCSS / styled-components / Tailwind 클래스 / 인라인 style /
  `.streamlit/config.toml` 의 theme 섹션 / `st.markdown(unsafe_allow_html=True)` 로 주입되는
  `<style>` 블록 / 전역 테마 토큰 파일.
- **레이아웃 마크업**: 순수하게 *배치/감싸기* 목적의 컨테이너·래퍼·그리드·컬럼 분할.
  (예: Streamlit `st.columns`, `st.container`, HTML `<div>` 래퍼, fly/grid 클래스)
- **디자인 토큰**: 컬러/타이포/스페이싱/보더/엘리베이션 변수 정의 파일.
- **정적 표현 텍스트의 마크업 래핑**(텍스트 *내용*은 바꾸지 않는다 — 감싸는 태그/클래스만).

---

## 2. 편집 절대 금지 영역 (LOGIC — DO NOT TOUCH)

다음은 **한 글자도** 바꾸지 않는다. 손대야 풀리는 문제가 있으면 **멈추고 logic-guardian에 에스컬레이션**한다.

- 이벤트 핸들러 (onClick/onChange/on_click/callback 등)
- 상태 관리 (useState/useReducer/store/`st.session_state` 읽기·쓰기 등)
- 데이터 흐름 / props 전달의 *값* / 바인딩 식
- **계산식·수식·비즈니스 규칙** (경제성 지표: NPV/IRR/원가/마진/민감도 등 일체)
- API / DB / 파일 I/O 호출 및 그 인자
- 라우팅 / 네비게이션 로직
- 폼 제출(submit) 로직 및 유효성 검사 규칙
- 조건 분기(if/else) 의 *조건식*
- import 구조 중 로직 모듈
- 위젯의 **key**, **value**, **on_change**, **콜백 인자** 등 상태와 연결된 속성

> 마크업을 감쌀 때도 위 속성/식/키는 **그대로 보존**해야 한다.
> "겉(컨테이너·클래스·스타일)"만 바꾸고 "속(값·키·핸들러)"은 보존한다.

---

## 3. 프레임워크 추정 & 파일 인벤토리 (초안)

### 3.1 현재 저장소 상태

- **스캔 시각 기준 상태**: 저장소가 **비어 있음** (커밋 없음, 추적 파일 없음).
  `git status` → "No commits yet" / 작업 트리에 소스 파일 없음.
- 즉, **아직 매핑할 UI 코드가 존재하지 않는다.**

### 3.2 프레임워크 추정

- **현재 단정 불가** — 소스 파일이 없어 근거가 부족하다.
- **추정 근거**: 저장소명 `Claude.lgchem_CHA_Economics` 와 과업 설명("경제성 분석 플랫폼")으로
  보아, **데이터 분석/대시보드형 앱**일 가능성이 높다. 이런 도구는 통상
  **Streamlit**(파이썬 기반 분석 대시보드) 또는 **React/Vue** SPA 로 구현된다.
- **확정 절차**: UI 코드가 추가되면 `ui-cartographer` 가 다음을 근거로 프레임워크를 확정한다:
  - 파이썬 + `import streamlit` / `app.py` / `pages/` / `.streamlit/config.toml` → **Streamlit**
  - `package.json` 의 `react`/`react-dom`, `.jsx`/`.tsx` → **React**
  - `vue`, `.vue` SFC → **Vue**
  - `index.html` + 순수 `style.css`/`script.js` → **순수 HTML+CSS**

### 3.3 전역 테마 / 스타일 주입 지점 (확정 대기)

UI 코드 추가 후 다음을 식별해 기록한다:
- Streamlit: `.streamlit/config.toml` `[theme]`, `st.markdown("<style>…</style>", unsafe_allow_html=True)` 호출부
- React: `styled-components` 테마 provider, `tailwind.config.*`, 전역 `*.css`/`index.css`
- Vue: 전역 SFC `<style>`, `assets/` CSS, Tailwind config
- 순수: `<link rel="stylesheet">` 대상 파일

### 3.4 파일 분류 표 (ui-cartographer 가 채움)

| 파일 경로 | 분류(presentation/logic/mixed) | 비고 |
|-----------|-------------------------------|------|
| _(스캔 후 작성)_ | — | 현재 소스 없음 |

### 3.5 `mixed` 파일 편집 허용 라인 맵 (ui-cartographer 가 채움)

`mixed` 파일은 함수/블록 단위로 "스타일 영역(허용)"과 "로직 영역(금지)"의 라인 경계를 기록한다.

| 파일 | 허용 라인 범위 (presentation) | 금지 라인 범위 (logic) | 근거 |
|------|------------------------------|------------------------|------|
| _(스캔 후 작성)_ | — | — | — |

---

## 4. 사용 규칙

1. `style-implementer` 는 변경 전 반드시 이 문서의 §1/§3.4/§3.5 를 확인한다.
2. 표가 비어 있으면(=스캔 전) **먼저 `/redesign` Phase 0(ui-cartographer)** 을 돌려 채운다.
3. `logic-guardian` 는 매 diff 마다 §2 위반 여부를 검사하고, 한 줄이라도 닿으면 **차단(rollback 요구)** 한다.
