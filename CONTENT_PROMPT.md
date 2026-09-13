# Từ Vựng HSK / 삼백 (HSK) — 콘텐츠 생성 프롬프트

> **Từ Vựng HSK(HSK VOCA)** 앱의 단어 콘텐츠 전체를 이 리포(`~/Documents/Developer/c300-content`)에 만들어 올리는 붙여넣기용 프롬프트.
> 규격 원본은 `content.config.json` · `plan/curriculum.json` · `prompts/` · `tools/`이며, 절차 상세는 [OVERNIGHT.md](OVERNIGHT.md).
> 이 문서는 **앱 구조가 바뀐 뒤(2026-09-14) 기준**으로 콘텐츠가 앱에 맞게 들어가도록 필요한 사실을 한곳에 모았다.

## 사용법

1. 터미널에서 `cd ~/Documents/Developer/c300-content && claude` 로 이 리포를 연다.
2. 아래 **붙여넣기 프롬프트** 블록 전체를 그대로 붙여넣는다. 첫 단어 `ultracode`가 멀티 에이전트 병렬 실행을 켠다.
3. 중간에 끊겨도 같은 프롬프트를 다시 붙여넣으면 된다. 이미 쓰인 권은 동결되고 `todo`에 남은 권만 이어서 쓴다.

## 앱이 이 콘텐츠를 읽는 방식

| 항목 | 내용 |
|---|---|
| 앱 | **Từ Vựng HSK**(베트남어, 기본) / 삼백(한국어) / HSK VOCA(영문) — 타깃 `C300`. **주 시장 베트남** |
| 언어 | 앱이 기기 언어로 UI·뜻 언어를 고름(베트남 → vi, 한국 → ko, 그 외 → vi), 설정에서 전환. **콘텐츠도 두 언어판**: 베트남어판 `vi`(주력), 한국어판 `ko` |
| 레벨 방식 | **급수 모드**. HSK 1–6 선택, 대시보드 `Đỗ X/200`(1–2급, 합격 120) / `Đỗ X/300`(3–6급, 합격 180) |
| 권 구성 | HSK 3.0 어휘 요강 신규어 기준 **1급 3 · 2급 2 · 3급 5 · 4급 10 · 5급 16 · 6급 18 = 54권 5,400단어**, 언어판마다 같은 54권 |
| 무료 | 급수마다 1권(10챕터) |
| 줄 형식 | vi: `- 简体 \| nghĩa tiếng Việt \| pīnyīn \| MẸO \| 中文例句 \| bản dịch` · ko: `- 简体 \| 한국어 뜻 \| pīnyīn \| TIP \| 中文例句 \| 예문 번역` |
| 카드 | **앞면 = 간체만**(병음 숨김), 뒷면 = 간체 + 병음 + 뜻·TIP·예문·번역. TTS = 중국어 단어/예문 + 뜻 언어 |
| 두 판의 관계 | **같은 권은 표제어·순서·예문이 완전히 같다** — 카드 ID가 같아 언어를 바꿔도 학습 기록이 이어진다. 검증기가 강제 |
| 파일·배포 | `content/vi/voca/…` → `manifest.vi.json`, `content/ko/voca/…` → `manifest.ko.json` (앱이 언어별 URL을 읽음). 모든 도구에 `--lang vi\|ko` |

## 붙여넣기 프롬프트

```
ultracode. 이 리포(현재 디렉토리)는 Từ Vựng HSK(HSK VOCA) 앱의 단어 콘텐츠 리포다. main 에 push 하면 앱에 배포된다.
아래 사실과 품질 기준을 지키며 OVERNIGHT.md 절차로 전체 단어책을 끝까지 만들고 배포하라.

[앱 구조]
- 앱 : Từ Vựng HSK(베트남어, 기본) / 삼백(한국어) / HSK VOCA(영문) — 타깃 C300. 주 시장 베트남
- 언어 : 앱이 기기 언어로 UI·뜻 언어를 고름(베트남 → vi, 한국 → ko, 그 외 → vi), 설정에서 전환. 콘텐츠도 두 언어판: 베트남어판 vi(주력), 한국어판 ko
- 레벨 방식 : 급수 모드. HSK 1–6 선택, 대시보드 Đỗ X/200(1–2급, 합격 120) / Đỗ X/300(3–6급, 합격 180)
- 권 구성 : HSK 3.0 어휘 요강 신규어 기준 1급 3 · 2급 2 · 3급 5 · 4급 10 · 5급 16 · 6급 18 = 54권 5,400단어, 언어판마다 같은 54권
- 무료 : 급수마다 1권(10챕터)
- 줄 형식 : vi: - 简体 | nghĩa tiếng Việt | pīnyīn | MẸO | 中文例句 | bản dịch · ko: - 简体 | 한국어 뜻 | pīnyīn | TIP | 中文例句 | 예문 번역
- 카드 : 앞면 = 간체만(병음 숨김), 뒷면 = 간체 + 병음 + 뜻·TIP·예문·번역. TTS = 중국어 단어/예문 + 뜻 언어
- 두 판의 관계 : 같은 권은 표제어·순서·예문이 완전히 같다 — 카드 ID가 같아 언어를 바꿔도 학습 기록이 이어진다. 검증기가 강제
- 파일·배포 : content/vi/voca/… → manifest.vi.json, content/ko/voca/… → manifest.ko.json (앱이 언어별 URL을 읽음). 모든 도구에 --lang vi|ko

[품질 기준]
- 기준: HSK 3.0 어휘 요강(누적 1급 300 · 2급 500 · 3급 1,000 · 4급 2,000 · 5급 3,600 · 6급 5,400). 각 급수에 **새로 추가되는** 단어만. 2026년 정기 시험은 아직 2.0이므로 2.0 해당 급수 빈출어 누락도 확인.
- 간체자, 병음은 성조 부호(경성은 부호 없음), 다음자는 그 단어의 실제 발음. 성어는 의미 단위로 띄어 쓴다.
- vi판 뜻: 소문자로 시작, 최대 2개, 성조 부호 온전한 베트남어. MẸO(60자 이내): **Hán-Việt 연결이 핵심 무기**(经济 = kinh tế), Hán-Việt인데 뜻이 다른 함정(环境 = hoàn cảnh → môi trường), 성조 함정(买/卖), 호응(因为…所以…), 문어/구어 구분. Hán-Việt 독음은 확실할 때만.
- ko판 TIP(45자 이내): 한국 한자어 연결(環境=환경), 뜻이 다른 한자어 경고(需要≠수요), 성조 함정, 호응.
- 예문 8–30자, 그 급수 문법 수준, 표제어가 그대로 들어간다. **ko판은 같은 권 vi판의 예문을 그대로 쓰고** 번역만 한국어로.

[절차]
1) python3 tools/plan.py clear-dummy && python3 tools/plan.py wordlist-briefs   (더미는 두 언어판 모두 삭제됨)
2) 급수마다 에이전트 1개씩 병렬: plan/briefs/wordlist-<band>.md 를 읽고 plan/wordlists/<band>.md 작성 (표제어 계획은 두 판 공유).
3) python3 tools/plan.py merge — SHORT 가 나오면 그 권 후보만 보충하는 에이전트를 돌리고 merge 반복해 0 short.
4) python3 tools/plan.py briefs --lang vi && python3 tools/plan.py briefs --lang ko
5) [베트남어판 먼저] plan/briefs/vi/<band>/unit-NNN.md 하나당 에이전트 1개, 동시 최대 12개. 각자 자기 파일 하나만 쓰고
   python3 tools/validate_content.py --lang vi --unit <band>/NNN 이 0 errors 가 될 때까지 자기 파일만 고친다.
6) python3 tools/validate_content.py --lang vi 전체 0 errors 까지 python3 tools/plan.py todo --lang vi 목록만 5) 반복.
7) python3 tools/build_manifest.py --lang vi → git add content plan manifest.vi.json → 커밋 → push (베트남어판 먼저 배포).
8) [한국어판] plan/briefs/ko/<band>/unit-NNN.md 로 5)–6)을 --lang ko 로 반복. 각 에이전트에게 "같은 경로의 content/vi/voca 파일에서
   표제어·병음·예문을 그대로 가져오고 뜻·TIP·번역만 한국어로 쓰라"고 알린다.
9) python3 tools/build_manifest.py --lang ko → 커밋 → push.

[금지]
- 표제어 선정과 배정은 반드시 `plan.py merge` 결과만 따른다. 작성 에이전트는 단어를 고르거나 바꾸지 않는다.
- 실제 기출문제·유료 교재 문장을 옮기지 않는다. 예문·TIP은 전부 새로 쓴다.
- `docs/`, `privacy.md`, `terms.md`, `README.md`는 건드리지 않는다(사이트·약관은 별도 관리).
- `tools/*.py`에 버그가 보이면 고치지 말고 멈춰서 보고한다(모든 앱 리포가 같은 도구를 공유함).
- 출시 전이므로 더미 권 삭제는 괜찮다. **출시 후에는 표제어 철자 변경·삭제 금지** — 카드 ID(`<unit id>-<표제어>`)에 학습 기록이 묶여 있다.

[완료 조건]
- python3 tools/validate_content.py --lang vi 와 --lang ko 둘 다 0 errors(두 판 표제어 불일치도 오류), 경고는 권당 3개 이하.
- python3 tools/plan.py todo --lang vi / --lang ko 둘 다 "nothing to do", 각 판 54권 5,400단어, dummy 0개.
- python3 tools/build_manifest.py --lang vi --check / --lang ko --check 둘 다 up to date, push 후 raw manifest.vi.json·manifest.ko.json 버전이 로컬과 같다.
끝나면 급수(밴드)별 권 수·단어 수, 경고 수, 커밋 해시, raw manifest 버전을 보고하라.
```

> 베트남어판만 먼저 출시해도 된다. 한국어판이 비어 있는 동안 한국어 사용자는 설정에서 Tiếng Việt로 바꾸거나, 7)까지만 돌린 뒤 8)–9)를 다음 밤에 돌린다. 단 앱 심사 전에는 두 판 모두 채워 둘 것(한국어 선택 시 빈 화면 방지).

## 완료 확인 (사람이 직접)

```bash
python3 tools/plan.py status --lang vi
for L in vi ko; do python3 tools/validate_content.py --lang $L --quiet | tail -1; python3 tools/build_manifest.py --lang $L --check; done
curl -s https://raw.githubusercontent.com/jsonpassion/C300/main/manifest.vi.json | head -3
```
