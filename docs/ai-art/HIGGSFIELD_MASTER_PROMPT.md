# HIGGSFIELD_MASTER_STYLE_PROMPT

모든 캐릭터/몬스터/아이템/환경 프롬프트 **맨 앞**에 그대로 붙여넣는 공통 스타일 문구.
개별 프롬프트(`HIGGSFIELD_*_PROMPTS.md`)는 이 뒤에 피사체 설명만 이어 붙이면 된다.

권장 모델: Higgsfield 이미지 모델 중 **Nano Banana Pro** 또는 **GPT Image 2**
(텍스트 렌더링이 필요 없고, 일관된 캐릭터/사물 렌더링 품질이 중요하므로 이 두 모델을 우선 시도할 것).

## MASTER STYLE BLOCK (그대로 복사)

```
Game asset for a classic isometric action-RPG (MU Online / Diablo II / dark medieval
fantasy lineage, original IP — do not copy any real game's logos, names, or textures).
Hand-painted fantasy RPG concept-art style, semi-stylized (about 70% stylized /
30% realistic), NOT photorealistic, NOT cartoon, NOT anime, NOT chibi.
Slightly deformed 6.5–7 head-tall proportions for characters (if a character is present).
Bold, clean, easily readable silhouette — this asset will be viewed small on screen from
a fixed isometric camera angle, so prioritize silhouette shape, weapon/armor readability,
and strong color contrast over fine facial or texture detail.
Medium-dark fantasy tone: threatening but not grimdark, no gore, no exposed viscera.
Materials: worn-but-maintained metal armor (matte to semi-gloss, not chrome-mirror),
weathered leather, heavy cloth/velvet, rough-hewn stone, coarse-grained wood, and
glowing magical accents concentrated on specific runes/lines rather than overall sheen.
3/4 isometric view (camera slightly above and to the side), subject centered,
plain neutral or solid-color background suitable for clean background removal / cutout,
soft rim lighting to separate subject from background, single consistent light source
direction across the whole asset set.
This is a single game asset, not a scene or a comic panel — no additional characters,
no watermark, no text, no UI elements, no border frame.
```

## NEGATIVE / AVOID BLOCK (그대로 복사)

```
Avoid: photorealism, anime style, cartoon/chibi proportions, cel-shading outline style,
sci-fi or futuristic elements, firearms, neon cyberpunk colors, exaggerated
oversized body proportions (e.g. tiny legs, huge head), plain white studio
e-commerce product photo look, blurry or low-detail rendering, extra limbs,
extra weapons, text or logos baked into the image, watermark, signature,
multiple subjects, background scenery/environment clutter, motion blur.
```

## 사용 규칙

1. 캐릭터/몬스터/아이템/환경 프롬프트는 전부 `[MASTER STYLE BLOCK] + [피사체 설명] + [NEGATIVE BLOCK]` 순서로 조립한다.
2. 같은 지역(마을/던전) 소속 몬스터를 여러 개 생성할 때는 `GAME_VISUAL_BIBLE.md`의 "지역별 컬러" 팔레트 HEX 값을 프롬프트 문장에 직접 넣어 색 일관성을 강제한다 (모델이 알아서 맞춰주지 않으므로 명시가 중요).
3. 등급별 아이템(무기/방어구)을 만들 때는 반드시 `GAME_VISUAL_BIBLE.md`의 "아이템 등급 컬러" HEX를 프롬프트에 그대로 인용한다 — 이 색은 게임 UI의 등급색과 100% 일치해야 하므로 임의로 바꾸지 않는다.
4. 배경 제거(컷아웃)가 필요한 모든 애셋은 "plain neutral or solid-color background" 조건을 절대 빼지 않는다. Higgsfield 자체에 확실한 배경 제거 기능이 있는지는 검증되지 않았으므로(`HIGGSFIELD_PIPELINE.md` 참고), 후처리 배경 제거를 전제로 프롬프트를 짠다.
