# 크로노스 소울 — AI 아트 파이프라인 기초 조사 결과

이 문서는 `/docs/ai-art/` 나머지 문서들이 어떤 전제 위에 서 있는지를 기록한다.
전제가 틀리면 나머지 문서가 전부 쓸모없어지므로, 항상 이 문서를 먼저 읽는다.

## 1. 실제 프로젝트 구조 (코드로 확인한 사실만)

- 저장소 파일은 3개뿐: `.gitignore`, `README.md`, `index.html`(약 6,550줄).
- 게임 엔진: Unity/Unreal/Godot 아님. **브라우저 HTML5 + Three.js**(ESM, importmap으로 CDN 로드) 단일 파일.
- Prefab / Blueprint / Scene 애셋 파일: 없음. 씬은 전부 JS 코드로 즉석 생성(`new THREE.Group()` 조립).
- Animation Controller / 스켈레탈 애니메이션: 없음. "애니메이션"은 히트 시 `mesh.scale.set(1.15,0.85,1.15)` 같은 JS 트윈/스쿼시-스트레치뿐.
- Material / Shader / Texture 파일: 없음. 전부 `MeshStandardMaterial({color:0x5a8a4a})` 같은 단색 재질. 이미지 파일 0개.
- 3D 모델 파일(.fbx/.glb/.obj): 없음. GLTFLoader/FBXLoader/OBJLoader 자체가 코드에 없어 애초에 외부 모델을 불러올 능력이 엔진에 없음.
- Audio 파일: 없음. 전부 `AudioContext` 오실레이터로 실시간 합성(`SFX.crit()` 등).
- 데이터 정의: JSON/ScriptableObject 아님. `index.html` 안의 JS 객체 리터럴(`MONSTER_DEFS`, `CLASS_DEFS`, `SETS`, `RARITY` 등).
- 몬스터/캐릭터 "모델": 캡슐+구+원뿔 등 **THREE.js 기하 프리미티브 조합**. 예: 고블린 = 초록색 캡슐 몸통 + 초록색 원뿔 머리.
- 아이템 아이콘: 실제 이미지가 아니라 **유니코드 이모지**(`⚔️🛡️💍` 등)를 텍스트로 표시.

## 2. Higgsfield 실제 기능 조사 결과 (2026-09-20, higgsfield.ai 직접 확인)

- Higgsfield API 모델 카탈로그의 카테고리는 **"전체 / 이미지 / 비디오"** 두 가지뿐이다. 3D 카테고리 자체가 없다.
- 등재된 모델은 전부 이미지·영상 생성 모델이다: Seedance/Kling/Sora/Veo3/Wan/MiniMax(비디오), Nano Banana/GPT Image/Flux/Seedream(이미지).
- Higgsfield가 "3D"를 언급하는 유일한 곳은 **"Higgsfield Supercomputer / MCP"**라는 별도 제품이며, 공식 설명은 다음과 같다:
  > "Describe the game and Supercomputer ships it. Hosting, build and a shareable URL are handled automatically." / "3D WORLDS FROM A SENTENCE — no modeling, no asset hunting."
- 즉 이것은 **프롬프트 하나로 게임 전체를 에이전트가 통째로 만들고 자체 호스팅까지 하는 서비스**이지, "몬스터 하나를 GLB 파일로 export해서 우리 엔진에 import"하는 개별 애셋 생성 API가 아니다.

### 결론
**"Higgsfield → 3D 메쉬(GLB/FBX) → Blender → 우리 Three.js 엔진에 import" 파이프라인은 Higgsfield 단독으로 실행 불가능하다.** 실제 3D 메쉬 export가 필요하다면 Meshy / Tripo3D / Rodin 같은 별도 도구가 필요하다 (이 프로젝트에서는 채택하지 않기로 결정 — 아래 3번 참고).

## 3. 채택한 방향 (사용자 확정, 2026-09-20)

1. 엔진을 3D 메쉬 임포트가 가능한 구조로 확장하지 않는다. 기존 Three.js 프리미티브 렌더링 구조는 그대로 유지한다.
2. Higgsfield의 **이미지 생성 모델**(Nano Banana Pro / GPT Image 2 / Flux / Seedream)로 캐릭터·몬스터·아이템·환경의 **2D 아트(스프라이트/아이콘/배경 텍스처)**를 만든다.
3. 엔진에는 `THREE.TextureLoader` + `THREE.Sprite`(또는 카메라를 항상 향하는 평면) 를 추가해 이 2D 아트를 3D 월드 위에 얹는다. 이 게임의 카메라는 회전하지 않는 고정 아이소메트릭 앵글이므로(`CAM_OFFSET` 고정), 스프라이트가 사실상 "고정된 2D 컷아웃"처럼 보여 정통 아이소메트릭 ARPG(구 디아블로류)와 동일한 방식이 자연스럽게 성립한다.
4. 스켈레탈 애니메이션은 도입하지 않는다. 대신 상태별(대기/공격/피격/사망) **정지 프레임 여러 장**을 만들어 텍스처를 교체하는 방식(간이 스프라이트시트)으로 "움직임"을 표현한다. 이는 Phase 2로 분류하고, Phase 1(MVP)은 상태당 1장(단일 정지 이미지)으로 시작한다.
5. 영상 생성 모델(Seedance/Kling/Sora)은 게임 내 에셋이 아니라 **타이틀 화면 배경 영상 / 트레일러 / 마케팅**용으로만 사용한다.

## 4. 이 결정이 바꾸는 것 / 안 바꾸는 것

| 항목 | 영향 |
|---|---|
| 전투 판정, 스탯 계산, 아이템 옵션, 몬스터 AI, 저장, 스킬 시스템 | **전혀 변경 없음.** 순수 시각 레이어만 교체 |
| `spawnMonster()`, `makePlayerMesh()` 등 메쉬 생성 함수 | 프리미티브 생성 로직 위/대신에 스프라이트를 얹는 방식으로 **확장**(교체 아님 — 프리미티브를 완전히 들어내는 대신, 먼저 나란히 두고 스프라이트 우선순위를 높이는 점진적 전환을 권장) |
| `MONSTER_DEFS` / `CLASS_DEFS` / `SETS` 등 데이터 객체 | 각 항목에 `spriteKey` 필드 하나만 추가하면 됨. 스탯/밸런스 필드는 무변경 |
| Animation Controller 연결 (원 요청 9번) | 존재하지 않는 개념이므로 "상태 → 텍스처 교체" 방식으로 재정의 (`HIGGSFIELD_ANIMATION_PROMPTS.md` 참고) |
| Modular Dungeon Kit (원 요청 11번) | 3D 모듈 배치가 아니라 **던전 테마별 배경/바닥 텍스처 + 장식 스프라이트 소품(기둥/횃불/조각상 등)**으로 재정의 |
