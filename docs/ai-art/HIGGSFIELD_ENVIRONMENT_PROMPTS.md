# HIGGSFIELD_ENVIRONMENT_PROMPTS

중요: 이 게임의 "환경"은 실제로는 **평평한 `CircleGeometry` 바닥 + 박스/실린더 프리미티브 건물/기둥**이다.
따라서 Higgsfield로 만드는 것은 새 지형 모델이 아니라 **① 바닥 타일링 텍스처 ② 건물 표면 텍스처 ③ 장식 소품 스프라이트(빌보드)**다.
이 세 가지를 기존 프리미티브 지오메트리 위에 입히는 것만으로 "새로 만든 맵처럼" 보이게 하는 것이 목표.

데이터 출처: `buildArena()`, `buildTown2()`, `buildTown3()`, `DUNGEON*_CENTER` 등.

## 플레이 공간 고려사항 (모든 지역 공통)

- **Player Navigation**: 바닥 텍스처는 이동 가능 영역과 장애물(바위/건물)의 경계가 명확히 구분되어야 한다 (`obstacles` 배열의 충돌 반경과 시각적으로 일치해야 플레이어가 헷갈리지 않음).
- **Combat/Monster Spawn Area**: 던전은 원형 아레나(`ARENA_RADIUS`)이므로, 바닥 텍스처는 중심에서 가장자리로 갈수록 자연스럽게 이어지는 원형 대칭 타일이어야 한다.
- **Boss Arena**: 보스 스폰 지점(`DUNGEON_CENTER.z - 4` 등)에 시각적 랜드마크(균열, 제단, 왕좌 등)를 텍스처/소품으로 배치해 "여기가 보스룸이다"를 사전에 암시.
- **Safe Area**: 마을은 위협적이지 않은 밝은 톤 유지.
- **Chokepoint/Landmark**: 던전 입구 포탈(`makePortal()`)은 이미 발광 링으로 구현되어 있으므로 색만 지역 테마에 맞춰 재활용 (신규 생성 불필요).

---

## ENV_TOWN1_001 — 크로노스 마을 (시작 마을)

- **코드 근거**: `townGround`(원형 초록 바닥), `makeHouse()`, `makeWell()` 등
- **테마**: 자연광 초록~올리브, 목조 건축

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless, tileable top-down ground texture for a medieval
fantasy village — packed dirt path bordered by short green grass,
subtle stone cobbles near the village well area. Warm daylight color
grading, olive-green palette (#5a8a4a family). No characters, no
buildings in frame — ground texture only, orthographic top-down angle.
```
**후처리**: 원형 반복(seamless tile) 검증 필수. `arenaGround` 대체용 `RepeatWrapping` 텍스처로 적용.
**적용 위치**: `townGround.material.map` (신규 `TextureLoader` 필요)

### ENV_TOWN1_PROP_HOUSE_001 — 목조 가옥 표면
```
[MASTER STYLE BLOCK 삽입]
Subject: a flat, front-facing texture sheet of a rustic wooden cottage
wall with a thatched or red-clay tile roof edge, warm brown wood grain,
small shuttered window. Designed to be UV-mapped onto a simple box shape
— avoid perspective distortion, keep it as a flat elevation/orthographic
view of the building facade.
```
**적용 위치**: `makeHouse()`가 생성하는 박스 메쉬의 `material.map`

---

## ENV_TOWN2_001 — 항구 마을

- **코드 근거**: `TOWN2_CENTER`, `buildTown2()` — drowned/seaghost/orc 필드
- **테마**: 차가운 청록~남색, 부두/목재 판자

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture for a weathered
wooden harbor pier mixed with wet sand patches, cool teal-blue color
grading (#3a5a5a family), faint water reflections at the tile edges.
Orthographic top-down angle, no characters, ground texture only.
```
**적용 위치**: `townGround2.material.map`

---

## ENV_TOWN3_001 — 사막 마을

- **코드 근거**: `TOWN3_CENTER`, `buildTown3()` — sandwraith/scarab/archer 필드
- **테마**: 황토~금색

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture of packed desert
sand with subtle wind-ripple patterns and scattered small rocks,
warm ochre-gold color grading (#c9a86a family). Orthographic top-down
angle, no characters, ground texture only.
```
**적용 위치**: `townGround3.material.map`

---

## ENV_DUNGEON1_001 — 오크 소굴 (Town1 소속)

- **코드 근거**: `DUNGEON_CENTER`, `buildArena()` — 오크/고블린 웨이브, 보스 오크 군주
- **테마**: 어두운 갈황토 바닥 + 주황 횃불

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture for a crude orc
war-camp cave floor — packed dirt with bone fragments, wooden stake
fences, and dried blood-brown stains, dark warm-brown palette (#4a4238
family). Orthographic top-down angle, ground texture only.
```
### ENV_DUNGEON1_PROP_PILLAR_001 — 기둥
```
[MASTER STYLE BLOCK 삽입]
Subject: a flat front-facing texture of a crude stone/wood support pillar
wrapped with tribal orc totems and animal skulls, dark stone base.
Designed for UV-mapping onto a cylinder shape.
```
**적용 위치**: `arenaGround.material.map`, 기둥 실린더 메쉬의 `material.map`

---

## ENV_DUNGEON2_001 — 심연의 균열 (Town2 소속)

- **코드 근거**: `DUNGEON2_CENTER` — wraith/sentinel/tentacle, 보스 심연의 지배자
- **테마**: 차가운 남색 + 균열 발광

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture of cracked dark
obsidian-like stone with faint glowing cyan void-cracks running through
it, cold navy-blue palette (#2a4a6a family). Orthographic top-down
angle, ground texture only.
```
**적용 위치**: `arenaGround2.material.map`

---

## ENV_DUNGEON3_001 — 침몰한 유적 (Town2 소속)

- **코드 근거**: `DUNGEON3_CENTER` — 보스 잊혀진 해적왕
- **테마**: 물에 잠긴 폐허, 청록

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture of a flooded
sunken stone ruin floor — cracked marble slabs partially submerged in
shallow water, barnacles and seaweed growth, cool slate-teal palette
(#2a3a4a family). Orthographic top-down angle, ground texture only.
```
**적용 위치**: `arenaGround3.material.map`

---

## ENV_DUNGEON4_001 — 모래 무덤 (Town3 소속)

- **코드 근거**: `DUNGEON4_CENTER` — 보스 파라오의 저주
- **테마**: 고대 이집트풍 무덤, 황금~황토

```
[MASTER STYLE BLOCK 삽입]
Subject: a seamless tileable top-down ground texture of an ancient
pharaoh's tomb floor — sand-worn sandstone tiles with faint gold
hieroglyph engravings, warm ochre-gold palette (#c9a227 family).
Orthographic top-down angle, ground texture only.
```
### ENV_DUNGEON4_PROP_STATUE_001 — 보스룸 랜드마크
```
[MASTER STYLE BLOCK 삽입]
Subject: a flat front-facing texture/cutout of a tall ancient Egyptian-
style pharaoh statue or obelisk, weathered gold-tan sandstone, placed as
a landmark prop to visually announce a boss arena.
```
**적용 위치**: `arenaGround4.material.map` + 보스 스폰 지점 랜드마크 스프라이트

---

## Modular Prop Kit (지역 공통 재사용 소품, 빌보드 스프라이트)

3D 모듈 배치 대신, 아래 소품을 **빌보드 스프라이트**로 만들어 여러 지역에서 색만 바꿔 재사용한다.

| Asset ID | 소품 | 재사용 지역 |
|---|---|---|
| `PROP_TREE_001` | 침엽수/활엽수 | Town1 필드 (기존 `THREE.ConeGeometry` 나무 대체) |
| `PROP_ROCK_001` | 바위 | 전 지역 공통 (`makeRock()` 대체) |
| `PROP_TORCH_001` | 벽걸이 횃불 | 던전 전체 공통 (색 온도만 통일) |
| `PROP_PILLAR_001` | 던전 기둥 | 던전 전체 (지역별 텍스처만 교체) |
| `PROP_STATUE_001` | 석상/우상 | 던전2, 던전4 |
| `PROP_DEBRIS_001` | 잔해/뼈 더미 | 던전1(오크뼈), 던전3(난파선 잔해) |
| `PROP_GATE_001` | 보스룸 입구 게이트 | 던전 전체 (`showWaveBanner`로 "보스 출현!" 나올 때 시각 강조용) |

각 소품은 `HIGGSFIELD_MASTER_PROMPT.md` 스타일로 1장씩만 생성하고, 지역별로는 **색조(hue) 오버레이만 다르게 적용**해 재사용한다 (제작 비용을 최소화하는 것이 원래 요청의 "반복 제작 비용" 우선순위와 일치).
