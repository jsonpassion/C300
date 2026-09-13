# HSK VOCA Content — HSK 어휘 콘텐츠 리포

> 앱 이름: **Từ Vựng HSK**(베트남어, 기본) · **삼백**(한국어) · **HSK VOCA**(영문). 주 시장은 베트남.
> 앱은 기기 언어(베트남어/한국어)로 화면과 단어 뜻을 고르고, 설정에서 바꿀 수 있다.
> [NINE90](https://github.com/jsonpassion/NINE90)(TOEIC 트랙)과 동일한 콘텐츠 파이프라인을 쓰는
> HSK 트랙 리포지토리 — 앱은 manifest URL 하나로 이 리포의 콘텐츠를 통째로 동기화합니다.

**현재 상태: 규격·도구·생성 파이프라인만 존재.** 단어는 [OVERNIGHT.md](OVERNIGHT.md) 절차로 밤새 병렬 생성한다.

## 구조

```
content.config.json               ← 트랙 규격: 밴드·권 수·언어·표기 (도구가 모두 이것을 읽는다)
plan/curriculum.json              ← 밴드별 10권 테마
prompts/wordlist.md               ← 1단계: 밴드별 후보 표제어 프롬프트
prompts/unit.vi.md                ← 2단계: 베트남어판 권 파일 쓰기 프롬프트 (Hán-Việt TIP)
prompts/unit.ko.md                ← 2단계: 한국어판 권 파일 쓰기 프롬프트 (한국 한자어 TIP)
tools/plan.py                     ← 후보 병합·전역 중복 제거·100개 배정·brief 생성·todo
tools/validate_content.py         ← 형식·표기·중복·배정 일치 검증 (0 errors 필수)
tools/build_manifest.py           ← manifest.<lang>.json 생성 (모든 도구 --lang vi|ko, 기본 vi)
content/vi/voca/{band}/unit-NNN.md ← 베트남어판 → manifest.vi.json
content/ko/voca/{band}/unit-NNN.md ← 한국어판 → manifest.ko.json (1파일 = 1권 = 100단어, 10단어 = 1챕터)
OVERNIGHT.md                      ← 밤샘 병렬 생성 런북 + 붙여넣기용 오케스트레이션 프롬프트
```

## 급수 (HSK 3.0 어휘 요강 · 합격선은 2.0/3.0 동일)

| band_id | 급수 | 합격선 | 누적 어휘 | 권 수 |
|---|---|---|---|---|
| `hsk-1` | 1급 입문 | 120/200 | 300 | 3 |
| `hsk-2` | 2급 기초 | 120/200 | 500 | 2 |
| `hsk-3` | 3급 초급 | 180/300 | 1,000 | 5 |
| `hsk-4` | 4급 중급 | 180/300 | 2,000 | 10 |
| `hsk-5` | 5급 중상급 | 180/300 | 3,600 | 16 |
| `hsk-6` | 6급 고급 | 180/300 | 5,400 | 18 |

2026년 정기 시험은 아직 HSK 2.0이고 3.0 정식 전환일은 CTI가 따로 공지한다. 7–9급(누적 11,000)은 점수 척도 공개 후 추가.

## 단어 줄 형식 — 앞면은 간체만, 병음은 뒷면

```
- 简体 | nghĩa tiếng Việt | pīnyīn | MẸO | 中文例句 | bản dịch        (content/vi)
- 简体 | 한국어 뜻        | pīnyīn | TIP  | 中文例句 | 예문 번역       (content/ko)
```

두 언어판의 같은 권은 **표제어·순서·예문이 같다** — 카드 ID가 같아서 앱에서 언어를 바꿔도 학습 기록이 이어진다(검증기가 강제).

샘플: [content/vi/voca/hsk-4/unit-001.md](content/vi/voca/hsk-4/unit-001.md) · [content/ko/voca/hsk-4/unit-001.md](content/ko/voca/hsk-4/unit-001.md) — 앱 확인용 더미(급수당 20단어, `dummy: true`).
본 생성 전에 `python3 tools/plan.py clear-dummy`로 지운다.

## 콘텐츠 규칙

- 유닛당 **정확히 100단어**, 10단어 = 1챕터 (앱의 회독 단위)
- **트랙 전체에서 표제어 중복 = 오류** (급수가 달라도 같은 단어는 한 번만). 같은 표기라도 읽기가 다르면 다른 단어
- 필드 안에 파이프(`|`) 금지 (구분자 전용)
- 카드 ID = `{파일 id}-{표제어 slug}` — 줄 순서와 무관하지만, **출시 후 표제어 철자 변경·삭제는 금지**
  (사용자 학습 진도가 카드 ID에 매여 있음). 추가는 새 유닛 파일로.
- `manifest.<lang>.json`의 `profile.free_chapters`(기본 10 = 1권) = 밴드마다 무료로 열리는 챕터 수
  (앱이 원격 설정으로 읽음)

## 워크플로

단어 생성은 [OVERNIGHT.md](OVERNIGHT.md) 한 곳에 정리돼 있다 (후보 목록 → 전역 중복 제거·배정 → 권별 병렬 작성 → 검증).

```bash
python3 tools/plan.py status --lang vi                # 진행 상황
for L in vi ko; do python3 tools/validate_content.py --lang $L && python3 tools/build_manifest.py --lang $L; done
git add content plan manifest.*.json && git commit && git push
```

앱은 raw.githubusercontent.com의 manifest.<lang>.json 버전 변경을 감지해 바뀐 파일만 내려받습니다
(sha256 검증 포함). raw CDN 캐시 특성상 push 후 매니페스트 반영까지 ~5분 걸릴 수 있습니다.

## 상표 고지

HSK(한어수평고시)는 중국국제중문교육기금회(CTI)가 주관하는 시험입니다. 이 리포지토리와 관련 앱은
중국국제중문교육기금회(CTI)와 무관하며, 주관 기관의 제휴·보증·승인을 받지 않았습니다. 모든 콘텐츠는 자체 제작이며
실제 기출문제를 포함하지 않습니다.

© 2026 ForgeLab
