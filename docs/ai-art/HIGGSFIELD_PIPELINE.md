# HIGGSFIELD_PIPELINE

## MCP 연동 가능성 (실제 확인 결과)

- 이 개발 환경(Claude Code 세션)에는 **Higgsfield MCP가 설치/연결되어 있지 않다** (커넥터 레지스트리 검색 결과 없음).
- Higgsfield 공식 사이트는 "Higgsfield MCP"를 언급하지만, 이는 **Higgsfield Supercomputer(자체 게임 생성·호스팅 에이전트)**에 에이전트를 연결하는 통합이지, "이미지 생성 API를 MCP로 호출해서 로컬에 PNG를 받는" 범용 연동은 아닌 것으로 보인다(공식 문서로 100% 확인되지 않음 — 실제 사용 전 Higgsfield 계정에서 직접 검증 필요).
- **확실히 되는 것**: Higgsfield API(REST, API 키 발급 가능)로 이미지 생성 요청 → 결과 이미지 URL을 받는 것. 이건 문서화된 표준 HTTP API이므로 신뢰할 수 있다.

이 문서는 위 사실에 따라 **MANUAL WORKFLOW**(지금 바로 가능)와 **MCP WORKFLOW**(설치·검증 후 가능, 현재는 설계만)를 분리한다.

---

## MANUAL WORKFLOW (지금 바로 실행 가능)

```
1. HIGGSFIELD_MASTER_PROMPT.md 의 스타일 블록 + 해당 애셋 문서의 프롬프트를 조합
2. Higgsfield 웹 UI(Nano Banana Pro 또는 GPT Image 2)에 붙여넣어 이미지 생성
3. 결과물 다운로드 (PNG)
4. 배경 제거
   - Higgsfield 자체 "컷아웃" 기능이 실제로 이미지에도 적용되는지 확인 필요(홈페이지의 "컷아웃"은 영상 프리셋으로 보임 — 검증 안 됨)
   - 검증 안 되면 remove.bg, Photoshop, GIMP, 또는 로컬 rembg 같은 별도 배경 제거 도구 사용
5. 알파 채널 PNG를 리사이즈(캐릭터/몬스터: 표시 크기의 2배 정도로 다운스케일, 아이콘: 128x128)
6. 파일을 저장소의 assets/sprites/{characters|monsters|items|environment}/ 에 저장
7. 게임 데이터(MONSTER_DEFS 등)에 spriteKey 필드 추가
8. 엔진에 스프라이트 로딩/렌더링 코드 추가 (아래 "엔진 통합" 참고)
9. 브라우저에서 실제로 확인 (기존 세션에서 쓰던 cache-busting 리로드 방식 그대로 사용)
```

## MCP WORKFLOW (설치·검증 후 목표 — 현재는 미보유)

```
Game Definition (MONSTER_DEFS 등 JS 객체)
  ↓
Codex/Claude 가 데이터 필드를 읽어 프롬프트 문자열 자동 조합 (HIGGSFIELD_*_PROMPTS.md 템플릿 기반)
  ↓
Higgsfield MCP 툴 호출 (설치/연결 후, 실제 툴 이름은 연결 시점에 확인)
  ↓
생성된 이미지 URL/파일을 로컬 assets/sprites/ 에 저장
  ↓
배경 제거 자동화 (별도 로컬 스크립트 또는 API — Higgsfield 자체 지원 여부 미검증)
  ↓
게임 데이터에 spriteKey 자동 기입
  ↓
브라우저에서 자동 리로드 및 스크린샷 확인
```
**주의**: 이 흐름은 Higgsfield MCP가 실제로 "이미지를 로컬 파일로 저장 가능한 API 호출"을 노출한다는 전제인데, 이건 아직 검증되지 않았다. 검증 전까지는 MANUAL WORKFLOW를 기본으로 쓴다.

---

## 엔진 통합 (Three.js, 실제 코드 변경 지점)

현재 `index.html`에는 `THREE.TextureLoader`가 전혀 쓰이지 않는다. 스프라이트 도입을 위한 최소 변경:

1. **텍스처 로더 추가**: `const textureLoader = new THREE.TextureLoader();`
2. **스프라이트 헬퍼 함수 추가**:
   ```js
   function loadSprite(path, widthWorldUnits, heightWorldUnits){
     const tex = textureLoader.load(path);
     tex.magFilter = THREE.NearestFilter; // 저해상도 아트 특유의 선명함 유지 여부는 아트 스타일에 맞춰 결정
     const mat = new THREE.SpriteMaterial({ map:tex, transparent:true });
     const sprite = new THREE.Sprite(mat);
     sprite.scale.set(widthWorldUnits, heightWorldUnits, 1);
     return sprite;
   }
   ```
3. **`spawnMonster()` 확장**: `def.spriteKey`가 있으면 기존 프리미티브 메쉬 생성 로직 대신(혹은 함께) `loadSprite()` 결과를 `g.add(...)`. 프리미티브 로직은 스프라이트가 없는 몬스터를 위한 폴백으로 유지 — 이렇게 하면 21종을 한 번에 다 바꾸지 않고 점진적으로 교체 가능.
4. **`makePlayerMesh()`도 동일 패턴**으로 확장.
5. **아이템 아이콘**: 인벤토리 렌더링 부분(`renderInventory()` 등)에서 `SLOT_ICON` 이모지 대신 `<img>` 태그로 교체 — 이건 THREE.js와 무관한 순수 DOM/CSS 변경이라 가장 리스크가 낮다. **우선순위 1순위로 추천.**
6. **바닥/건물 텍스처**: 기존 `MeshStandardMaterial({color:...})`의 `color` 대신 `map: textureLoader.load(path)` 로 교체 + `texture.wrapS/wrapT = THREE.RepeatWrapping`으로 타일링.

이 변경들은 전부 **기존 함수에 조건부 분기를 추가하는 방식**이라, 기존 게임플레이/밸런스 코드를 전혀 건드리지 않는다.

---

## AI Asset Pipeline 자동화 설계 (장기 목표)

```
MONSTER_DEFS 에 새 몬스터 항목 추가 (기존처럼 hp/atk/color 등 수치 입력)
  ↓
Codex가 새로 추가된 항목을 감지 (spriteKey 없음 → 아직 아트 없음으로 판단)
  ↓
GAME_VISUAL_BIBLE.md + 해당 몬스터의 color/scale/shape/ranged 필드를 읽어
  HIGGSFIELD_MONSTER_PROMPTS.md 형식의 프롬프트 자동 초안 생성
  ↓
(사용자 검토/승인)
  ↓
Higgsfield에서 이미지 생성 (MANUAL 또는 MCP WORKFLOW)
  ↓
배경 제거 + 리사이즈
  ↓
assets/sprites/monsters/{key}.png 로 저장
  ↓
MONSTER_DEFS[key].spriteKey = key 자동 기입
  ↓
브라우저 프리뷰로 실제 스폰 후 스크린샷 확인 (이번 세션에서 계속 써온 검증 방식 그대로)
```

이 흐름이 갖춰지면, 앞으로 "새 몬스터 하나 추가해줘" 요청 시 **①데이터 작성 → ②프롬프트 초안 자동 생성 → ③(승인 후) 이미지 생성 안내 → ④엔진 연결 → ⑤브라우저 검증**까지 지금까지 이 세션에서 해온 작업 방식(코드 수정 → 문법 검사 → 브라우저 라이브 검증 → 커밋)과 동일한 리듬으로 처리할 수 있다.

---

## 지금 당장 만들 첫 Batch (Vertical Slice 우선순위)

1. **아이템 아이콘 5등급 템플릿** (`HIGGSFIELD_ITEM_PROMPTS.md`) — 엔진 변경이 가장 적고(순수 DOM), 인벤토리 화면에 상시 노출되어 체감 효과가 큼.
2. **플레이어 캐릭터 4종** (`HIGGSFIELD_CHARACTER_PROMPTS.md`) — 항상 화면 중앙에 보임.
3. **초반 필드 몬스터 3종**: 고블린/늑대/오크 (`MON_GOBLIN_001`/`MON_WOLF_001`/`MON_ORC_001`)
4. **보스 5종** (`BOSS_*_001`) — 데모 티가 가장 많이 나는 지점.
5. 이후 나머지 몬스터/환경/애니메이션 Phase 2로 순차 진행.
