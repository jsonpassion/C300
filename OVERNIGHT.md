# 밤샘 병렬 단어 생성 런북

> 이 리포의 단어 콘텐츠를 **서로 겹치지 않게, 병렬로** 만드는 절차. 핵심은 순서다:
> **① 밴드별 후보 목록 → ② 스크립트로 전역 중복 제거·100개씩 배정 → ③ 권마다 정해진 100단어만 채워 쓰기 → ④ 검증.**
> 작성자(에이전트)는 ③에서 단어를 **고르지 않는다**. 이미 배정된 100개만 채우므로 병렬로 수십 개를 동시에 돌려도 중복이 생기지 않는다.
>
> **이 트랙은 두 언어판이다 — 베트남어판(`vi`, 기본·주력)과 한국어판(`ko`).** 표제어 계획(①②)은 하나를 공유하고,
> ③④만 언어별로 돈다. 두 판의 같은 권은 표제어·순서·예문이 같아야 한다(카드 ID·학습 기록이 언어 전환에도 유지됨 — 검증기가 강제).
> 파일: `content/vi/voca/…` → `manifest.vi.json`, `content/ko/voca/…` → `manifest.ko.json`. 언어 선택은 모든 도구에 `--lang vi|ko`.

규격 원본: `content.config.json`(밴드·권 수·언어판) · `plan/curriculum.json`(권별 테마) · `prompts/wordlist.md` · `prompts/unit.vi.md` · `prompts/unit.ko.md`.

> **한 번에 돌리려면 [CONTENT_PROMPT.md](CONTENT_PROMPT.md)의 붙여넣기 프롬프트를 쓴다** — 이 앱의 구조(레벨 방식·권 수·카드 앞뒷면·언어)와 품질 기준이 들어 있다.

## 0. 준비

```bash
python3 tools/plan.py clear-dummy      # 앱 확인용 더미 권 삭제 (두 언어판 모두)
python3 tools/plan.py wordlist-briefs  # 1단계 프롬프트 생성 → plan/briefs/wordlist-<band>.md
python3 tools/plan.py status
```

## 1단계 — 밴드별 후보 목록 (밴드 수만큼 병렬)

각 에이전트에게: *"`plan/briefs/wordlist-<band>.md`를 읽고 그대로 수행해 `plan/wordlists/<band>.md`를 써라."*
권당 후보를 120개씩 받아 두는 이유는 ②에서 다른 밴드와 겹치는 단어가 빠지기 때문이다.

## 2단계 — 전역 중복 제거와 배정 (스크립트, 1회)

```bash
python3 tools/plan.py merge
```

- 낮은 밴드·앞 권이 먼저 가져간다. 이미 본문이 쓰인 권은 **동결**되어 다시 배정되지 않는다.
- `SHORT`가 나오면 그 권의 후보를 `plan/wordlists/<band>.md`의 해당 `## unit-NNN` 아래에 더 적고 merge를 다시 돌린다.

```bash
python3 tools/plan.py briefs --lang vi  # 베트남어판 프롬프트 → plan/briefs/vi/<band>/unit-NNN.md
python3 tools/plan.py briefs --lang ko  # 한국어판 프롬프트 → plan/briefs/ko/<band>/unit-NNN.md
```

## 3단계 — 권 작성 (권 수 × 언어 수만큼 병렬)

**베트남어판을 먼저** 끝낸다(주력 시장). 한국어판은 같은 권의 베트남어판 예문을 그대로 재사용하므로 뒤에 돌리면 빠르고 일관된다.

각 에이전트에게: *"`plan/briefs/<lang>/<band>/unit-NNN.md`를 읽고 그대로 수행해 지정된 경로에 파일 하나를 써라. 다른 파일은 건드리지 마라. 다 쓰면 `python3 tools/validate_content.py --lang <lang> --unit <band>/NNN`이 0 errors인지 확인하고, 오류 줄만 고쳐라."*

## 4단계 — 검증·재시도·배포

```bash
for L in vi ko; do
  python3 tools/validate_content.py --lang $L   # 0 errors 필수 (중복·100개·필드·표기·배정 일치·두 판 표제어 일치)
  python3 tools/plan.py todo --lang $L          # 빠졌거나 실패한 권의 brief 목록 → 3단계로 다시
  python3 tools/build_manifest.py --lang $L
done
git add content plan manifest.*.json && git commit -m "Content: <band> units" && git push
```

`main` push가 곧 배포다. 앱은 manifest 버전이 바뀌면 받아 간다.

## Claude Code에 그대로 붙여넣는 오케스트레이션 프롬프트

```
ultracode. 이 리포(현재 디렉토리)의 단어 콘텐츠를 OVERNIGHT.md 절차대로 끝까지 만든다.
1) python3 tools/plan.py clear-dummy && python3 tools/plan.py wordlist-briefs
2) 밴드마다 에이전트 1개씩 병렬: plan/briefs/wordlist-<band>.md 를 읽고 plan/wordlists/<band>.md 작성.
3) python3 tools/plan.py merge — SHORT 가 있으면 해당 권 후보만 보충하는 에이전트를 돌리고 merge 반복.
4) python3 tools/plan.py briefs --lang vi && python3 tools/plan.py briefs --lang ko
5) 먼저 vi: plan/briefs/vi/<band>/unit-NNN.md 하나당 에이전트 1개, 동시 최대 12개로 병렬 작성. 각 에이전트는 자기 파일 하나만 쓰고
   python3 tools/validate_content.py --lang vi --unit <band>/NNN 이 0 errors 가 될 때까지 자기 파일만 고친다.
6) python3 tools/validate_content.py --lang vi 전체가 0 errors 가 될 때까지: python3 tools/plan.py todo --lang vi 의 목록만 5)를 반복.
7) python3 tools/build_manifest.py --lang vi 후 커밋·push (베트남어판 먼저 배포).
8) 같은 절차를 --lang ko 로 반복 (brief 는 plan/briefs/ko/, 같은 권의 vi 파일 예문을 재사용하라고 에이전트에게 알려라).
중간에 멈추면 같은 프롬프트로 다시 시작해도 된다 — 쓰인 권은 동결되고 todo 만 이어서 한다.
```
