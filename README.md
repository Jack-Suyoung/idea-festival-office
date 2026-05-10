# idea_festival_office

현대자동차·기아 사내 **아이디어 페스티벌(Making Track)** 출품 준비를 위한 멀티에이전트 사무실. 6명의 Claude Code subagent 가 발산 → 시장 검증 → 구현 계획 → 평가 → 공격 → 보고 파이프라인을 실행한다.

## 사용법

이 디렉토리에서 Claude Code 를 실행한다:

```bash
cd /Users/suyoungchoi/my_workspace/idea_festival_office
claude
```

그러면 `.claude/agents/` 의 subagent 들과 `.claude/commands/` 의 슬래시 커맨드가 자동 로드된다.

### 풀 파이프라인 (예정)

```
/festival-pipeline
```

### 개별 호출 예시

```
"idea_generator 시켜서 새 아이디어 배치 5개 뽑아줘"
"market_researcher 로 #3 아이디어가 이미 출시됐는지 확인해줘"
"plan_designer 로 #1 아이디어 4개월 제작 로드맵 만들어줘"
"evaluator 로 batch1 전체 평가해줘"
"critic 으로 살아남은 아이디어 공격해줘"
"reporter 로 최종 통과 1~3개 정리해줘"
```

## 디렉토리 구조

```
idea_festival_office/
├── .claude/
│   ├── agents/              # 6명의 부서원 정의
│   │   ├── idea_generator.md     ✅
│   │   ├── market_researcher.md  ✅
│   │   ├── plan_designer.md      ✅
│   │   ├── evaluator.md          ✅
│   │   ├── critic.md             ✅
│   │   └── reporter.md           ✅
│   └── commands/
│       └── festival-pipeline.md  ✅
├── reports/
│   ├── ideas/               # 1단계 발산 산출물 + feedback.md
│   ├── plans/               # 2단계 구현 계획
│   ├── evaluations/         # 3단계 평가/공격
│   └── final/               # 최종 통과 아이디어 보고
└── README.md
```

## 부서 구성 (계획)

| # | 에이전트 | 역할 | 모델 | 주요 산출물 |
|---|---|---|---|---|
| 1 | `idea_generator` | 발산. 4대 테마 × 팀 강점 × 페르소나 → 5~10개 후보. 베이스라인(스마트 위치 허브 박스) 항상 포함 | opus | `reports/ideas/YYYY-MM-DD-batchN.md` |
| 2 | `market_researcher` | 인터넷·현대 브랜드샵·네이버·유튜브·구글 검색으로 ① 이미 출시됐는지 ② 경쟁사 유사 제품 ③ 사용자 니즈 공감대 검증 | opus | `reports/ideas/{slug}-market.md` |
| 3 | `plan_designer` | 통과 후보별 **4개월** 실물 제작 로드맵, BOM, HW/SW 분담, PoC 단계, 리스크 | opus | `reports/plans/{slug}.md` |
| 4 | `evaluator` | 정량 평가(공식 4축 + 제작 가능성 + 시장 검증). 컷오프 미만 탈락 | opus | `reports/evaluations/{slug}.md` |
| 5 | `critic` | Devil's advocate. 통과 후보를 공격 → 개선 피드백을 generator/plan_designer 로 환류 | opus | `reports/evaluations/{slug}-critique.md` |
| 6 | `reporter` | 최종 1~3개를 예선 제출 가능 형태로 정리 (요약/캐치프레이즈/도해/일정/팀 매칭) | opus | `reports/final/YYYY-MM-DD-{slug}.md` |

## 평가 루브릭

대회 공식 4축 (각 25점) + 보조 축으로 구성:

```
공식 4축:
  1. 실현 가능성 (PoC 데이터 + 양산 경로 포함)
  2. 독창성 (한 문장 차별화 가능?)
  3. 기술 적합성 (회사 기술 자산과 시너지)
  4. 고객 지향성 (페르소나 + 페인포인트 명확성)

보조 축 (탈락 트리거):
  ✗ 이미 시중/경쟁사 출시품 (market_researcher 검증)
  ✗ 사용자 제작 역량 초과 (사용자 피드백 누적으로 동적 갱신)
  ✗ 4대 테마 미적합
```

## 컨텍스트

- **대회 주최**: 현대차·기아 R&D본부 + AVP본부 (사내 임직원 행사, 매년 개최)
- **사이클**: 서류 선정 약 3개월 → 본선 6팀 → 실물 제작 약 4개월 (총 약 7개월)
- **2024 수상**: ADSM(시선 사이드미러, 대상), H-브리즈(수소차 가습), 스마트 러기지(에어포켓)
- **2025 수상**: ANT(옴니 트랜스포터, 대상), 트레일러 토잉, 디지로그 락
- **베이스라인 아이디어**: 스마트 위치 허브 박스 (인프라 독립형 차량 중심 실내 위치 인지)
- **팀**: 본인(자율주행 SW) + 배우자(간호사) + 페르소나(카시트 두 아이 부모)

## 사용자 피드백 루프

- `reports/ideas/feedback.md` 에 사용자 의견(선호/금기/제작 역량 한계)을 누적 기록
- `idea_generator` 는 매 배치 시작 시 이 파일을 읽어 반영
- 사용자가 산출물 보고 피드백 → feedback.md 업데이트 → 다음 배치는 자동 반영

## 실시간 모니터링

파이프라인이 길게 돌 때(여러 분~수십 분) 진행 상황을 두 가지 방식으로 본다:

### 1. 별도 터미널 — `tail -f` (권장)
다른 터미널을 하나 띄우고:
```bash
cd /Users/suyoungchoi/my_workspace/idea_festival_office
tail -f reports/pipeline_status.log
```
모든 에이전트가 시작·각 단계·종료 시점에 timestamp 와 함께 한 줄씩 흘려보낸다. 단독 호출이든 풀 파이프라인이든 동일하게 흐른다.

로그 예시:
```
[2026-05-10 22:35:01] [pipeline] START — full pipeline (요청 5개)
[2026-05-10 22:35:02] [idea_generator] START — 발산 5개 요청
[2026-05-10 22:35:15] 💡 [idea_generator] 1/5 컨텍스트 로드 (feedback 12줄, 직전 배치 1개, 제외 1건)
[2026-05-10 22:35:30] 💡 [idea_generator] 2/5 발산 — 후보 5개 생성 (테마 분포 2/0/2/1)
[2026-05-10 22:36:10] [idea_generator] END → reports/ideas/2026-05-10-batch2.md (5개 후보)
[2026-05-10 22:36:11] [pipeline] STAGE 2/7 → market_researcher 호출
...
```

### 2. 대시보드 — `reports/pipeline_status.md`
markdown 표로 단계별·후보별 진행 상태가 보인다. 풀 파이프라인 실행 시 메인 Claude 가 각 단계 사이에 자동 갱신한다. 에디터/뷰어로 열어두면 새로고침마다 최신.

대시보드 항목:
- 단계 진행 (7개 단계, 상태/시작/종료/결과)
- 후보별 진행 (slug 별로 어느 단계까지 통과했는지)
- 최근 로그 마지막 20줄 (파이프라인 종료 후)

### 3. 메인 채팅창
풀 파이프라인 실행 중에는 메인 Claude 가 각 단계 완료 시 한 줄 보고를 채팅에 띄운다. 별도 터미널 없이도 큰 흐름은 따라갈 수 있다.

## git 관리

이 office 디렉토리는 git repo 가 아니다. 필요시 `git init` 으로 별도 repo 운영 가능.

## 진행 상태

- [x] 폴더 구조 생성
- [x] `idea_generator` 작성 + 1차 실행 검증 + 피드백 반영 교정
- [x] 나머지 5개 에이전트(`market_researcher` / `plan_designer` / `evaluator` / `critic` / `reporter`) 작성
- [x] `/festival-pipeline` 슬래시 커맨드 작성
- [ ] 각 에이전트 단독 실행 검증 (idea_generator 외)
- [ ] 풀 파이프라인 1회 실행 검증
