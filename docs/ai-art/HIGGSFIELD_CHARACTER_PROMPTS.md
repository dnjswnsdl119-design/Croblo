# HIGGSFIELD_CHARACTER_PROMPTS

플레이어블 클래스 4종. 데이터 출처: `index.html`의 `CLASS_DEFS`.
모든 프롬프트는 `HIGGSFIELD_MASTER_PROMPT.md`의 MASTER STYLE BLOCK을 앞에 붙여서 사용한다 (아래는 피사체 설명 부분만).

---

## CHAR_FIGHTER_001 — 파이터

- **현재 게임에서의 역할**: 근접 밸런스 전사. 검+방패, 공수 균형, 초반 튜토리얼 클래스로 추정(4클래스 중 첫 항목).
- **현재 Asset 존재 여부**: New (현재는 파란색 캡슐+구)
- **코드 근거**: `CLASS_DEFS.fighter` — `color:0x3a6ea5`(파랑), `ranged:false`, desc: "검과 방패를 다루는 근접 전사"

**Higgsfield Prompt**
```
[MASTER STYLE BLOCK 삽입]

Subject: a human male warrior in blue-and-steel plate armor, wielding a
one-handed longsword and a round shield with a simple heraldic emblem.
Balanced, sturdy build (not bulky, not slender) — reads as a well-rounded
melee fighter. Primary color: deep blue (#3a6ea5) armor accents over
gunmetal steel plate. Confident forward-facing combat stance, weight
slightly forward as if ready to step into a swing. Short cape optional,
kept small so it doesn't obscure the silhouette. Sword blade has a
faint worn edge, not pristine.
```

**Negative/Avoid**: 마스터 네거티브 블록 그대로 + `no two-handed weapons, no ranged weapons, no dual-wielding`

**필요한 후처리**
- 배경 제거 → 알파 채널 PNG
- 1024×1024 생성 후 게임 내 표시 크기(약 64×96px)에 맞춰 다운스케일 + 선명화(sharpen) 1회
- (Phase 2) 동일 시드/캐릭터 참조로 `idle / attack / hit / death` 4포즈 추가 생성 (`HIGGSFIELD_ANIMATION_PROMPTS.md` 참고)

**적용 위치**: `CLASS_DEFS.fighter`에 `spriteKey:'fighter'` 필드 추가 → `assets/sprites/characters/fighter_idle.png`

---

## CHAR_SAVAGE_001 — 새비지

- **현재 게임에서의 역할**: 최고 맷집 근접 전사, 거대 무기 사용.
- **코드 근거**: `CLASS_DEFS.savage` — `color:0x8a4a2a`(적갈색), desc: "거대한 무기를 휘두르는 야성의 전사. 최고의 맷집과 지속 전투력"

**Higgsfield Prompt**
```
[MASTER STYLE BLOCK 삽입]

Subject: a heavily-built barbarian warrior wielding an oversized two-handed
greataxe or greatsword resting on one shoulder. Wears fur-and-hide armor
mixed with rough iron plates, primary color rust-brown/copper (#8a4a2a).
Broad, tank-like silhouette — noticeably bulkier than a standard knight.
Wild hair or beard, tribal/primal aesthetic rather than noble knight
aesthetic. Battle scars visible but not gory. Wide, grounded stance.
```

**Negative/Avoid**: 마스터 네거티브 + `no shield, no light/slender build, no clean noble-knight aesthetic`

**후처리**: 위와 동일.
**적용 위치**: `CLASS_DEFS.savage.spriteKey='savage'` → `assets/sprites/characters/savage_idle.png`

---

## CHAR_VALKYRIE_001 — 발키리

- **현재 게임에서의 역할**: 민첩/크리티컬 특화 근접 전사, 창 사용, 높은 기동력.
- **코드 근거**: `CLASS_DEFS.valkyrie` — `color:0xaa4a8a`(자주색), desc: "창과 속도로 적을 베어내는 민첩한 전사. 높은 치명타와 기동력"

**Higgsfield Prompt**
```
[MASTER STYLE BLOCK 삽입]

Subject: an agile female warrior wielding a long spear/polearm with a
winged or feathered ornamental motif (valkyrie theme, NOT literal angel
wings on the back — keep it grounded fantasy, not divine/holy). Lighter
segmented armor with more exposed mobility joints than the fighter/savage,
primary color magenta-purple (#aa4a8a) with silver trim. Slender but
athletic build, dynamic mid-lunge or ready-to-dash pose to convey speed
and agility. Small cloth streamers or ribbons that suggest fast movement.
```

**Negative/Avoid**: 마스터 네거티브 + `no heavy bulky armor, no literal angel wings, no shield`

**후처리**: 위와 동일.
**적용 위치**: `CLASS_DEFS.valkyrie.spriteKey='valkyrie'` → `assets/sprites/characters/valkyrie_idle.png`

---

## CHAR_MAGICIAN_001 — 매지션

- **현재 게임에서의 역할**: 원거리 원소 마법사. 방어구 무시 딜러, 낮은 방어력.
- **코드 근거**: `CLASS_DEFS.magician` — `color:0x4a2a8a`(진보라), `ranged:true, range:8`, desc: "원소 마법으로 원거리에서 적을 제압하는 지혜의 마법사"

**Higgsfield Prompt**
```
[MASTER STYLE BLOCK 삽입]

Subject: a robed elemental mage holding an ornate wooden/crystal-topped
staff, faint arcane energy glowing at the staff's tip and along
embroidered rune patterns on the robe hem. Primary color deep indigo-purple
(#4a2a8a) robe with gold rune trim. Slim, unarmored silhouette (cloth only,
no plate armor — this class has low defense and the silhouette should
communicate "fragile spellcaster" at a glance). Hood up or elaborate
hairstyle, contemplative/casting posture with one hand raised.
```

**Negative/Avoid**: 마스터 네거티브 + `no armor plates, no melee weapons, no visible muscular bulk`

**후처리**: 위와 동일.
**적용 위치**: `CLASS_DEFS.magician.spriteKey='magician'` → `assets/sprites/characters/magician_idle.png`

---

## 공통: 성장무기(Growth Weapon) 표현

`GROWTH_PATHS`(blood/storm/titan)와 `GROWTH_WEAPON_STAGES`(4단계)는 현재 색상 틴트(`updateWeaponVisual()`)로만 표현된다.
스프라이트 전환 후에는 **무기를 캐릭터 스프라이트와 분리된 별도 레이어**로 얹는 것을 권장한다 (무기 스프라이트 3종 × 4단계 = 12장, `HIGGSFIELD_ITEM_PROMPTS.md`의 성장무기 절 참고). 이렇게 하면 캐릭터 몸체 스프라이트를 재생성하지 않고도 무기 성장 단계를 갈아 끼울 수 있다.
