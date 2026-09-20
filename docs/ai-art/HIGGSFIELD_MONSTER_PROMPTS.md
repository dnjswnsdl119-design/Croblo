# HIGGSFIELD_MONSTER_PROMPTS

데이터 출처: `index.html`의 `MONSTER_DEFS`(21종: 일반 16 + 보스 5).
모든 프롬프트는 `HIGGSFIELD_MASTER_PROMPT.md`의 MASTER STYLE BLOCK을 앞에 붙인다.
지역별 컬러는 `GAME_VISUAL_BIBLE.md`를 따른다.

---

# Tier 1 — 최우선 (등장 빈도 최고, Town1 필드)

## MON_GOBLIN_001 — 고블린

- **역할**: 초반 필드 최빈출 일반 몬스터 (`MONSTER_DEFS.goblin`)
- **코드 근거**: `hp:30, atk:5, color:0x5a8a4a(연두), scale:1.0`, 근접
- **존재 여부**: New

```
Subject: a small, wiry green-skinned goblin, hunched posture, holding a
crude short sword or rusty dagger. Primary color olive-green (#5a8a4a)
skin, ragged brown-leather scraps as clothing (no full armor — this is a
weak early-game enemy and should read as scrappy/disposable at a glance).
Slightly mischievous, feral facial expression. Small scale relative to a
human (about shoulder-height to a knight).
```
**Negative**: 마스터 네거티브 + `no full armor, no heroic pose, not cute/friendly`
**후처리**: 배경 제거, 512×512로 충분(작은 몬스터).
**적용 위치**: `MONSTER_DEFS.goblin.spriteKey='goblin'`

## MON_WOLF_001 — 늑대

- **역할**: 초반 필드 빠른 일반 몬스터 (`MONSTER_DEFS.wolf`)
- **코드 근거**: `hp:22, atk:7, speed:3.4(가장 빠름), color:0x777788(회청)`

```
Subject: a lean, feral grey-blue wolf (#777788) with lowered head and
bared fangs, mid-stride running/pouncing pose to convey its high speed.
Slightly larger and more menacing than a realistic wolf — fantasy
predator, not a cute animal. No armor, no rider, pure beast silhouette.
```
**Negative**: 마스터 네거티브 + `no domestic-dog cuteness, no armor/tack`
**적용 위치**: `MONSTER_DEFS.wolf.spriteKey='wolf'`

## MON_ORC_001 — 오크 전사

- **역할**: 중반 필드 탱키한 근접 몬스터 (`MONSTER_DEFS.orc`)
- **코드 근거**: `hp:55, atk:13, def:4, color:0x6b5a3a(카키브라운), scale:1.25`

```
Subject: a stocky green-brown orc warrior (#6b5a3a) in crude iron plate
scraps over hide, wielding a heavy cleaver or axe. Broader and more
armored than the goblin — should visually read as tougher/higher-tier
than MON_GOBLIN_001 while sharing the same greenskin family design
language (tusks, coarse skin).
```
**Negative**: 마스터 네거티브 + `not identical to goblin design, no polished knight armor`
**적용 위치**: `MONSTER_DEFS.orc.spriteKey='orc'`

## MON_ARCHER_001 — 해골 궁수

- **역할**: 원거리 딜러형 초중반 몬스터 (`MONSTER_DEFS.archer`, `ranged:true`)
- **코드 근거**: `hp:18(낮음), atk:8, color:0xd8d3c0(뼈색)`

```
Subject: an undead skeletal archer, bleached bone-white/ivory (#d8d3c0)
frame held together by tattered dark cloth wraps, drawing a simple
recurve bow. Thin, fragile-looking silhouette (low HP enemy) but the bow
and drawn arrow must be very clearly readable at a distance for gameplay
readability (players need to instantly identify "ranged threat").
```
**Negative**: 마스터 네거티브 + `no flesh/zombie look (must read as bone), no melee weapon`
**적용 위치**: `MONSTER_DEFS.archer.spriteKey='archer'`

---

# Tier 1 — 보스 5종 (임팩트 최고, 등장 빈도 낮음)

## BOSS_CHIEF_001 — 고블린 대장

- **역할**: Town1 필드 보스, 던전1 열쇠 드랍 (`MONSTER_DEFS.chief`, `isBoss:true`)
- **코드 근거**: `hp:90, color:0xcc8833(황동색), scale:1.6`

```
Subject: a larger, battle-scarred goblin chieftain wearing a crude brass/
bronze-colored (#cc8833) crown or trophy-skull headdress, wielding a
bigger jagged cleaver or a spiked club. Noticeably larger than
MON_GOBLIN_001 (about 1.6x) with trophy bones/scalps hanging from a belt
to signal "boss of the goblin tribe." Must be instantly recognizable as
an upgraded/leader version of the regular goblin, sharing its color
family but with gold/brass leader accents.
```
**Negative**: 마스터 네거티브 + `not a completely different creature — must read as goblin's leader`
**후처리**: 1024×1024, 보스는 인게임 임팩트가 크므로 (Phase 2) 등장 연출용 별도 클로즈업 컷 1장 추가 권장.
**적용 위치**: `MONSTER_DEFS.chief.spriteKey='chief_boss'`

## BOSS_ORCLORD_001 — 오크 군주

- **역할**: 던전1(오크 소굴) 보스, 던전2 열쇠 드랍, Danger Zone에도 등장 (`isBoss:true`, 페이즈 시스템 적용 대상)
- **코드 근거**: `hp:220, color:0x9a3a2a(진적갈), scale:2.0, aggro:20`

```
Subject: a massive, imposing orc warlord in heavy dark-red-brown (#9a3a2a)
battle plate, wielding a large two-handed warhammer or greataxe with
trophy chains. Significantly larger and more armored than MON_ORC_001
(about 2x scale) — a clear "raid boss" silhouette. Wide battle stance,
intimidating posture. Because this boss has a 3-phase enrage system in
gameplay, design the base pose to allow a visually distinct "enraged"
variant later (e.g. cracked armor, glowing red eyes version).
```
**Negative**: 마스터 네거티브 + `not a reskinned generic ogre, must clearly be an orc leader`
**적용 위치**: `MONSTER_DEFS.orclord.spriteKey='orclord_boss'`

## BOSS_ABYSSLORD_001 — 심연의 지배자

- **역할**: 던전2(심연의 균열) 보스 (`isBoss:true`)
- **코드 근거**: `hp:420, color:0x2a4a6a(짙은청), scale:2.4`, 지역 테마: 심연/차가운 청록

```
Subject: an eldritch abyssal entity, deep navy-blue (#2a4a6a) chitinous
or crystalline body with tentacle-like or void-crack details, faintly
glowing cyan cracks across its form suggesting a rift/void origin. Should
feel alien and unsettling compared to the more "grounded" orc/goblin
bosses — this is the abyss-dungeon final boss. Large, imposing silhouette
(2.4x base scale), floating or hovering stance optional.
```
**Negative**: 마스터 네거티브 + `no orc/humanoid warrior design language, must feel otherworldly`
**적용 위치**: `MONSTER_DEFS.abysslord.spriteKey='abysslord_boss'`

## BOSS_PIRATEKING_001 — 잊혀진 해적왕

- **역할**: 던전3(침몰한 유적) 보스 (`isBoss:true`)
- **코드 근거**: `hp:480, color:0x2a3a4a(짙은남색), scale:2.3`, 테마: 익사한 선원들의 지역

```
Subject: a drowned, decaying ghost-pirate king, dark navy-slate (#2a3a4a)
tattered admiral's coat waterlogged and barnacle-encrusted, wielding a
corroded cutlass and holding a ghostly lantern or a cursed ship's wheel.
Ragged tricorn hat silhouette to read as "pirate" instantly. Pale,
drowned-corpse skin tone with a faint sickly cyan glow (curse/undeath).
```
**Negative**: 마스터 네거티브 + `not a cheerful/comedic pirate, must read as undead and menacing`
**적용 위치**: `MONSTER_DEFS.pirateking.spriteKey='pirateking_boss'`

## BOSS_PHARAOH_001 — 파라오의 저주

- **역할**: 던전4(모래 무덤) 최종 보스 (`isBoss:true`, `shape:'crystal'`)
- **코드 근거**: `hp:560(전체 최고), atk:31(전체 최고), color:0xc9a227(고대금), scale:2.35`

```
Subject: an ancient cursed pharaoh, mummified/undead pharaoh king wrapped
in tattered golden-tan (#c9a227) burial linens over ornate gold-and-lapis
funerary armor, holding a crook-and-flail sceptre or a curved khopesh
sword. Egyptian-tomb aesthetic (original design, not copying any real
media's specific character) — gold headdress, glowing amber/gold eyes.
This is the game's strongest boss, so the silhouette should read as the
most elaborate and largest of all five bosses.
```
**Negative**: 마스터 네거티브 + `no green cartoon-mummy look, no bandaged-comedy-mummy trope`
**적용 위치**: `MONSTER_DEFS.pharaoh.spriteKey='pharaoh_boss'`

---

# Tier 2 — 나머지 필드/던전 몬스터 (8종, 항구·심연·사막 테마)

동일한 마스터 스타일 블록 + 아래 요약 설명을 조합해서 생성한다. 각 항목의 `shape` 필드가 코드에 있으면 형태 힌트로 반드시 반영한다.

| Asset ID | 이름 | 코드 근거 | 지역/테마 | 피사체 설명 요약 |
|---|---|---|---|---|
| `MON_WRAITH_001` | 시간을 잃은 망령 | `color:0x8a5fd8, shape:'blob', ranged:true` | 던전2 (심연) | 보라색(#8a5fd8) 반투명 유령형 몸체(blob 실루엣), 시계/사슬 파편이 몸 주위를 떠다님, 원거리 저주 투사체를 쏘는 자세 |
| `MON_SENTINEL_001` | 균열의 파수병 | `color:0x4a5a7a, shape:'crystal'` | 던전2 (심연) | 남회색(#4a5a7a) 결정체 골렘, 각진 크리스탈 형태의 갑옷 같은 외피, 근접 방어형 육중한 자세 |
| `MON_TENTACLE_001` | 심연의 촉수 | `color:0x2a6a5a, shape:'blob'` | 던전2 (심연) | 짙은 청록(#2a6a5a) 촉수 덩어리, 바닥에서 솟아난 형태, 다리 없이 촉수로 지탱 |
| `MON_DROWNED_001` | 익사한 선원 | `color:0x3a5a5a` | 항구 마을 필드 | 짙은 청회색(#3a5a5a) 피부의 익사체 선원, 해초가 감긴 낡은 선원 복장, 녹슨 갈고리나 닻 사슬 무기 |
| `MON_SEAGHOST_001` | 저주받은 뱃사공 | `color:0x5a8a9a, shape:'blob', ranged:true` | 항구 마을 필드 | 청록(#5a8a9a) 반투명 유령 뱃사공, 낡은 노를 지팡이처럼 들고 원거리 저주를 캐스팅하는 자세 |
| `MON_SANDWRAITH_001` | 모래 망령 | `color:0xd8b878, shape:'blob', ranged:true` | 사막 마을 필드 | 황토색(#d8b878) 모래 소용돌이 형태의 정령, 몸체가 모래 입자로 흩날리는 표현 |
| `MON_SCARAB_001` | 저주받은 풍뎅이 | `color:0x4a3a2a` | 사막 마을 필드 | 짙은 갈색(#4a3a2a) 대형 갑충, 광택 있는 딱딱한 등껍질, 낮은 자세의 네발(또는 6족) 곤충형 |

**공통 Negative**: 마스터 네거티브 블록 그대로 적용.
**공통 후처리**: 512×512 생성 → 배경 제거 → 게임 표시 크기로 다운스케일.
**적용 위치**: 각 `MONSTER_DEFS.<key>.spriteKey` 필드에 매핑.

---

# Elite/Affix 오버레이 (신규 생성 아님 — 합성 규칙)

`MONSTER_AFFIXES`(분노한/거대한/신속한/태고의/희귀한)는 별도 스프라이트를 만들지 않는다.
대신 기본 몬스터 스프라이트 위에:
1. `affix.color`를 이용한 색상 오버레이(곱하기 블렌드, 20~30% 강도)
2. 발밑 컬러 링(이미 THREE.RingGeometry로 구현되어 있음 — 유지)
3. `giant` 어픽스만 스프라이트 자체를 1.35배 확대 렌더

이 방식은 21종 × 5어픽스 = 105장을 새로 만드는 대신 **21장 + 런타임 색상 합성**으로 해결한다 (`HIGGSFIELD_PIPELINE.md`의 "재사용 규칙" 참고).
