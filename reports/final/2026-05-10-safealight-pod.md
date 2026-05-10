# 최종 보고 — SafeAlight Pod
**slug**: `safealight-pod`
**작성일**: 2026-05-10
**작성자**: reporter
**입력**:
- idea: reports/ideas/2026-05-10-batch2.md (#1)
- market: reports/ideas/safealight-pod-market.md (양산 동일도 50%, 시장 니즈 70%)
- plan: reports/plans/safealight-pod.md (16주 / BOM 226만원 / 리스크 9건 / fallback 4종)
- evaluation: 2차 83(축)+6(가산)=**89**, **3순위**
- critique: 답변가능 0 / 답변부족 5 / 답변불가 0 → **REVISE** (B/D는 구조적 약점 근접)

---

## 1. 한 페이지 요약 (예선 제출 초안)

**캐치프레이즈**: **차에 SEA가 없어도, 외산 차량이어도, 1분 만에 자석으로 붙이는 어린이 하차 안전 박스.**

**페인포인트** (페르소나: 카시트 두 아이 부모):
주차 후 카시트 두 아이를 한 명씩 내리는 동안 옆을 지나는 자전거·전동킥보드·차량에 어린이가 무방비. 부모는 한 아이를 안느라 다른 아이를 못 본다. SEA가 없는 구형 차량/외산 차량 부모는 기능 자체가 없다. 한국 도어 사고 과실은 차량 측에 집중되며 마이크로모빌리티(전동킥보드/전기자전거) 급증으로 사각 위험 확대.

**핵심 메커니즘** (입력 → 처리 → 출력):
1. 차량 후방 좌·우 휀더에 N52 자석으로 부착되는 외부 박스 ×2 (3D 프린팅 IP65 케이싱, 8000mAh 배터리, 솔라셀 옵션, 도난 방지 와이어).
2. 각 박스: Acconeer XM125 mmWave 레이더(거리·속도) + 광각 USB IR 카메라(객체 분류) + Jetson Orin Nano 8GB(중앙 처리).
3. YOLOv8n fine-tuning으로 자전거/전동킥보드/보행자 3분류 + TTC 산출.
4. 카시트 압력 매트(FSR 8×4, BLE)가 "어린이 탑승 차량"임을 박스에 알림 → 시동 OFF 후 30분 저전력 액티브 (레이더 듀티 사이클 10%).
5. 출력: 좌·우 도어 핸들 LED 클립 ×4 (RGB + ESP32-C3, 코인셀)이 적·황·녹 차등 점등 + 박스 외부 부저(70dB 이하) + 부모 앱 한 줄 알림("오른쪽 위험: 자전거 4m, 왼쪽 도어 사용 권장").

**차별** (market 인용): "모든 OEM 양산품은 차량 내장(애프터마켓 외부 부착 X), 마이크로모빌리티 분류 명시 X, 카시트 점유 연계 활성화 X" (market.md L24). **외부 부착 + 레이더+카메라 통합 + AI 객체 분류 + BLE 도어 핸들 LED + 카시트 매트 연계 통합 제품은 미발견** (market.md L31).

**4대 테마**: 주=1번(차량 혁신), 보조=4번(완전히 새로운 개념: 외부 액세서리화)
**수상 DNA 매핑**: ② 기존 기술의 영리한 재해석(SEA를 양산 ECU 의존 없이 외부화) / ③ 사회적/포용적 가치(어린이 하차 사고 + 마이크로모빌리티 안전) / ④ 명확한 단일 페인포인트(카시트 두 아이 내리는 동안 사각)

---

## 2. 기술 도해

```
┌─────────────────── 입력 (외부 부착 박스 ×2) ──────────────────┐
│                                                                 │
│  좌측 휀더 박스                  우측 휀더 박스                 │
│  ┌─────────────────┐             ┌─────────────────┐           │
│  │ Acconeer XM125  │             │ Acconeer XM125  │           │
│  │ 60GHz mmWave    │             │ 60GHz mmWave    │           │
│  │ (거리·속도)     │             │ (거리·속도)     │           │
│  │ ELP 광각 USB IR │             │ ELP 광각 USB IR │           │
│  │ 카메라 + 850nm  │             │ 카메라 + 850nm  │           │
│  │ IR LED          │             │ IR LED          │           │
│  └────────┬────────┘             └────────┬────────┘           │
│           │ N52 자석×4 + 도난 와이어       │                    │
└───────────┼────────────────────────────────┼────────────────────┘
            ▼                                ▼
┌─────────────────── 처리 (Jetson Orin Nano 8GB) ──────────────┐
│                                                                │
│  YOLOv8n fine-tuning (자전거/전동킥보드/보행자 3분류)           │
│  TTC 산출 (레이더 거리·속도 + 카메라 bbox 결합, ±0.5초)         │
│  카시트 점유 룰 (BLE FSR 8×4 매트 ×2 → 시동 OFF 30분 액티브)    │
│  저전력 듀티 사이클 10% (레이더 0.1s on / 0.9s off)             │
│  BLE 메시 4노드 (ESP-NOW 우선, end-to-end ≤300ms)               │
│  18650 ×2 + BMS 8000mAh 12V (+ 솔라셀 5W 보조)                  │
│  카메라 영상 온디바이스 추론 / 저장 0 (PIPA/GDPR)               │
│                                                                │
└────────────────────────────┬───────────────────────────────────┘
                             ▼ BLE 메시
┌──────────────── 출력 (≤300ms) ────────────────────────────────┐
│                                                                │
│  도어 핸들 LED 클립 ×4 (RGB + ESP32-C3 + 코인셀 + 진동 모터)    │
│    좌측·우측·전·후 도어 핸들에 BLE 영구 부착                    │
│    적색(위험)/황색(주의)/녹색(안전) 차등                        │
│  박스 외부 LED 스트로보 (WS2812B 30) + 70dB 이하 액티브 부저    │
│  부모 앱 (Flutter 단일 화면) 한 줄 알림                         │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

**부품/기술 라벨**:
- 입력: Acconeer XM125 60GHz mmWave 레이더 ×2 / ELP 1080P USB IR-cut 광각(160°) 카메라 ×2 / 850nm 12V IR LED 어레이 ×2
- 처리: NVIDIA Jetson Orin Nano Super 8GB / SLA 3D 프린팅 ABS-Like + 실리콘 가스켓 IP65 케이싱 / N52 50mm 자석 ×8 + 강철 도난 와이어 / 18650×4 BMS 8000mAh 12V + 솔라셀 6V 5W 보조 / FSR 8×4 어레이 + ESP32 + LiPo 500mAh 카시트 매트 ×2
- 출력: 도어 핸들 LED 클립 ×4 (RGB + 코인셀 + 진동 모터 + ESP32-C3, 자체 PCB) / WS2812B 30 LED 스트립 + 12V 액티브 부저(70dB 이하) / Flutter/React Native 부모 앱

---

## 3. 4개월 일정 (16주 압축)

| 단계 | 주차 | 핵심 산출물 | 통과 기준 (게이트) |
|---|---|---|---|
| **M1 PoC** | W1-4 | 부품 발주(W1) / Jetson 플래싱·CUDA·DeepStream / 한국 도심 주차장 야간/우중 자체 영상 30분+ 수집 / YOLOv8n fine-tuning / SLA 3D 프린팅 케이싱 v1 | **W4 게이트: 책상 위 자전거 모형 시야 진입 시 LED 적색, mAP@0.5 ≥0.70 (자체 50장 검증)** |
| **M2 알고리즘+HW 통합** | W5-8 | TTC 알고리즘(레이더+카메라) / BLE 메시 4노드 (ESP-NOW) / 도어 핸들 LED 클립 ×4 PCB 조립 / 카시트 매트 FSR 어레이 캘리브레이션 / IP65 케이싱 v2 + 자석 100회 탈착 내구 | **W8 게이트: 통합 데모 v1 — 압력 → 박스 액티브 → 자전거 접근 → LED 적색 + 폰 푸시. TTC ±0.5초, BLE end-to-end ≤300ms** |
| **M3 통합 v2** | W9-12 | 박스 ×2 좌·우 동시 운용 / 시동 OFF 30분 저전력 / FP 5% 이하 튜닝 / 도난 와이어 + 솔라셀 부착 / 차량 모형 + 마네킹 + 자전거 모형 시나리오 5종 / 부저 70dB 측정 | **W12 게이트: FP ≤5%, 시동 OFF 30분 유지, 부저 70dB 이하 리포트** |
| **M4 발표** | W13-16 | 실차 1대 + 카시트 마네킹 ×2 + 자전거/킥보드 실물로 야외 시연 영상 5종(주간/야간/우중/그림자/역광) / 사전 녹화 fallback 영상 / 5분 슬라이드 / 리허설 ×3 / 박스 외관 다듬기 | **W16: 5분 발표 + 라이브 시연 1회 + fallback 영상 100% 준비, Q&A 5종 카드** |

(상세는 reports/plans/safealight-pod.md §2 참조)

---

## 4. BOM 요약

| # | 핵심 부품 | 수량 | 소계(KRW) |
|---|---|---|---|
| 1 | NVIDIA Jetson Orin Nano Super 8GB | 1 | 480,000 |
| 2 | Acconeer XM125 (A121 60GHz PCR mmWave) | 2 | 190,000 |
| 3 | ELP 1080P USB IR-cut 광각(160°) 카메라 + 850nm IR LED 어레이 | 2세트 | 134,000 |
| 4 | 박스 케이싱 (SLA 3D ABS-Like + IP65 가스켓) + N52 자석 마운트 ×8 + 도난 와이어 | 2세트 | 124,000 |
| 5 | 18650×4 + BMS 8000mAh 12V 박스 배터리 | 2 | 90,000 |
| 6 | 솔라셀 6V 5W 보조 패널 (옵션) | 2 | 36,000 |
| 7 | BLE 외부 부저(70dB 이하) + WS2812B LED 스트로보 ×2 + 12mm 푸시 | 1세트 | 40,000 |
| 8 | 도어 핸들 LED 클립 ×4 (RGB + ESP32-C3 + 코인셀 + 진동 모터, 자체 PCB) | 4 | 100,000 |
| 9 | 카시트 압력 매트 ×2 (FSR 8×4 + ESP32 + LiPo 500mAh) | 2 | 130,000 |
| 10 | 카시트 시연용 (영유아·토들러 5점식) ×2 + 마네킹 ×2 | 4 | 280,000 |
| 11 | 자전거+전동킥보드 모형, 케이블/방수 커넥터/3M VHB, GoPro/모니터/삼각대 | 1세트 | 449,000 |
| **소계** | | | **2,053,000** |
| **예비비 10%** | | | 205,000 |
| **합계** | | | **약 226만원** |

**사내 한도 대비**: 본선 제작비 600~1,000만원 → BOM **226만원은 한도의 23~38% 사용**, 충분히 여유.

> **단가 주석**: Jetson Orin Nano 한국 가격은 NVIDIA 공식 $249 인하 후 정발 환산 추정. Acconeer XM125는 Mouser/DigiKey 기준 추정. 실제 발주 전 디바이스마트·Mouser 재확인 필요.

---

## 5. 팀 매칭

| 역할 | 담당 | 비중 |
|---|---|---|
| HW (박스 케이싱 SLA 출력 / IP65 가스켓 / 자석 마운트 / 도어 핸들 LED 클립 PCB / 카시트 매트 FSR / 도난 와이어 + 솔라셀 / 실차 부착 지그) | 본인 | 100h / 35% |
| SW (YOLOv8 fine-tuning 자전거·킥보드·보행자 한국 도심 데이터 / TTC / BLE 메시 4노드 / 저전력 듀티 사이클 / 카시트 점유 룰 / Flutter 부모 앱) | 본인 | 130h / 45% |
| 데모 준비 (야외 주차장 시연 영상 5종·편집·fallback·5분 슬라이드·리허설 ×3) | 본인 | 58h / 20% |
| 알림 강도/timing 임상 자문 (어린이 외상·하차 행동 패턴) | 배우자 | **자문만, 코드 0** — 4 후보 중 의료 contribution 가장 낮음 (가산점 미부여 사유) |
| 페르소나 (실차 시연 / 본인 차량 + 부부 차량 + 지인 차량 1대) | 본인 직접 | W11~W13 |
| 외부 자원 (도어 핸들 LED 클립 PCB 외주, Acconeer 백업으로 TI IWR1443 국내 재고) | 외주 | 옵션 |

---

## 6. 자체 평가 4축

| 축 | 점수 | 한 줄 근거 |
|---|---|---|
| ① 실현 가능성 | 21/25 | 16주 게이트 4개 정량 + PoC 6종 + Fallback 4단계 + 이중 BLE 백업 + 부품 백업(Acconeer→TI) + BOM 226만원 한도 내. 잔존: W6~7 BLE 메시 + W10~11 FP 튜닝 타이트, 자체 30분 데이터 일반화 부족, 실차 휀더 도장 손상 미평가 |
| ② 독창성 | 20/25 | 양산 동일도 50%로 중간. 외부화 + 마이크로모빌리티 분류 + 카시트 점유 + 도어 차등 4중 결합은 미발견. 다만 핵심 메커니즘(후방 레이더+카메라)은 OEM SEA 컨셉 중첩, 단일 새 메커니즘 아님 |
| ③ 기술 적합성 | 23/25 | 본인 자율주행 객체 분류·TTC·센서 퓨전 코드 자산 직접 재사용 + mmWave 레이더+비전 융합 = ADAS 회사 자산과 가장 직접 시너지. 양산 3단계(애프터마켓 → SEA Plus → 카셰어링). 잔존: 배우자 임상 contribution 약함("알림 timing 자문 + 코드 0") |
| ④ 고객 지향성 | 19/25 | 이중 운용("주차 시 분리 / 도어 핸들 LED 영구") + 카메라 프라이버시(온디바이스, 영상 저장 0) + 페르소나 페인 직격. 잔존: 부모 자비 80만원 WTP 검증 0, 페인이 법률·과실 중심·일상 구매 트리거 약함, 사용성 테스트 부재 |
| **합계** | **83/100** + 가산점 6 = **89** | DNA ②③④ + HW 4종 + 시장 70% (정확 컷오프). 자율주행+의료·사회적 가치 단독 명시 약해 미부여 |

(critic 보정: 답변부족 5건 모두 §11에서 별도 명시. 상세는 reports/evaluations/safealight-pod.md §2차 평가 참조)

---

## 7. 시장 차별 각도

1. **애프터마켓 외부화** — 양산 OEM SEA는 신차/상위 트림 한정. 외부 부착형이면 구형/외산/저가 차량(SEA 미장착 약 600만대 추정)까지 시장 확장. 한국엔 SEA 미장착 차량이 다수.
2. **마이크로모빌리티 비전 분류** — 양산 OEM SEA는 후방 차량(BSD 레이더 기반)에 최적화. 전동킥보드·자전거를 명시적으로 분류·TTC 산출하는 박스는 마이크로모빌리티 시대 대응으로 차별 명확.
3. **카시트 점유 연계 + 도어별 차등 알림** — 카시트 압력 매트 BLE로 "어린이 탑승 차량"임을 박스에 신호 → 시동 OFF 후 N분 액티브 + 도어 핸들 LED 적·황·녹 차등 표시. OEM SEA는 일반적인 후석 도어 잠금/경고에 머무름.

**인용 출처** (market.md):
- [Hyundai SEA Owner Manual](https://ownersmanual.hyundai.com/full_webhelp/NE1a/2025/en_US/id23BSFA00P5Z.html) / [Hyundai SmartSense](https://www.hyundaiusa.com/us/en/safety) — 동일도 60% (차량 내장 한정)
- [Audi Exit Warning / Dutch Reach Project](https://www.dutchreach.org/solution-way-to-prevent-doorings/) — 동일도 60%
- [Ford Media Cycling Warning](https://media.ford.com/content/fordmedia/feu/en/news/2020/02/27/new-car-tech-could-be-just-what-cyclists-want---a-warning-that-h.html) — 동일도 55%
- [Electrek VW ID.7 Exit Warning](https://electrek.co/2023/12/12/getting-doored-is-a-cyclists-worst-fear-but-next-gen-car-tech-aims-to-curb-it/) — 동일도 55%
- [BikeBiz Honda Exit Warning](https://bikebiz.com/car-makers-to-unveil-tech-that-prevents-dooring-of-cyclists/) — 동일도 50%
- [JVIS Illuminated Door Handles](https://jvis.us/product/illuminated-door-handles/) — 동일도 15% (단순 점등)
- [Primo Dynamic Door Handle LEDs](https://primodynamicleds.com/collections/door-handles) — 동일도 10%
- [Wahoo TRACKR Radar (자전거용 후방 레이더)](https://www.wahoofitness.com/devices/bike-sensors/trackr-radar-buy) — 동일도 20% (사용 시나리오 정반대)
- [Carmanah Vehicle Detection](https://carmanah.com/product-category/vehicle-detection-systems/) — 동일도 5% (인프라형)
- [노랗IT월드 택시 개문사고 과실비율](https://yellowit.co.kr/%EC%9E%90%EC%A0%84%EA%B1%B0-%EB%B0%8F-%EC%98%A4%ED%86%A0%EB%B0%94%EC%9D%B4-%ED%83%9D%EC%8B%9C-%EA%B0%9C%EB%AC%B8%EC%82%AC%EA%B3%A0-%EA%B3%BC%EC%8B%A4%EB%B9%84%EC%9C%A8-10-%EB%B0%9C%EC%83%9D-%ED%95%A0/)
- [나무위키 문콕](https://namu.wiki/w/%EB%AC%B8%EC%BD%95)
- [한국일보 전동킥보드 사고](https://www.hankookilbo.com/News/Read/A2025110711010000113)
- [easylaw 개문사고 법령](https://easylaw.go.kr/CSP/CnpClsMain.laf?popMenu=ov&csmSeq=1506&ccfNo=3&cciNo=2&cnpClsNo=1)

---

## 8. 수상 DNA 매핑

| DNA | 매핑 근거 |
|---|---|
| ② 기존 기술의 영리한 재해석 | OEM SEA(차량 내장)를 외부 부착 + 마이크로모빌리티 분류 + 카시트 연계로 재해석 — 2024 ADSM(시선 사이드미러) 대상의 "양산 ADAS를 외부 액세서리화" DNA와 같은 계열 |
| ③ 사회적/포용적 가치 | 어린이 하차 사고 + 마이크로모빌리티 시대 보행자·라이더 안전. SEA 미장착 600만 대 차량 부모도 동일한 안전 기능을 누리게 함 |
| ④ 명확한 단일 페인포인트 | "카시트 두 아이 한 명씩 내릴 때 다른 아이 사각" — 단일 행동 시나리오. 한국 도어 사고 과실이 차량 측에 집중되는 법률 환경 |

---

## 9. 데모 시나리오 5분

| 시간 | 화면/행동 | 메시지 |
|---|---|---|
| 0:00-0:30 | 페인 영상: 부모가 마트 주차장에서 카시트 두 아이를 한 명씩 내리는 동안 우측에서 전동킥보드가 휙 지나가는 사전 녹화 영상 (Critic E 환류 후: 가족 출연 빼고 개문사고 뉴스 클립 5초 + 시연 25초) | "당신 차에 SEA가 없거나, 외산 차량이거나, 구형이거나. 두 아이의 도어 사각은 누가 봐주나?" |
| 0:30-2:00 | 메커니즘 슬라이드 + 박스 분해 도해 + BLE 메시 4노드 토폴로지 + 시동 OFF 30분 저전력 타임라인 | "차량 ECU 0의존, 자석으로 1분 부착. 카시트 매트가 '아이 탑승 중'이라고 박스에 알리면, 박스는 시동 OFF 후에도 30분 깨어 있다" |
| 2:00-3:30 | **라이브**: 본인 차량 우측 휀더에 박스 자석 부착 → 카시트 마네킹 ×2 좌·우 매트에 안착 → 시동 OFF → 자전거 모형 우측 후방에서 접근 → **우측 도어 핸들 LED 적색 + 외부 부저(70dB 이하) + 폰 앱 "오른쪽 위험: 자전거 4m" 푸시** → 자전거 멀어지면 LED 녹색. 좌측 도어 핸들은 내내 녹색 | "도어 핸들이 색깔로 말해준다. 위험한 도어는 손에 닿기 전에 빨갛다" |
| 3:30-4:00 | Fallback 영상(M4 W13~15 사전 녹화): 동일 시나리오 + 야간 + 우중 + 전동킥보드 4종 컷 (자동 타임아웃 10초 트리거, Critic E 환류) | "주간뿐 아니라 야간·우중에도 동일 — 마이크로모빌리티 시대의 부모 도구" |
| 4:00-5:00 | 차별 + critic 정면 방어 1슬라이드(외부 부착 UX + OEM 카니발리제이션) + 양산 3단계(H-Genuine → SEA Plus → 카셰어링) + 팀 강점(자율주행 인지 스택 + 간호사 임상) + 사회적 가치 | "OEM SEA를 대체하는 게 아니라, SEA가 없는 곳에 먼저 가고, SEA가 있는 곳에 마이크로모빌리티 분류를 더한다" |

**Fallback 4종 영상**(주간/야간/우중/킥보드) + 박스↔폰 직접 BLE 보조 채널 이중 백업 + 자동 fallback 타임아웃 10초 + 발표자 단축키 수동 트리거.

---

## 10. 인용 출처

**Market** (위 §7과 동일):
- [Hyundai SEA Owner Manual](https://ownersmanual.hyundai.com/full_webhelp/NE1a/2025/en_US/id23BSFA00P5Z.html), [Hyundai SmartSense](https://www.hyundaiusa.com/us/en/safety)
- [Dutch Reach Project (Audi Exit Warning)](https://www.dutchreach.org/solution-way-to-prevent-doorings/)
- [Ford Media](https://media.ford.com/content/fordmedia/feu/en/news/2020/02/27/new-car-tech-could-be-just-what-cyclists-want---a-warning-that-h.html)
- [Electrek VW ID.7](https://electrek.co/2023/12/12/getting-doored-is-a-cyclists-worst-fear-but-next-gen-car-tech-aims-to-curb-it/)
- [BikeBiz Honda](https://bikebiz.com/car-makers-to-unveil-tech-that-prevents-dooring-of-cyclists/)
- [JVIS](https://jvis.us/product/illuminated-door-handles/) / [Primo Dynamic](https://primodynamicleds.com/collections/door-handles)
- [Wahoo TRACKR](https://www.wahoofitness.com/devices/bike-sensors/trackr-radar-buy) / [Carmanah](https://carmanah.com/product-category/vehicle-detection-systems/)
- [노랗IT월드 택시 개문사고](https://yellowit.co.kr/%EC%9E%90%EC%A0%84%EA%B1%B0-%EB%B0%8F-%EC%98%A4%ED%86%A0%EB%B0%94%EC%9D%B4-%ED%83%9D%EC%8B%9C-%EA%B0%9C%EB%AC%B8%EC%82%AC%EA%B3%A0-%EA%B3%BC%EC%8B%A4%EB%B9%84%EC%9C%A8-10-%EB%B0%9C%EC%83%9D-%ED%95%A0/)
- [나무위키 문콕](https://namu.wiki/w/%EB%AC%B8%EC%BD%95) / [한국일보 전동킥보드](https://www.hankookilbo.com/News/Read/A2025110711010000113) / [easylaw 개문사고](https://easylaw.go.kr/CSP/CnpClsMain.laf?popMenu=ov&csmSeq=1506&ccfNo=3&cciNo=2&cnpClsNo=1)

**Critic 인용 (Open Questions 근거)**:
- [Hyundai 2026 LX3 SEA Owners Manual](https://ownersmanual.hyundai.com/full_webhelp/LX3/2026/en_US/idb12c9f8b1e6.html)
- [2026 Hyundai SmartSense Tustin](https://www.tustinhyundai.com/blog/hyundai-smartsense-features-2026)
- [2026 Palisade Safety](https://www.hyundai.com/worldwide/en/suv/palisade-2026/safety)
- [Robustness analysis YOLO/Faster R-CNN — Nature Sci Rep 2025](https://www.nature.com/articles/s41598-025-28737-5)
- [Advancing low-light object detection with YOLO — Wiley 2024](https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/ccs2.12114)
- [Performance Evaluation of Real-Time Object Detection for E-Scooters — arXiv 2405.03039](https://arxiv.org/abs/2405.03039)

---

## 11. Open Questions / 본선 진출 시 즉시 보강해야 할 사항

critic 5개 공격 모두 **답변부족 / 답변불가 0** — 단, B(retrofit 시한부) + D(부모 80만원 WTP) + C-(4)(사내 SEA IP 충돌)는 **구조적 약점에 매우 근접**. 사내 예선 통과 후 본선 4개월 동안 가장 무거운 보강 작업이 필요한 후보:

### 11-A. mAP 0.7 게이트 + 캘리브레이션 드리프트 + 한겨울 cold-boot (critic A, 답변부족)
- **남은 약점**: ① mAP@0.5 0.7은 "10대 중 3대 놓침" — 자율주행 도어 사고 회피용으로 너무 관대. YOLOv8s가 저조도에서 mAP 0.55 수준. 자체 30분 + BDD100K로는 **자전거/킥보드 마이너 클래스 long-tail**(뒷모습·안장 가림·우산 라이더) 절대 부족. plan §5 R4 fallback("자전거+그 외 2분류")이 발동되는 순간 **차별축 핵심(마이크로모빌리티 분류) 자체 폐기**. ② 외부 박스 진동·문닫힘 충격으로 레이더-카메라 캘리브레이션 드리프트, 온라인 보정 알고리즘 부재. ③ -10°C 18650 가용량 60~70% 감소 + Jetson cold-boot 7~10초 → 듀티 10% 매번 재부팅 시 wake 시간 부족.
- **즉시 보강** (M0~M2, **6주 이상 필요 — 1주 안에 불가**): 한국 도심 자체 데이터 **30분 → 5시간** 확장 + 라이더 자세 라벨 가이드(앞·뒤·측·우산·짐·전조등 on/off) + **합성 데이터 domain randomization** 도입. **온라인 캘리브레이션 알고리즘**(정적 anchor 기반 자동 보정)을 M2 또는 M3에 1~2주 추가. 한겨울 cold-boot 측정 PoC 데이터 #3 항목에 추가, always-on Nano 모드 기본 + 듀티 10%는 카메라 wake에만.

### 11-B. SEA 신차 표준화 → retrofit 시장 시한부 (critic B, 답변부족, 구조적 약점 근접)
- **남은 약점**: ① 2026 Kona/Palisade SmartSense에 SEA 표준 장착 확대. retrofit 600만대 풀은 매년 8~15% 자연 감소, 5~7년 뒤 폐차 대상. ② OEM이 SW 업데이트 1회로 e-scooter 클래스 추가하면 끝나는 일을 외부 박스 + Jetson + 226만원 BOM으로 푸는 것이 사내 본부장 시각에서 합리적인가. ③ "SEA Plus" 카니발리제이션 회피 논리가 약함 — 사내 SEA 팀이 "왜 우리 모듈에 e-scooter 학습 한 줄 안 넣고 외부 박스를 새로 만들죠?" 직격 가능.
- **즉시 보강** (M0~M1, **idea_generator 환류 필요**): 2단계 SEA Plus 포지셔닝을 1단계로 끌어올리는 재설계 검토. **외부 박스 형태를 줄이고 카시트 매트 + 도어 핸들 LED 클립 + 폰 앱 중심**의 더 가볍고 부모 직접 가치 명확한 액세서리로 축소 가능성 검토. 차별 한 줄을 "외부화"가 아닌 **"카시트-점유 인지 도어 LED 차등 + 마이크로모빌리티 클래스 학습"**으로 재진술. ARR 시뮬레이션 추가(80만원 × 600만대 × 1% 침투 = 480억 골든 윈도우 5~7년).

### 11-C. H-Genuine 결재 경로 + 차종 호환 + 도장 클레임 + 사내 SEA IP (critic C, 답변부족)
- **남은 약점**: ① H-Genuine Accessory 등록 18~24개월 + 6단계 결재(상품기획·부품개발·품질·영업·법무·출시) — plan "1단계 W17~6개월"은 현실의 1/4. ② 알루미늄 휀더(EV 모델 다수, 아이오닉 5/6, EV6, GV60), CFRP, 폴리프로필렌 펜더 라이너에서는 **자석 안 붙음**. ③ 클리어코트 헤이즈/광택 손실 측정 0 — N52 자석 100회 탈착 시 도장 손상 거의 확실, BMW 7시리즈 도장 수리 200만원 PL 클레임 1건이면 양산 ROI 붕괴. ④ 사내 SEA 특허 회피·라이선스 협의 사전 시뮬레이션 부재 — W12 인터뷰 액션은 답이 아님.
- **즉시 보강** (M0~M1, **2~3주 필요**): plan §8 1단계 일정을 18~24개월 현실로 명시 + "MOU → PoC 인증 → 시판" 재구간화. 차종 호환 매트릭스(SEA 미장착 상위 10종 × 휀더 재질) 1주 내 작성 + **3M VHB 영구 부착 + 트렁크 스페어 모듈 휴대 2-mode 정책**. **자석↔도장 사이 0.5mm 실리콘 패드 + 마이크로파이버 라이너** BOM 추가, 클리어코트 헤이즈 측정(60° 광택계 ΔGU + 육안 등급) PoC 데이터 #8 신설. **사내 SEA 팀 비공식 자문을 W4까지** 앞당김(W12는 너무 늦음).

### 11-D. 부모 자비 80만원 구매 트리거 실증 부재 (critic D, 답변부족, 구조적 약점 근접)
- **남은 약점**: ① 어린이 도어 존 사고는 통계상 빈도 낮음(전 사고 0.X% 미만) — 부모 시각 "평생 한 번 일어날까 말까". ② 80만원은 카시트 1대(30~50만원)·베이비 모니터(20만원)·1회성 안전 교육(5만원) 비교에서 약함, plan §7 PoC 데이터 6종에 **WTP 0**. ③ 페르소나(저자=자율주행 엔지니어 + 간호사 배우자)는 소득·기술 친숙도·안전 가치관 상위 5%, 일반 부모와 동일시 불가. ④ market.md 인용 4건 모두 "법률·과실·문콕"에 집중, **"내 아이가 도어 사이로 사고당함" 부모 직접 페인 인용 0**.
- **즉시 보강** (M0~M1, 1~3주): plan §7 PoC 데이터 #7로 **부모 인터뷰 N=10** (맘카페·당근맘 모집): 도어 존 사고 인지·80만원 WTP 5점 척도·대체재 비교 (카시트 추가 1대 vs SafeAlight Pod). **모듈 분할 판매**로 가격 anchoring 재설계 — 풀세트 80만원 → 박스만 40만 / 매트 추가 +20 / LED 추가 +20. **must-have 페인 보강** — 한국 맘카페 "주차장에서 아이 사고 날 뻔" 1인칭 후기 5건 이상 인용 (market.md 환류).

### 11-E. "기존 SEA 외장판" 인상 + 5분 임팩트 + fallback 신뢰성 (critic E, 답변부족)
- **남은 약점**: ① 첫 90초 "차에 박스 자석 부착 + 자전거 모형 + LED 적색"이 ADAS 출신 심사위원에게 "현대 SEA의 외장판" 즉시 활성화. ② 5분 안에 7개 객체 + BLE 메시 + 도어 차등 + 카시트 매트 + 야외 환경 — 인지 부담 과다. ③ 가족 출연 도박. ④ fallback 트리거 정의 모호("BLE 페어링 미감지"). ⑤ BLE 메시 4노드 토폴로지 그림은 엔지니어용, R&D본부장 5초 흡수 형식 아님.
- **즉시 보강** (M0~M1, 1~2주): 5분 시연을 **"전동킥보드 시나리오부터 시작"**으로 재구성 (SEA가 못 잡는 케이스 강조). 차별 한 줄 자막을 시연 전 30초에 미리 박음. 시연 객체 축소(박스 ×1 + 매트 ×1, 도어 핸들 LED는 무대 모형 도어 1개로 분리). 가족 출연 빼고 **개문사고 뉴스 클립 5초 + 본인 시연 25초**. **자동 fallback 타임아웃 10초** 설계 + 발표자 단축키 이중화. **단일 인포그래픽 1장**(앞-주차장-부모-아이-자전거-LED 색깔) 추가 제작 (W14 1일).

### 즉시 환류 작업 우선순위 (3 후보 중 가장 무거운 환류 — 본선 진출 후 4개월 중 첫 1개월 전부 투입 필요)
1. **차별 한 줄 재진술 + 외부 박스 비중 축소 검토** (idea_generator) — 가장 구조적
2. **데이터 30분 → 5시간 + 합성 데이터** (plan_designer) — 6주 이상
3. **온라인 캘리브레이션 알고리즘** (plan_designer) — 1~2주
4. **부모 인터뷰 N=10 + 모듈 분할 가격 + must-have 후기 5건** (plan_designer + market_researcher)
5. **차종 호환 매트릭스 + 클리어코트 헤이즈 측정 + 도장 보호 BOM** (plan_designer)
6. **사내 SEA 팀 비공식 자문 W4 이전** (plan_designer) — 답이 부정적이면 양산 경로 자체 막힘
7. **시연 흐름 재설계 (킥보드부터)** + 자동 fallback 10초 + 인포그래픽 1장 (plan_designer)

> **권고**: 본 후보는 자율주행 SW 자산 시너지가 3 후보 중 가장 강하지만, **구조적 약점 (B retrofit 시한부 + C-4 사내 SEA IP + D WTP 부재)** 3건이 누적되어 사내 예선 제출 시 #1 seatfit-coach 또는 #2 pinchsense를 우선하고, 본 후보는 **본선 진출 후 첫 1개월에 위 1·6·4번을 완료한 결과를 보고 추가 진행 여부 재결정**하는 것을 권장.

---

## 산출물 저장
**경로**: `/Users/suyoungchoi/my_workspace/idea_festival_office/reports/final/2026-05-10-safealight-pod.md`
