# HIGGSFIELD_ANIMATION_PROMPTS

## 먼저: "Animation State"가 원래 요청과 다르게 정의되어야 하는 이유

이 프로젝트에는 Animation Controller/Blueprint가 없고, 스켈레탈 애니메이션 자체가 없다(`00_FOUNDATION.md` 참고).
코드에서 실제로 확인되는 몬스터 상태머신은 단 3가지뿐이다 (`m.state`, 검색 확인됨):

- `idle` — 대기
- `chase` — 추적/교전 (이동 + 사거리 내 공격 쿨다운 처리가 이 상태 안에서 함께 일어남)
- `return` — 귀환

그 외의 "동작"은 상태가 아니라 **순간적인 이벤트**로 구현되어 있다:
- 공격/피격 시: `mesh.scale.set(1.15,0.85,1.15)` 스쿼시 펄스 (120ms 후 원복)
- 사망 시: 즉시 `removeMonster()` — 사망 모션 자체가 없음
- 보스 페이즈 전환 시: `flashSkillEffect()` 붉은 플래시 + 이름표 텍스트 변경

따라서 "Idle/Walk/Attack01/HeavyAttack/Stun/Spawn/Victory" 같은 표준 Animation Set을 그대로 요구하는 것은
**존재하지 않는 시스템을 있다고 가정**하는 것이 된다. 대신 아래처럼 **현재 코드가 실제로 지원하는 이벤트에 맞춘,
정지 프레임 여러 장 교체 방식**으로 재정의한다.

## MVP (Phase 1): 상태당 1장만

`HIGGSFIELD_CHARACTER_PROMPTS.md` / `HIGGSFIELD_MONSTER_PROMPTS.md`에서 만든 **기본 포즈 1장**을 모든 상태(대기·이동·공격)에
공용으로 쓰고, 기존 JS 스쿼시 펄스 효과는 그대로 유지한다. 즉 Phase 1은 "정지 스프라이트 + 기존 트윈 이펙트"만으로 완성되며,
추가 이미지 생성이 필요 없다. **이게 비용 대비 효과가 가장 큰 1차 목표다.**

## Phase 2 (선택): 상태별 프레임 추가

여유가 있을 때, 캐릭터/핵심 몬스터에 한해 아래 4프레임을 추가 생성한다. 매번 **동일 시드/동일 캐릭터 참조 이미지**를 넣어
생성해야 프레임 간 일관성이 유지된다(참조 이미지 기능 지원 여부는 실제 Higgsfield UI에서 확인 필요).

| 프레임 | 실제 코드 이벤트 | 프롬프트 지시 |
|---|---|---|
| `idle` | `m.state === 'idle'` | 중립적인 대기 자세, 무기를 편안히 든 상태 |
| `attack` | `chase` 상태에서 공격 쿨다운 트리거 순간 | 무기를 휘두르거나 찌르는 동작의 정점(apex) 포즈, 모션 블러 없이 또렷하게 |
| `hit` | 데미지를 받는 순간 (`spawnDamageNumberAt` 호출 시점) | 살짝 뒤로 젖혀지거나 움츠러든 포즈 |
| `death` | `killMonster()`/`onPlayerDeath()` | 쓰러지는 중간 포즈(완전히 눕기 직전) — 너무 그로테스크하지 않게 |

**생성 방법 후보 (검증 필요, "MANUAL WORKFLOW" 참고)**:
1. 이미지 모델로 4장을 개별 프롬프트로 생성 (동일 캐릭터 유지가 관건 — 실패 시 재시도 필요)
2. 또는 Higgsfield의 영상 모델(Seedance 등)로 짧은 동작 영상을 만든 뒤 프레임을 추출 — 이 경로는 영상 생성 비용이 들고 프레임 추출이라는 추가 작업이 필요하므로 Phase 2에서도 후순위.

## 엔진에 연결하기 위해 필요한 작업 (Phase 2 채택 시)

1. `MONSTER_DEFS`/`CLASS_DEFS`에 `spriteFrames:{idle, attack, hit, death}` 필드 추가 (없으면 `idle` 프레임을 전체 상태에 재사용하도록 폴백).
2. 몬스터/플레이어 업데이트 루프에서 상태 전환 시점(`m.state` 변경, 피격 시점, 사망 시점)에 텍스처를 교체하는 헬퍼 함수 1개 추가 (`setSpriteFrame(m, frameKey)`).
3. 프레임 전환에 최소한의 지속시간(예: hit 프레임 150ms 유지 후 idle로 복귀)을 주는 타이머 — 기존 스쿼시 펄스의 `setTimeout(...,120)` 패턴을 그대로 재사용 가능.

## 등장 연출 (Spawn/Victory 대체)

- **보스 등장 연출**: 현재 `showWaveBanner('보스 출현!')` 텍스트만 있음. Higgsfield 영상 모델로 3~5초짜리 보스 등장 컷씬을 만들어 이 배너 타이밍에 재생하면 효과가 크다 (`HIGGSFIELD_PIPELINE.md`의 "게임 내 애셋이 아닌 항목" 참고 — 이건 정지 스프라이트가 아니라 짧은 영상이므로 별도 트랙).
- **던전 입장 연출**: 이미 `fadeTransition()` 페이드가 구현되어 있음 — 배경 이미지 삽입은 우선순위 낮음.
