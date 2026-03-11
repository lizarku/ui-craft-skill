---
name: ui-craft
description: Vue 3 + CSS 변수 환경에서 "정확하면서도 재미있는" UI를 만들거나 다듬는 스킬. 낚시 앱은 데이터 대시보드가 아니라 낚시인의 놀이터다.
trigger: frontend/src 하위 .vue, .ts, .css, .html 파일 수정 시 자동 적용
---

# ui-craft

> **디자인 철학**: 데이터는 정확하게, 경험은 흥미롭게. 딱딱한 대시보드가 아니라 "다시 열어보고 싶은" 서비스를 만든다.
> 단, 재미가 집중을 방해하면 안 된다. **흥미로움과 질서는 공존해야 한다.**
> - 재미 요소는 사용자의 시선을 **핵심 정보로 유도**하는 방향이어야 한다
> - 장식이 정보를 가리거나, 시선이 분산되거나, "뭘 봐야 하지?"가 되면 실패다
> - 원칙: **하나의 화면에서 사용자의 눈이 갈 곳은 명확해야 한다** (시각적 위계)

## 1. 모드 전환

| 모드 | 키워드 | 설명 |
|------|--------|------|
| **baseline** (기본) | `담백하게` `담백한` `담백` 또는 키워드 없음 | 1순위 + 2순위 규칙. 재미 요소는 유지하되 시각 연출은 절제 |
| **expressive** | `화려하게` `화려한` `화려` | baseline + 3순위 표현 규칙. 시각적 임팩트 적극 활용 |

- 부정형(`화려하게 하지 마`, `화려하게 말고`)은 baseline으로 처리한다.
- 명시적으로 `담백하게`가 있으면 expressive 규칙을 절대 적용하지 않는다.

---

## 2. [1순위] 프로젝트 고유 규칙 (항상 적용)

### 컴포넌트 구조
- `<script setup lang="ts">` + `<style scoped>` 필수
- Composition API only. Options API 금지
- 전역 클래스는 `mf-*` 접두사, scoped 내부는 kebab-case
- 상태 클래스는 flat: `active`, `expanded`, `disabled` (BEM 금지)

### CSS 변수 체계
```css
/* 배경 */ --bg, --bg2, --bg3, --bg-top
/* 테두리 */ --bd, --bd2
/* 텍스트 */ --t1 (주), --t2 (보조), --t3 (힌트)
/* 색상 */ --grn, --yel, --red, --blu, --cyan, --orange
/* 차트 */ --clr-wind, --clr-wave, --clr-danger
/* 타이포 */ --type-display ~ --type-nano (8단계)
/* 폰트 굵기 */ --fw-normal ~ --fw-bold (4단계)
/* 폰트 패밀리 */ --ff-sans, --ff-display, --ff-mono
```
- 색상/배경/텍스트에 하드코딩 hex/rgb 금지. 반드시 CSS 변수 사용
- 60-30-10 색상 비율 준수 (color-harmony-guidelines.css 참조)

### 타이포그래피
- font-size는 `--type-*` 변수 사용. scoped에서 px 하드코딩 금지
- font-weight는 `--fw-*` 변수 사용
- 한국어: `word-break: keep-all` 필수
- 최소 폰트 11px, 12px 미만은 `font-weight >= 500`

### 반응형
```css
/* 주력 3단계 */
@media (max-width: 768px) { /* 태블릿 */ }
@media (max-width: 480px) { /* 모바일 */ }
@media (max-width: 360px) { /* 소형 */ }
```

### Transition 표준
```css
/* hover */ transition: 0.15s ease;
/* 데이터 바/게이지 */ transition: 0.4s ease; /* max 0.5s */
/* 모달/패널 */ transition: 0.25s cubic-bezier(0.16, 1, 0.3, 1);
```
- `transform`과 `opacity`만 애니메이션. layout 속성(width, height, top) 금지
- `prefers-reduced-motion: reduce` 시 transition 0s

---

## 3. [2순위] 실용 원칙 (항상 적용)

### 로딩/빈 상태/에러
- 데이터 fetch 시 `mf-spinner` 또는 스켈레톤 표시
- 빈 결과 시 `mf-empty-state` + 안내 메시지
- API 에러 시 인라인 에러 메시지 (토스트 남발 금지)
- `aria-live="polite"` 로 상태 변경 알림

### 접근성
- 인터랙티브 요소에 `aria-label` 필수 (특히 아이콘 버튼)
- 모달: focus trap 구현 (AddEquipmentModal 패턴 참조)
- 키보드: Enter/Space 활성화, Escape 닫기
- `:focus-visible` 아웃라인 유지 (제거 금지)
- 데이터 시각화: `role="img"` + `aria-label`로 요약 제공

### 숫자/데이터
- 숫자 표시: `font-variant-numeric: tabular-nums`
- 긴 텍스트: `text-wrap: balance` (제목), `text-wrap: pretty` (본문)

### 재미 요소 (baseline에서도 항상 적용)
대중 서비스는 흥미를 유발해야 한다. 아래는 "담백하게"에서도 허용되는 재미 요소:
- **어종 이모지/이미지**: 스코어 카드, 히트맵 셀 등에 시각적 캐릭터성 부여
- **색상 코딩의 감성**: 안전(초록)/주의(노란)/위험(빨간)에 단순 배경색이 아닌 그라디언트 힌트, 아이콘 조합으로 직관성 + 재미
- **마이크로 인터랙션**: 카드 hover 시 미세 `translateY(-2px)`, 스코어 바 채워지는 애니메이션, 판정 뱃지 등장 시 살짝 바운스
- **데이터에 생동감**: 숫자만 나열하지 말고 아이콘·컬러·크기 변화로 강약 표현 (풍속 화살표, 파고 웨이브 아이콘 등)
- **빈 상태도 재미있게**: "등록된 장비가 없습니다" 대신 "아직 장비가 없어요! 🎣 첫 장비를 등록해보세요"
- **전환의 부드러움**: 페이지/섹션 전환 시 자연스러운 fade (0.2s) — 뚝뚝 끊기지 않게

**재미의 경계선** — 아래에 해당하면 재미가 아니라 소음이다:
- 한 화면에 움직이는 요소가 3개 이상 동시 작동
- 장식이 데이터 영역을 침범하여 숫자/텍스트 가독성 저하
- 사용자가 "어디를 봐야 하지?"라고 느끼는 시선 분산
- 이모지/아이콘이 정보 전달 없이 공간만 차지
- 색상이 너무 많아 강약 구분이 안 되는 상태 (포인트 컬러는 화면당 2개 이내)

---

## 4. [3순위] 표현 원칙 (expressive 모드에서만 적용)

> "화려하게" 키워드가 있을 때만 아래 규칙을 추가 적용한다.

### 허용 범위
- 랜딩 히어로 섹션: gradient 배경, 미세 패럴랙스, 큰 타이포 허용
- 섹션 전환: `opacity` fade (0.3s 이내)
- 카드 hover: 미세 `translateY(-2px)` + `box-shadow` 강화
- 색상: `--grn`, `--cyan` 등 포인트 컬러를 30% 영역에 적극 활용

### 금지 (expressive에서도)
- 3D transform, perspective, 회전 애니메이션
- 자동재생 동영상/GIF 배경
- 텍스트 그림자 남발 (1개 이하)
- 네온/글로우 효과 과용
- "AI 슬롭": 무의미한 장식 요소, 과도한 그라디언트 중첩, 의미 없는 아이콘 나열

---

## 5. DO / DON'T

### DO
```vue
<!-- CSS 변수 사용 -->
<style scoped>
.card { background: var(--bg2); color: var(--t1); border: 1px solid var(--bd); }
.card-title { font-size: var(--type-sm); font-weight: var(--fw-semi); }
</style>

<!-- 로딩 상태 -->
<template>
  <div v-if="loading" class="mf-spinner" />
  <div v-else-if="!items.length" class="mf-empty-state">데이터가 없습니다</div>
  <div v-else>...</div>
</template>

<!-- 접근성 -->
<button aria-label="필터 초기화" @click="reset">
  <IconRefresh />
</button>
```

### DON'T
```vue
<!-- 하드코딩 색상 -->
<style scoped>
.card { background: #1a1a2e; color: white; font-size: 14px; }
</style>

<!-- 로딩 없음 -->
<template>
  <div>{{ data }}</div> <!-- 로딩/빈/에러 상태 누락 -->
</template>

<!-- 접근성 미흡 -->
<div @click="toggle" style="cursor:pointer"> <!-- button이 아님, aria 없음 -->
  <IconMenu />
</div>
```

---

## 6. Tailwind 사용 가이드
- Tailwind는 보조적으로만 사용 (유틸리티 레이아웃: flex, grid, gap, p, m)
- 색상/타이포/테마 관련은 반드시 CSS 변수 우선
- `@apply` 사용 금지 (scoped와 충돌)

---

## 7. QA 체크리스트

작업 완료 전 아래 항목을 확인한다:

- [ ] `<script setup lang="ts">` + `<style scoped>` 사용했는가
- [ ] 색상/배경/텍스트에 CSS 변수(`--bg`, `--t1` 등)를 사용했는가 (하드코딩 없는가)
- [ ] font-size에 `--type-*` 변수를 사용했는가
- [ ] 한국어 텍스트에 `word-break: keep-all` 적용했는가
- [ ] 768px / 480px / 360px 브레이크포인트로 반응형 처리했는가
- [ ] 로딩 상태 (spinner/skeleton) 있는가
- [ ] 빈 상태 안내 메시지 있는가
- [ ] 에러 상태 인라인 처리했는가
- [ ] 인터랙티브 요소에 `aria-label` 있는가
- [ ] 키보드 접근 가능한가 (focus-visible 유지)
- [ ] transition은 `transform`/`opacity`만 사용했는가
- [ ] `prefers-reduced-motion` 대응했는가
- [ ] 전역 클래스는 `mf-*` 접두사인가
- [ ] 재미 요소(마이크로 인터랙션, 색상 감성, 빈 상태 문구)가 포함되어 있는가
- [ ] expressive 모드 요청이 아니면 과도한 시각 연출(그라디언트 배경, 패럴랙스)을 추가하지 않았는가
