# 최종 보고 — SeatFit Coach
**slug**: `seatfit-coach`
**작성일**: 2026-05-10
**작성자**: reporter
**입력**:
- idea: reports/ideas/2026-05-10-batch2.md (#3)
- market: reports/ideas/seatfit-coach-market.md (양산 동일도 30%, 시장 니즈 80%)
- plan: reports/plans/seatfit-coach.md (16주 / BOM 228만원 / 리스크 9건 / fallback 3중)
- evaluation: 2차 92(축)+10(가산)=102 → 100 캡, **1순위**
- critique: 답변가능 1 / 답변부족 4 / 답변불가 0 → **REVISE**

---

## 1. 한 페이지 요약 (예선 제출 초안)

**캐치프레이즈**: **부모 손에 들리는 카시트 점검소 — 5초 거치하면 4가지를 동시에 본다.**

**페인포인트** (페르소나: 카시트 두 아이 부모 + 간호사 배우자):
NHTSA 통계상 카시트 46%가 잘못 설치되어 있고, 한국에는 CPST 인증 점검소가 거의 없다. 등받이 각도(영유아 45°)·ISOFIX 잠금·하네스 어깨 슬롯·체스트 클립 위치·pinch test 장력을 매번 다 외우고 점검할 부모는 없으며, 두 아이를 한 명씩 채우는 동안 다른 아이가 떼를 쓰면 "대충 채워서 출발"이 일상이다.

**핵심 메커니즘** (입력 → 처리 → 출력):
1. 200g 휴대형 박스를 카시트 옆에 5초 거치 (시작 버튼 1회).
2. ToF VL53L5CX 4×4 zone(등받이 각도) + 광각 카메라+IR 링(라벨 OCR·ISOFIX 인디케이터·하네스 슬롯) + 2축 로드셀(pinch test) + IMU(박스 자세 보정)가 동시에 측정.
3. Raspberry Pi 5에서 카시트 모델 OCR → 클라우드 DB의 임상 룰베이스(배우자 작성) 매칭 → 4개 항목 진단.
4. 8×8 LED 매트릭스 + 1W 스피커 음성 가이드("등받이 각도 38도, 영유아 기준 45도. 베이스 뒤쪽을 1cm 들어주세요").

**차별** (market 인용): "부모가 들고 다니며 다 모델·정량·자동 진단하는 휴대형 도구는 미발견" (market.md L25). Britax ClickTight·Cybex SensorSafe·Nuna Virtual은 모두 자체 카시트 한정 또는 인적 서비스. **휴대형 + 다 모델 + ToF 정량 + 로드셀 + 라벨 OCR + 임상 룰 + 5초 UX 7중 결합**은 미발견.

**4대 테마**: 주=3번(사업 연관 서비스), 보조=1번(차량 혁신)
**수상 DNA 매핑**: ③ 사회적/포용적 가치(영유아 교통 안전 + NHTSA 46% 잘못 설치) / ④ 명확한 단일 페인포인트(설치 오류 진단) / ⑤ 양산 경로 명시(현대차·기아 신차 동봉 + 카시트 OEM 협업 + 보건복지부 사업)

---

## 2. 기술 도해

```
┌──────────────────── 입력 (5초 거치) ────────────────────┐
│                                                          │
│  ┌─────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │ ToF     │  │ 광각 카메라  │  │ 2축 로드셀   │        │
│  │VL53L5CX │  │+IR 링(940nm) │  │+슬림 프로브  │  IMU   │
│  │ 4×4 zone│  │ +백색 LED링  │  │  HX711 ×2    │ BNO055 │
│  └────┬────┘  └──────┬───────┘  └──────┬───────┘   │    │
│       │ 등받이 각도  │ 라벨/ISOFIX/    │ pinch     │ 박스│
│       │ ±2°          │ 하네스/체스트   │ 장력      │ 자세│
│       │              │ 클립 위치       │ ±0.1N     │ 보정│
│       └──────────────┴──────────────────┴──────────┘    │
│                            │                             │
└────────────────────────────┼─────────────────────────────┘
                             ▼
┌──────────────────── 처리 (Raspberry Pi 5 8GB) ──────────┐
│                                                          │
│  PaddleOCR/TrOCR (모델 OCR) → 카시트 5종 매칭            │
│   ─ 다이치 / 페도라 / 브라이택스 / 맥시코시 / 사이벡스   │
│                                                          │
│  YOLOv8-nano (인디케이터·하네스 슬롯 분류)                │
│  센서 퓨전 (ToF + IMU 각도 / FSR 캘리브레이션)            │
│  임상 룰베이스 매칭 (배우자 작성 JSON, 영유아/토들러)     │
│  ESP32-S3 보조 MCU (LED 1차 응답·부팅 즉시)               │
│  AWS Lambda + DynamoDB (만료일·리콜 fetch, 정적 JSON 폴백)│
│                                                          │
└────────────────────────────┬─────────────────────────────┘
                             ▼
┌──────────────────── 출력 (10초 이내) ────────────────────┐
│                                                          │
│  WS2812B 8×8 LED 매트릭스 — 항목별 OK/경고 (적·황·녹)    │
│  1W 스피커 + PAM8403 — 음성 가이드 (TTS pre-rendered)    │
│  4색 LED 인디케이터 + 시작 버튼                          │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**부품/기술 라벨**:
- 입력: VL53L5CX 4×4 zone ToF / OV5648 130° 광각 USB IR-cut 카메라 / 940nm 12LED + 백색 5LED 듀얼 링 / Loadstar RP-S40-ST 5kg 박형 ×2축 + HX711 ×2 / BNO055 9축 IMU
- 처리: Raspberry Pi 5 8GB + 액티브 쿨러 / ESP32-S3 보조 MCU / PaddleOCR/TrOCR fine-tuning / YOLOv8-nano fine-tuning / AWS Lambda + DynamoDB free tier
- 출력: WS2812B 8×8 LED 매트릭스 + 광확산 패널 / 1W 8Ω 스피커 + PAM8403 / 12mm 푸시 + 4색 LED / 18650 ×2 + BMS + 5V/3A 부스트 (4시간 동작) / TP4056 USB-C 충전

---

## 3. 4개월 일정 (16주 압축)

| 단계 | 주차 | 핵심 산출물 | 통과 기준 (게이트) |
|---|---|---|---|
| **M1 PoC** | W1-4 | 부품 발주 / 5개 센서 단독 동작 / 박스 케이싱 v1 (200g · 5분 배터리) | **W4 게이트: ToF 등받이 각도 ±2°, 5초 내 결과** |
| **M2 알고리즘+HW 통합** | W5-8 | 카시트 5종 라벨 OCR fine-tuning / ISOFIX·하네스 분류기 / pinch 캘리브레이션 / 5센서 통합 + LED 매트릭스 + 음성 가이드 | **W8 게이트: 5초 거치 → 4개 항목 진단 출력 (OCR ≥90%, 분류기 ≥85%, 로드셀 σ≤0.1N)** |
| **M3 시나리오 통합** | W9-12 | 5종 카시트 회귀 / 케이싱 v2 (부팅 5초) / AWS 백엔드 + 정적 JSON 폴백 / 30 시나리오 시연 + SUS 5명 | **W12 게이트: 30개 중 25개 정상 진단 + SUS ≥70** |
| **M4 발표** | W13-16 | 4K 시연 영상 (정상 5 / 오장착 5 / 클로즈업) / 5분 슬라이드 12장 / 리허설 ×3 + Q&A 카드 / 백업 박스 1세트 | **W16: 5분 발표 ±15초 + fallback 영상 100% 준비** |

(상세는 reports/plans/seatfit-coach.md §2 참조)

---

## 4. BOM 요약

| # | 핵심 부품 | 수량 | 단가(KRW) |
|---|---|---|---|
| 1 | Raspberry Pi 5 8GB + 액티브 쿨러 + microSD | 1세트 | 163,000 |
| 2 | ToF VL53L5CX 4×4 zone | 2 | 32,000 |
| 3 | OV5648 광각 USB IR-cut 카메라 | 1 | 28,000 |
| 4 | 940nm IR + 백색 듀얼 LED 링 | 1 | 12,000 |
| 5 | BNO055 9축 IMU | 1 | 35,000 |
| 6 | Loadstar 박형 로드셀 ×2 + HX711 ×2 | 2세트 | 24,000 |
| 7 | WS2812B 8×8 LED + 광확산 패널 | 1 | 15,000 |
| 8 | 1W 스피커 + PAM8403 / ESP32-S3 보조 MCU | 1세트 | 20,000 |
| 9 | 18650 ×2 + BMS + 5V 부스트 + USB-C 충전 | 1세트 | 30,000 |
| 10 | PETG 케이싱 (3회 이터레이션) | 1 | 50,000 |
| 11 | 카시트 시연용 (다이치/페도라/브라이택스/사이벡스/맥시코시) | 5 | 평균 250,000 |
| 12 | 시연 보조 (마네킹·조명·외장 BLE 텐션 게이지·잡자재) | 1세트 | 184,000 |
| **소계** | | | **2,073,000** |
| **예비비 10%** | | | 207,000 |
| **합계** | | | **약 228만원** |

**사내 한도 대비**: 보도 기준 본선 팀 제작비 600~1,000만원 → BOM **228만원은 한도의 23~38% 사용**, 충분히 여유. 카시트 5종(125만원)을 중고/대여로 절감 시 약 110만원까지 절감 가능.

---

## 5. 팀 매칭

| 역할 | 담당 | 비중 |
|---|---|---|
| SW (비전 AI / 센서 퓨전 / 통합 / OCR / 클라우드) | 본인 | 150h / 52% |
| HW (케이싱 3D 프린팅 / 전원 / 센서 마운트 / pinch 프로브 가공) | 본인 | 80h / 28% |
| 데모 준비 (영상·슬라이드·리허설·Q&A) | 본인 | 58h / 20% |
| 임상 룰베이스 작성 (영유아·토들러 단계별 적정 각도/하네스/클립 JSON 5종 + Q&A 카드 + 발표 1분 임상 코멘트) | 배우자 | 80h / 코드 0 |
| SUS 5명 사용성 테스트 (지인 부모 모집) | 페르소나 본인 + 배우자 | W12 1주 |
| 외부 자원 (3D 프린팅 외주 fallback Cubicon3D) | 외주 | +5만원 |

---

## 6. 자체 평가 4축

| 축 | 점수 | 한 줄 근거 |
|---|---|---|
| ① 실현 가능성 | 23/25 | 16주 게이트 4개 모두 정량 통과 기준 + BOM 228만원 한도의 25~40% + Fallback 3중(영상/백업박스/정량 결과표). 잔존: W5~W8 OCR 데이터 수집 타이트 |
| ② 독창성 | 23/25 | 양산 동일도 30% (4 후보 중 최저) + 휴대형 + 다 모델 + 정량 + 라벨 OCR + 임상 룰 + 5초 UX 7중 결합 미발견. 카시트 모델 DB는 추격 가능 |
| ③ 기술 적합성 | 23/25 | 본인 자율주행 자산(다센서 퓨전·OCR·캘리브레이션) ↔ 후보 직접 매핑 + 배우자 임상 룰베이스 직접 작성. 4 후보 중 두 강점 가장 직접 결합. 잔존: ADAS 객체분류·TTC 코어는 안 씀 |
| ④ 고객 지향성 | 23/25 | NHTSA 46% + 한국 CPST 점검소 부재 + 페르소나 직접 보유 + 5초 UX 정량 + SUS 5명 게이트. 잔존: 부모 자비 WTP 직접 인터뷰 부재 |
| **합계** | **92/100** + 가산점 10 → **100 캡** | DNA ③④⑤ + HW 7종 + 시장 80% + 자율주행+의료 + 사회적 가치 모두 매핑 |

(critic 보정: 답변부족 4건은 §11에서 별도 명시. 상세는 reports/evaluations/seatfit-coach.md §2차 평가 참조)

---

## 7. 시장 차별 각도

1. **휴대형 + 다 모델 + 정량 측정** — Britax/Cybex/Nuna 등 모든 OEM 솔루션은 자체 카시트 한정 + 정성 인디케이터. ToF 4×4 zone(±1°) + 로드셀(±0.1N) + 라벨 OCR로 모델 자동 인식하는 부모 휴대형은 미발견.
2. **임상 룰베이스 + 모델 DB 매칭** — 카시트 OEM 단독으로는 다른 OEM 카시트 가이드를 들고 있을 이유가 없음 → 중립적 진단 도구 포지션. 배우자(간호사)의 영유아 흉곽 압박 한계 임상 강점이 직접 결합.
3. **5초 자가 점검 UX** — Nuna Virtual은 Zoom 인적 서비스, CPST 점검소는 한국에 거의 없음. "5초 거치 → 음성 가이드"는 "당장 차에 달린 채로 점검 후 출발"이라는 새 사용 시나리오.

**인용 출처** (market.md):
- [Britax ClickTight](https://us.britax.com/why-britax/innovation/clicktight) — 동일도 35% (자체 카시트 한정)
- [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) — 동일도 40% (앱 가이드 양산, 정량 측정 X)
- [Nuna Virtual Car Seat Check](https://nunababy.com/usa/virtual-car-seat-check) — 동일도 30% (인적 서비스, AI 자동 진단 X)
- [NHTSA Car Seat Safety](https://www.nhtsa.gov/equipment/car-seats-and-booster-seats) — 46% 잘못 설치 통계 출처
- [econmingle 롱패딩 기사](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/) — "한국 엄마들이 가장 많이 하는 실수 ... 대충 채워서 출발" 페인 직격
- [클리앙 카시트 잘 태우기](https://www.clien.net/service/board/lecture/18121244) — 한국 부모 자발 가이드
- [CDOT Korean Car Seat Guide](https://www.codot.gov/safety/carseats/assets/multilingual-child-passenger-safety-guides/child-passenger-safety-guide-korean.pdf) — 한국어 사용자 니즈 정부 인정

---

## 8. 수상 DNA 매핑

| DNA | 매핑 근거 |
|---|---|
| ③ 사회적/포용적 가치 | 영유아 교통 안전 + NHTSA 46% 잘못 설치 통계 + 한국 CPST 부재. 보건복지부·서울시 영유아 안전 지원 사업 양산 경로 (2024 ADSM·H-브리즈 라인) |
| ④ 명확한 단일 페인포인트 | "카시트 설치 점검을 5초로" — 한 줄 페인 + 한 줄 솔루션. 2024 스마트 러기지(에어포켓)·2025 디지로그 락 같은 "단일 행동 코칭" DNA |
| ⑤ 양산 경로 명시 | 1단계 보건복지부/서울시 영유아 안전 사업 → 2단계 H-Genuine Accessory → 3단계 신차 옵션/카시트 OEM IP 라이선스 (3단계 명시는 2024 ADSM 시선 사이드미러 패턴) |

---

## 9. 데모 시나리오 5분

| 시간 | 화면/행동 | 메시지 |
|---|---|---|
| 0:00-0:30 | 페인 영상: NHTSA 46% + 한국 부모 카시트 흔한 실수(롱패딩·하네스 헐거움·등받이 각도) | "한국 부모 절반이 카시트를 잘못 채우고 있다. 점검소는 한국에 거의 없다." |
| 0:30-1:30 | 메커니즘 슬라이드 + 박스 분해도: ToF×2 / 광각 IR 카메라 / 2축 로드셀 / IMU / RPi5+ESP32 | "5초 거치하면 4가지를 동시에 본다 — 각도, 인디케이터, 하네스 위치, 장력" |
| 1:30-2:00 | "왜 폰 아닌가" 1슬라이드: 폰+BLE 게이지 조합 vs 박스 1대 사이드바이사이드 비교 영상 | "폰엔 ToF 4×4 zone과 IR 링이 없다 + 부모는 도구 3개 들 수 없다" (critic #1 정면 방어) |
| 2:00-3:30 | 라이브: 배우자가 영유아 마네킹을 다이치에 일부러 잘못 장착(38°, 슬롯 오류) → 본인 박스 거치 → LED 적색 + 음성 "등받이 각도 38도, 영유아 기준 45도. 베이스 뒤쪽을 1cm 들어주세요" | "박스가 임상 가이드를 음성으로 가이드한다" |
| 3:30-4:00 | 재장착 → 박스 거치 → 녹색 LED + 음성 "정상" + pinch 0.9N 적정 | "5초만에 끝난다" |
| 4:00-5:00 | 차별 + 양산 + 팀 1슬라이드: 휴대형+다모델+정량+임상 / 양산 3단계 / 자율주행 비전 + 간호사 임상 + 두 아이 부모 페르소나 직접 보유 | "현대차·기아 패밀리 SUV 부모를 위한 가장 직접적 안전 도구" |

**Fallback 3중**: ① 30 시나리오 4K 사전 녹화 영상 (W13), ② 백업 박스 예비 1세트 (W14), ③ 정량 결과표 슬라이드 (SUS / 진단 정확도 / pinch σ).

---

## 10. 인용 출처

**Market**:
- [Britax ClickTight](https://us.britax.com/why-britax/innovation/clicktight) / [Britax Alpine Base](https://us.britax.com/shop/car-seat-accessories/britax-alpine-infant-car-seat-base-with-clicktight)
- [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) / [Cybex App](https://apps.apple.com/us/app/cybex/id6474168019)
- [Nuna Virtual Car Seat Check](https://nunababy.com/usa/virtual-car-seat-check)
- [Evenflo SensorSafe](https://www.evenflo.com/pages/sensorsafe)
- [Doona SensAlert](https://www.doona.com/en-us/doonalab/sensalert/shop-sensalert)
- [Bambi Baby Comparison Nuna LUMN vs Britax ClickTight](https://www.bambibaby.com/blogs/learning-center/nuna-lumn-vs-britax-grow-with-you-clicktight-harness-2-booster-car-seat-car-seat-comparison)
- [Filo Tata Pad / Motherly Best Car Seat Sensor App](https://www.mother.ly/parenting/safety/car-seat-safety/best-car-seat-sensor-app/)
- [Elepho eClip](https://elepho.com/products/eclip-baby-reminder-for-your-car)
- [Smart Kid Belt](https://smartkidbelt.co/products/smart-kid-belt-single-pack)
- [Clinometer Play Store](https://play.google.com/store/apps/details?id=com.plaincode.clinometer) / [Car Inclinometer](https://play.google.com/store/apps/details?id=com.darkcube.carinclinometer)
- [Amazon ISOFIX Latch](https://www.amazon.com/isofix-latch/s?k=isofix+latch)
- [NHTSA Car Seats and Boosters](https://www.nhtsa.gov/equipment/car-seats-and-booster-seats)
- [SafeKids Right Fit](https://ucsg.safekids.org/basic-tips/right-fit/)
- [미주 한국일보 카시트 46%](http://ny.koreatimes.com/article/20220921/1433647)
- [클리앙 카시트 잘 태우기](https://www.clien.net/service/board/lecture/18121244)
- [보배드림 카시트 장착 안전 수칙 10계명](https://m.bobaedream.co.kr/board/bbs_view/strange/2827550)
- [베이비뉴스 유아용 카시트 사장님 자리](https://www.ibabynews.com/news/articleView.html?idxno=10408)
- [econmingle 롱패딩 기사](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/)
- [GM Korea Blog 나이별 카시트 장착법](https://blog.gm-korea.co.kr/5801)
- [CDOT Korean Car Seat Guide PDF](https://www.codot.gov/safety/carseats/assets/multilingual-child-passenger-safety-guides/child-passenger-safety-guide-korean.pdf)

**Plan**:
- [디바이스마트 RPi5 (~13만원)](https://www.devicemart.co.kr/goods/view?no=15392299)
- [디바이스마트 HX711](https://www.devicemart.co.kr/goods/view?no=15008022)

**Critic 인용 (Open Questions 근거)**:
- [ST VL53L5CX datasheet](https://www.st.com/resource/en/datasheet/vl53l5cx.pdf) — soft target 캘리브레이션 별도 필요
- [Carseat.org 2025 Britax Grow With You ClickTight Review](https://www.car-seat.org/threads/2025-britax-grow-with-you-clicktight-combination-car-seat-review-the-next-frontier-update-from-our-blog.230886/)
- [Consumer Reports Britax Boulevard ClickTight](https://www.consumerreports.org/babies-kids/car-seats/britax-boulevard-clicktight/m372932/)
- [newstomato 다이치 시장 점유율 47%](https://www.newstomato.com/ReadNews.aspx?no=874566)
- [EBN 다이치 홈플러스 PB](https://www.ebn.co.kr/news/articleView.html?idxno=1445450)

---

## 11. Open Questions / 본선 진출 시 즉시 보강해야 할 사항

critic 5개 공격 중 **답변부족 4건 / 답변불가 0건** — 사내 예선 통과 후 본선 4개월 (M0~M4) 동안 즉시 메워야 할 리스크. 환류 항목 그대로:

### 11-A. 비전·OCR·ToF OOD 일반화 결함 (critic A)
- **남은 약점**: ① OCR ≥90%는 학습 5종 신품 라벨 기준. 라벨 경년열화·조명 변화·천 가림 OOD 매트릭스 부재. ② VL53L5CX 카시트 천(검은색·반사율 5%) 산란 보정 미실시. ③ OCR 신뢰도 임계값 미정의 → false positive 시 토들러용을 영유아용으로 매칭하여 잘못된 임상 가이드 출력 위양성 책임 리스크.
- **즉시 보강** (M0~M2 4h씩): plan §7 데이터 1·2 매트릭스에 "라벨 경년열화 × 조명 × 가림 × 천 색상" 30장+ 추가, ToF 천 보정 회귀 계수표 5종(검은색 카시트 1종 시연 필수), "모델명 음성 confirm" 강제 + 신뢰도 <0.9 자동 매뉴얼 폴백 + 임상 음성 silence UX.

### 11-B. 차별화 부족 — Britax 인디케이터 + Cybex/Evenflo 폰+BLE 조합 (critic B)
- **남은 약점**: ① Britax ClickTight는 자체 카시트 측면 인디케이터 양산 중 → "왜 추가 도구를?" 공격. ② Cybex/Evenflo SensorSafe = 폰 앱 + BLE 클립 = 장력은 클립이 측정·가이드 영상은 폰이 보여줌. ③ 부모 WTP(자비 구매 의향) 데이터 0.
- **즉시 보강** (M0 1주): "5종 카시트 순정 인디케이터 부재 비교 표" 슬라이드 추가, BOM에 Cybex SensorSafe Kit 또는 Evenflo SensorSafe 1세트(약 10만원) 추가 시연용, plan §7 데이터 7로 SUS와 함께 "지인 부모 5명 WTP 30분 인터뷰".

### 11-C. 카시트 모델 DB 100+ 확장 + OEM 채널 협상 (critic C)
- **남은 약점**: ① 한국 시장 활성 모델 50~80종 + 신모델 5~10종/년 cycle. OEM 인센티브 0(다이치는 자체 앱·홈플러스 PB로 사용자 가둠). ② H-Genuine Accessory 등록 24~36개월 + KC 인증 필요 → 본 plan "신차 동봉 2단계"는 비현실적. ③ Maker's Track 박스(RPi5/ESP32/3D 프린팅)는 양산 형태 아님.
- **즉시 보강** (M0~M1 2주): 양산 로드맵 재정렬 — 1단계 보건복지부·서울시 영유아 안전 사업 / 2단계 H-Genuine Accessory / 3단계 신차 옵션 또는 카시트 OEM IP 라이선스. DB 자체 갱신 파이프라인 슬라이드 추가(국토부·KC·CPSC 리콜 DB 크롤링 자동화). 차별 한 줄을 "휴대형 박스" → "**다 모델 카시트 진단 IP 플랫폼**"으로 재포지셔닝.

### 11-D. 페인포인트 — NHTSA 미국 통계 한계 + 한국 정부 정량 자료 부재 + 롱패딩 (critic D, 답변가능)
- **즉시 보강**: 도로교통공단 어린이 카시트 사용 실태 조사 + 한국교통안전공단 충돌 시험 데이터 인용 추가. **롱패딩 자동 감지** 임상 룰 추가 (외투 두께 카메라 추정 ≥3cm 시 "외투를 벗기고 다시 채워주세요" 음성) — 차별 한 줄에 "롱패딩 자동 감지" 명시로 한국 시장 차별 강화.

### 11-E. 5분 발표 임팩트 + fallback 신뢰성 (critic E)
- **남은 약점**: ① "휴대형+다모델+정량+임상" 4수식어 → 한 줄 차별 약함. ② 라이브 시연 4개 부품 동시 의존(OCR + ToF + LED + TTS). ③ 도해 1장 부족.
- **즉시 보강** (M0 1주): 차별 한 줄 재작성 → "**부모 손에 들리는 카시트 점검소**". 라이브 시연 1.5분 → 1분 (ToF+LED만 라이브, OCR+임상 음성은 사전 녹화 영상). "5초 + 4가지" 픽토그램(스톱워치+체크 4개 아이콘) 1장 + 박스 사진 1장으로 분해도 대체 (분해도는 부록).

### 즉시 환류 작업 우선순위 (1~2주 안에 완료 후 critic 재평가)
1. **차별 한 줄 재작성** + 롱패딩 자동 감지 추가 (idea_generator)
2. **plan §6 시연 시간 재배분** + 도해 픽토그램 1장 (plan_designer)
3. **PoC 데이터셋 OOD 강화** + ToF 천 보정 + WTP 인터뷰 5명 (plan_designer)
4. **양산 로드맵 재정렬** + DB 자체 갱신 파이프라인 (plan_designer)
5. **한국 정부 정량 통계 추가 조사** + 다이치 라인업 20종 매핑 (market_researcher)

---

## 산출물 저장
**경로**: `/Users/suyoungchoi/my_workspace/idea_festival_office/reports/final/2026-05-10-seatfit-coach.md`
