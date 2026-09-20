# HIGGSFIELD_ITEM_PROMPTS

현재 인벤토리는 실제 이미지가 아니라 **이모지**(`SLOT_ICON = {weapon:'⚔️', armor:'🛡️', ...}`)로 표시된다.
목표: 슬롯당 이모지 1개 → 등급별로 다른 실제 아이콘 이미지로 교체.

## Prompt Template (게임 데이터로부터 자동 생성 가능한 형태)

```
BASE WEAPON: {slot 별 무기 실루엣 — 아래 표}
MATERIAL:    {아이템 등급에 따른 재질 — steel(일반) / enchanted steel(마법) / gleaming silver(희귀) / ornate gold-inlaid(세트) / legendary radiant(유니크)}
RARITY:      {RARITY[item.rarity].color 를 프롬프트에 HEX로 직접 삽입}
ELEMENT:     {세트/유니크 테마에 따른 속성 — none / fire / frost / holy / abyssal 등}
ENHANCEMENT: {item.enhance 값에 따른 발광 강도 — 아래 "강화 단계별 표현" 참고}
```

이 템플릿을 코드에서 `createRandomItem()` / `SETS` / `UNIQUES` 데이터로부터 문자열로 조합하면,
새 아이템을 추가할 때마다 프롬프트를 수동으로 쓰지 않고 자동 생성할 수 있다 (`HIGGSFIELD_PIPELINE.md`의 자동화 절 참고).

### 슬롯별 BASE WEAPON/ARMOR 실루엣 (`SLOT_LABEL` 기준)

| slot | 실루엣 |
|---|---|
| weapon | 클래스에 따라 다름 — one-handed sword(파이터) / greataxe(새비지) / spear(발키리) / staff(매지션) |
| armor | 흉갑(chest plate) |
| helmet | 투구 |
| gloves | 건틀릿 |
| boots | 각반/부츠 |
| belt | 허리띠, 버클 강조 |
| accessory | 반지 또는 목걸이(작은 실루엣이므로 보석/발광부를 크게 그려 가독성 확보) |

### 등급별 MATERIAL/AURA (`RARITY` 데이터, HEX는 임의 변경 금지)

| 등급 | HEX | 재질/보조 표현 |
|---|---|---|
| 일반(Normal) | `#c9c9c9` | 무광 강철, 장식 없음 |
| 마법(Magic) | `#4a9eff` | 강철 + 파란 룬 각인 1~2개 |
| 희귀(Rare) | `#ffd84a` | 은/금 세공, 보석 1개 박힘, 은은한 금색 발광 |
| 세트(Set) | `#3ddc6a` | 세트별 고유 문양 각인(아래 참고), 초록 발광 라인 |
| 유니크(Unique) | `#ff8c2a` | 화려한 장식, 강한 주황 발광, 독자적 형태(다른 등급과 실루엣부터 다르게) |
| 제작(Crafted) | `#6adfff` | 정교한 대칭 세공, 하늘색 발광 |

### 강화 단계별 표현 (`item.enhance`, 0~`MAX_ENHANCE`=15)

| 강화 범위 | 시각 escalation |
|---|---|
| +0 ~ +4 | 변화 없음 (베이스 등급 표현 그대로) |
| +5 ~ +9 | 무기 날/보석에 옅은 발광 라인 추가 |
| +10 ~ +14 | 발광 강도 상승 + 무기 주변에 작은 파티클(등급 컬러) |
| +15 (MAX) | 무기 전체에 등급 컬러 아우라 + 무기 끝에서 빛 입자가 흘러내리는 표현 |

---

## 예시 프롬프트 (실제 프로젝트 데이터 기반)

### ITEM_WEAPON_SWORD_NORMAL_001
```
[MASTER STYLE BLOCK 삽입]
Subject: a single plain steel one-handed sword icon, straight blade,
simple leather-wrapped hilt, no gems, no glow, slightly worn edge.
Icon composition: weapon angled diagonally (bottom-left to top-right),
filling ~80% of frame, plain dark neutral background for easy cutout.
```

### ITEM_WEAPON_SWORD_RARE_001
```
[MASTER STYLE BLOCK 삽입]
Subject: a rare-quality one-handed sword icon, gold-and-silver
filigree along the fuller, a single amber gem set in the crossguard,
faint golden glow (#ffd84a) along the blade edge. Same icon composition
as ITEM_WEAPON_SWORD_NORMAL_001 for visual consistency across rarities.
```

### ITEM_WEAPON_SWORD_UNIQUE_001 — "태초의 대검" (`UNIQUES` 데이터)
- 코드 근거: `{ name:'태초의 대검', slot:'weapon', base:{atk:22,crit:3}, flavor:'세상이 갈라지기 전에 벼려진 검.' }`
```
[MASTER STYLE BLOCK 삽입]
Subject: a legendary greatsword icon called "the Sword of the First
Sundering" — a massive ancient blade with a crack of primordial light
running down its center, dark weathered steel base with bright orange
glow (#ff8c2a) emanating from the crack, ornate ancient runes etched
along the fuller. Must look distinctly more elaborate than any Rare-tier
sword — this is the game's signature legendary weapon.
```
**적용 위치**: `UNIQUES` 배열의 `'태초의 대검'` 항목에 `iconKey` 필드 추가

---

## SETS(4종) 시그니처 무기 아이콘

`SETS` 데이터 기준, 세트별로 통일된 디자인 언어를 부여한다 (같은 세트의 무기/방어구/장신구는 같은 장식 모티프를 공유해야 세트라는 게 시각적으로 전달됨).

| Asset ID | 세트 | 코드 근거 | 디자인 모티프 |
|---|---|---|---|
| `ITEM_SET_HERO_001` | 용사의 세트 | `hero`, 초록(#3ddc6a) | 고전적인 영웅상 — 심플한 십자 가드, 태양 문양 |
| `ITEM_SET_SHADOW_001` | 그림자 세트 | `shadow`, 초록(세트 공통) | 암살자풍 단검, 검은 가죽 + 어두운 자수정 장식 |
| `ITEM_SET_GUARDIAN_001` | 수호자의 세트 | `guardian` | 육중한 판금 워해머/판금 갑주, 방패 문양 각인 |
| `ITEM_SET_ABYSS_001` | 심연 세트 | `abyss` (5부위 세트 — 유일하게 helmet/gloves/boots까지 있는 풀세트) | 심연 크랙 문양이 전 부위에 이어지는 대검+흉갑+투구+건틀릿+각반 |

**예시 (ITEM_SET_ABYSS_WEAPON_001 — "심연의 대검")**
```
[MASTER STYLE BLOCK 삽입]
Subject: a large two-handed sword icon from the "Abyss Set" (5-piece set:
weapon, armor, helmet, gloves, boots) — dark obsidian blade with glowing
cyan void-crack lines running through it (matching ENV_DUNGEON2 palette),
green set-bonus glow (#3ddc6a) along the crossguard to signal active set
bonus in the UI. The crack motif must be repeated identically across all
5 abyss-set pieces for visual cohesion.
```

---

## 룬/보석/성장무기 (별도 소형 아이콘 세트)

- `RUNES`, `GEMS`: 각각 색상 코드(`RARITY.rune #c77dff`, `GEM_STAT_LABELS` 참고)에 맞춘 작은 보석/룬석 아이콘 1세트씩.
- 성장무기(`GROWTH_PATHS`: blood/storm/titan × `GROWTH_WEAPON_STAGES` 4단계 = 12장): 캐릭터 프롬프트 문서의 "성장무기 표현" 절 참고. 예: `blood` 경로는 붉은 흡혈 오라, `storm` 경로는 전기/번개 이펙트, `titan` 경로는 두꺼운 암석질 텍스처로 무기가 점점 커지는 4단계 진화를 표현.
