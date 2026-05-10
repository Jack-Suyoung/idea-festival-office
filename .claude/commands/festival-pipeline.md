---
description: 풀 아이디어 페스티벌 파이프라인 1회 실행 — 발산 → 시장 → 평가 → 계획 → 재평가 → 비판 → 보고. 각 단계마다 진행 상태를 사용자에게 보고하고 status.md/log 갱신
---

# /festival-pipeline

현대차·기아 사내 아이디어 페스티벌 출품 후보 파이프라인을 한 번에 돌린다. 각 단계는 자동으로 다음 단계로 이어지며, **사용자는 두 가지 방식으로 실시간 진행 상황을 본다**:

1. **메인 채팅창**: 메인 Claude 가 각 에이전트 호출 직전·직후에 한 줄 보고
2. **별도 터미널**: 사용자가 `tail -f reports/pipeline_status.log` 띄워두면 모든 에이전트의 단계별 진행을 실시간으로 봄

또한 `reports/pipeline_status.md` 대시보드가 단계별·후보별 상태를 markdown 표로 항상 최신 상태로 유지된다.

# 흐름

1. **idea_generator** — 후보 N개 발산 → `reports/ideas/YYYY-MM-DD-batchN.md`
2. **market_researcher** — 모든 후보 시장 조사 → `reports/ideas/{slug}-market.md`
3. **evaluator (1차)** — 시장 결과만으로 컷오프, **DROP 후보는 즉시 제외** (이후 단계 작업량 절감)
4. **plan_designer** — 1차 통과 후보 각각 → `reports/plans/{slug}.md`
5. **evaluator (2차)** — plan 반영해 재평가 → `reports/evaluations/{slug}.md`
6. **critic** — 60점+ 후보 각각 공격 → `reports/evaluations/{slug}-critique.md`
7. **reporter** — critic PASS 후보 → `reports/final/YYYY-MM-DD-{slug}.md`

---

# 메인 Claude 가 해야 할 것

## 0. 사용자 인자 확인 (1회)
이 슬래시 커맨드를 받으면 사용자에게:
- 발산 후보 개수 (기본 7)
- 새로 발산할지 / 기존 batch 파일을 재사용할지

## 1. 파이프라인 초기화 (start)

다음을 순서대로 실행:

```bash
mkdir -p /Users/suyoungchoi/my_workspace/idea_festival_office/reports
cd /Users/suyoungchoi/my_workspace/idea_festival_office
RUN_ID="$(date '+%Y%m%d-%H%M%S')"
echo "" >> reports/pipeline_status.log
echo "===== PIPELINE START $(date '+%Y-%m-%d %H:%M:%S') (run=$RUN_ID) =====" >> reports/pipeline_status.log
echo "[$(date '+%Y-%m-%d %H:%M:%S')] [pipeline] START — full pipeline (요청 N개, 새 발산/기존 batch)" >> reports/pipeline_status.log
```

그리고 `reports/pipeline_status.md` 를 다음 템플릿으로 **덮어쓴다 (Write tool 사용)**:

```markdown
# 파이프라인 상태 대시보드
**run**: {RUN_ID}
**시작**: YYYY-MM-DD HH:MM:SS
**최종 갱신**: YYYY-MM-DD HH:MM:SS
**현재 단계**: 1/7 idea_generator (진행 중)

## 단계 진행
| # | 단계 | 에이전트 | 상태 | 시작 | 종료 | 결과 요약 |
|---|---|---|---|---|---|---|
| 1 | 발산 | idea_generator | 🔄 진행중 | HH:MM | - | - |
| 2 | 시장 검증 | market_researcher | ⏳ 대기 | - | - | - |
| 3 | 평가 1차 | evaluator | ⏳ 대기 | - | - | - |
| 4 | 구현 계획 | plan_designer | ⏳ 대기 | - | - | - |
| 5 | 평가 2차 | evaluator | ⏳ 대기 | - | - | - |
| 6 | 비판 | critic | ⏳ 대기 | - | - | - |
| 7 | 최종 보고 | reporter | ⏳ 대기 | - | - | - |

## 후보별 진행
(idea_generator 종료 후 채워짐)

| slug | market | eval-1 | plan | eval-2 | critic | reporter |
|---|---|---|---|---|---|---|
| - | - | - | - | - | - | - |

## 최근 로그 (마지막 20줄)
(파이프라인 종료 시 채워짐 — 진행 중에는 `tail -f reports/pipeline_status.log` 로 실시간 확인)
```

## 2. 사용자 안내 (1회, 시작 직후)

사용자에게 다음을 한 번에 보여라:

```
🚀 파이프라인 시작 (run={RUN_ID})

📺 실시간 모니터링: 다른 터미널에서
   cd /Users/suyoungchoi/my_workspace/idea_festival_office
   tail -f reports/pipeline_status.log

📋 대시보드 (단계·후보별 상태):
   reports/pipeline_status.md

각 단계 완료 시 여기 채팅창에서도 한 줄 보고드리겠습니다.
```

## 3. 단계 호출 + 상태 갱신 루프

각 단계마다 동일한 패턴:

### Step a: 단계 시작 — log 추가 + status.md 갱신
```bash
echo "[$(date '+%Y-%m-%d %H:%M:%S')] [pipeline] STAGE {N}/7 → {agent_name} 호출" >> reports/pipeline_status.log
```
그리고 `pipeline_status.md` 의 **현재 단계** 줄과 해당 행의 상태를 `🔄 진행중` 으로 Edit.

### Step b: subagent 호출
명시적으로 입력 산출물 경로를 전달. 예:
```
market_researcher 야, reports/ideas/2026-05-10-batch1.md 의 후보 모두에 대해 시장 조사 진행해줘.
```

### Step c: subagent 종료 — 사용자에게 한 줄 보고 + status.md 갱신
subagent 응답에서 핵심 결과를 추출해 메인 채팅창에 한 줄 보고:
```
✅ [2/7] market_researcher 완료 — 처리 5개 (GO=2 REVISE=2 DROP=1) → reports/ideas/{slug}-market.md
```

그리고 `pipeline_status.md` 의 해당 행을 `✅ 완료` + 시작/종료 시각 + 결과 요약으로 Edit. 다음 단계 행을 `🔄 진행중` 으로 Edit. **현재 단계** 줄도 갱신.

### Step d: 조건부 중단
- **모든 후보가 1차 evaluator 에서 DROP** → 사용자에게 "idea_generator 재실행 권고" 후 파이프라인 종료
- **critic 에서 모든 후보가 DROP** → "batch 재발산 권고" 후 종료

## 4. 단계별 호출 가이드

| 단계 | 호출 | 입력 | 핵심 보고 |
|---|---|---|---|
| 1 | idea_generator | 사용자 인자 (N, 새/기존) | "발산 N개 — slug 목록" |
| 2 | market_researcher | batch 파일 경로 | "GO=A REVISE=B DROP=C" |
| 3 | evaluator (1차) | batch + market 결과 (slug별 반복) | "1차 통과 K/N" |
| 4 | plan_designer | 1차 통과 slug 별 (반복) | "plan K개 작성, 4개월 가능성 분포" |
| 5 | evaluator (2차) | idea+market+plan (slug별 반복) | "총점 분포, 80+=K, 60-79=L" |
| 6 | critic | 60점+ slug 별 (반복) | "PASS=A REVISE=B DROP=C" |
| 7 | reporter | PASS slug 별 (반복) | "최종 N건 → reports/final/" |

각 단계의 slug 별 반복 호출은 **순차적으로** (병렬 X — 진행 가시성 우선).

## 5. 파이프라인 종료

마지막 reporter 호출 후:

```bash
echo "[$(date '+%Y-%m-%d %H:%M:%S')] [pipeline] END — 총 N단계, 최종 후보 K건, 소요 X분" >> reports/pipeline_status.log
echo "===== PIPELINE END $(date '+%Y-%m-%d %H:%M:%S') (run=$RUN_ID) =====" >> reports/pipeline_status.log
```

`pipeline_status.md` 갱신:
- 현재 단계 → `완료`
- 모든 행 종료 시각 채움
- **최근 로그 (마지막 20줄)** 섹션을 `tail -n 20 reports/pipeline_status.log` 결과로 채움

사용자에게 최종 보고:

```
🏁 파이프라인 완료 (run={RUN_ID}, 소요 X분)

최종 후보 K건:
1. {slug A} (총점 XX, critic PASS) → reports/final/YYYY-MM-DD-{slug-a}.md
2. {slug B} (총점 XX, critic PASS) → reports/final/YYYY-MM-DD-{slug-b}.md

📋 대시보드: reports/pipeline_status.md
📜 전체 로그: reports/pipeline_status.log

다음 액션 (예선 제출 보강):
- 각 final 파일의 "PoC 우선 작업" 항목 검토
- "사용자 다음 액션" 3개 시작
- 추가 발산이 필요하면 idea_generator 재호출
```

---

# 사용 예

```
/festival-pipeline
→ "후보 몇 개로 발산할까요? (기본 7) 새 발산 / 기존 batch?"
사용자: "새로, 5개"
→ 🚀 시작 안내 + tail 명령 안내
→ [1/7] idea_generator 호출 → ✅ "발산 5개"
→ [2/7] market_researcher 호출 → ✅ "GO=2 REVISE=2 DROP=1"
→ [3/7] evaluator 1차 → ✅ "통과 4/5"
→ [4/7] plan_designer (4개 slug 반복) → ✅ "plan 4개 작성"
→ [5/7] evaluator 2차 (4개 slug 반복) → ✅ "80+=1, 60-79=2"
→ [6/7] critic (3개 slug 반복) → ✅ "PASS=2 REVISE=1"
→ [7/7] reporter (2개 slug 반복) → ✅ "final 2건"
→ 🏁 종료 보고
```

# 부분 실행 (개별 호출)

풀 파이프라인이 부담스러우면 개별 호출:
- `idea_generator 시켜서 새 batch 발산` (7개)
- `market_researcher 로 batch1 시장 조사`
- `evaluator 로 {slug} 평가`
- `plan_designer 로 {slug} 4개월 계획`
- `critic 으로 {slug} 공격`
- `reporter 로 {slug} 최종 보고`

개별 호출 시에도 각 에이전트가 자체적으로 `pipeline_status.log` 에 START/단계/END 를 흘려보내므로 `tail -f` 로 모니터링 가능.
