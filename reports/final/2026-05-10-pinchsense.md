# 최종 보고 — PinchSense
**slug**: `pinchsense`
**작성일**: 2026-05-10
**작성자**: reporter
**입력**:
- idea: reports/ideas/2026-05-10-batch2.md (#4)
- market: reports/ideas/pinchsense-market.md (양산 동일도 35%, 시장 니즈 75%)
- plan: reports/plans/pinchsense.md (16주 / BOM 163만원 / 리스크 7건 / fallback 5각도)
- evaluation: 2차 84(축)+8(가산)=**92**, **2순위**
- critique: 답변가능 2 / 답변부족 3 / 답변불가 0 → **REVISE**

---

## 1. 한 페이지 요약 (예선 제출 초안)

**캐치프레이즈**: **pinch test를 외우지 마라. 끈이 빨갛게 켜져서 직접 알려준다.**

**페인포인트** (페르소나: 카시트 두 아이 부모 + 간호사 배우자):
하네스 적정 장력은 손가락 핀치 테스트로 매번 확인해야 하지만, 두 아이를 한 명씩 채우는 동안 다른 아이가 떼를 쓰면 "대충 채워서 출발"이 일상. 두꺼운 외투(롱패딩) 입혔을 때 충돌 시 외투 압축 후 슬립스루 사고가 한국 겨울 핫이슈. econmingle: "한국 엄마들이 가장 많이 하는 실수가 ... 다리 사이 줄을 제대로 당기지 않는 것".

**핵심 메커니즘** (입력 → 처리 → 출력):
1. 5점식 하네스 좌·우 어깨 끈에 임베드된 슬림 모듈 ×2 + 체스트 클립 ToF 모듈 ×1 + 카시트 베이스 BLE 허브 ×1.
2. 어깨 모듈: FSR 어깨 압력 + 변형 게이지 끈 장력 + BNO055 IMU 끈 각도 → 다축 센서 퓨전으로 "하네스 적합도 점수(0~100)".
3. 체스트 클립 ToF(VL53L1X)가 클립 수직 위치(겨울드랑이 ±2cm)를 카시트 등받이 기준으로 측정.
4. 끈 위 RGB LED 스트립(1cm×8cm)이 적색(헐거움) → 황색(중간) → 녹색(적정) 실시간 색 변화. 부모는 끈을 당기면서 LED가 녹색이 될 때까지만 당기면 끝(매뉴얼·앱 불필요).
5. 출발 후 헐거워지면 BLE 허브가 운전석 후방 미니 디스플레이(또는 #2 LullaMirror 연동)에 알림.

**차별** (market 인용): "5점식 하네스 어깨 끈에 임베드된 실시간 장력·위치 LED 코칭은 미발견" (market.md L13). Cybex/Evenflo SensorSafe는 체스트 클립 BLE(미체결 알림). PinchSense는 **체결된 상태에서 장력 적정성을 끈 자체 LED로 0초 피드백 루프** + 두꺼운 외투 슬립스루 자동 검출.

**4대 테마**: 주=4번(완전히 새로운 개념: 카시트 능동 진단 부품화), 보조=1번(차량 혁신)
**수상 DNA 매핑**: ① 직관적 인터랙션(조작 0, 색만 보면 됨) / ③ 사회적/포용적 가치(영유아 안전 + 한국 겨울 슬립스루) / ④ 명확한 단일 페인포인트(매번 핀치 테스트 못 하는 행동 격차)

---

## 2. 기술 도해

```
┌──────────────────── 입력 (체결 동작 자체) ────────────────────┐
│                                                                │
│  ┌─────────────── 어깨 모듈 ×2 (좌/우) ─────────────┐          │
│  │                                                    │          │
│  │  FSR-402 어깨 압력  변형 게이지 끈 장력  BNO055 IMU│          │
│  │  (12.7mm 원형)      (Loadstar 50N+HX711) 9축       │          │
│  │       │                  │                 │       │          │
│  │       └──────────┬───────┴─────────────────┘       │          │
│  │                  ▼                                  │          │
│  │           ESP32-C3 (BLE 5.0)                        │          │
│  │                  │                                  │          │
│  └──────────────────┼──────────────────────────────────┘          │
│                     │                                              │
│  ┌──────────── 체스트 클립 모듈 ×1 ──────────┐                    │
│  │   VL53L1X ToF (클립 수직 위치)              │                  │
│  │   ESP32-C3 (BLE) + CR2032                   │                  │
│  └──────────────────┬──────────────────────────┘                  │
└─────────────────────┼──────────────────────────────────────────┘
                      ▼ BLE 메시
┌──────────────── 처리 (카시트 베이스 BLE 허브 ESP32) ─────────┐
│                                                                │
│  다축 센서 퓨전:                                               │
│    어깨 압력 + 끈 장력 + IMU 각도 → 하네스 적합도 점수 0~100   │
│  임상 룰베이스 (배우자 작성):                                  │
│    영유아 8kg / 토들러 15kg 단계별 흉곽 압박 한계 ±15%         │
│  외투 두께 추정:                                               │
│    체결 직후 5초 텐션 곡선 패턴(상대 변화) → 0/1/2겹 분리     │
│  체스트 클립 위치: ToF 거리 → 정상/오프셋                      │
│                                                                │
└────────────────────────────┬───────────────────────────────────┘
                             ▼
┌──────────────────── 출력 (≤200ms) ───────────────────────────┐
│                                                                │
│  WS2812B RGB LED 스트립 (1cm×8cm) — 끈 외측 임베드            │
│    헐거움 적색 → 중간 황색 → 적정 녹색 실시간                  │
│  코인 진동 모터 — 체스트 클립 위치 NG 시                       │
│  운전 중 헐거움 → BLE 허브 → 운전석 후방 미니 디스플레이       │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

**부품/기술 라벨**:
- 입력 (어깨 ×2): Interlink FSR-402 12.7mm + Loadstar iLoad Mini 50N + HX711 + BNO055 9축 IMU + ESP32-C3 SuperMini + LiPo 100~150mAh
- 입력 (체스트 클립): VL53L1X ToF + ESP32-C3 + CR2032 + 자석 분리 충전(또는 Critic 환류 후 슬리브 영구 임베드 + USB-C)
- 처리: ESP32 DevKit BLE 허브 + 의료등급 실리콘 IPX7 포팅 + Pogo pin 자석 충전 도크 (4채널)
- 출력: WS2812B 144LED/m 1cm×8cm 컷팅 + 코인형 10mm 3V 진동 모터 + (선택) BLE 미니 디스플레이 연동
- 케이싱: TPU(연질) + PETG 3D 프린팅 + 폴리에스터 5점식 어깨 패드 호환 슬리브

---

## 3. 4개월 일정 (16주 압축)

| 단계 | 주차 | 핵심 산출물 | 통과 기준 (게이트) |
|---|---|---|---|
| **M1 PoC** | W1-4 | 부품 발주 / ESP32-C3 펌웨어 부팅 / FSR+변형게이지 단독 캘리브레이션 / 외투 0/1/2겹 × 장력 3 × 9샘플 = 243건 | **W4 게이트: FSR+변형게이지 합성 신호로 헐거움/적정/과도 3-class F1 ≥0.85, 외투 0겹 vs 2겹 분리** |
| **M2 통합** | W5-8 | IMU 각도 보정·다축 센서 퓨전 / RGB LED 적·황·녹 ≤200ms / 어깨 모듈 직물 슬리브 봉제 시제품 / BLE 허브 펌웨어 | **W8 게이트: 어깨 끈 임베드 모듈 좌/우 1세트 + 마네킹 1명, 끈 당김에 LED ≤200ms + 적정 텐션 ±15%** |
| **M3 통합** | W9-12 | 체스트 클립 ToF 모듈 / IPX7 에폭시 포팅 + Pogo pin 자석 분리 충전 / 4모듈 BLE 멀티플렉싱 / 두 아이용 카시트 2대 통합 / 세탁 5회 테스트 | **W12 게이트: 4세트 동시 동작, 세탁 5회 후 드리프트 ≤10%, 체스트 클립 ±1cm** |
| **M4 발표** | W13-16 | 두꺼운 외투 슬립스루 마네킹 시연 / 5각도 영상 (부모 시점·LED 클로즈업·외투 0/2겹·하네스 측면·도크 1초) / 5분 슬라이드 / 리허설 ×3 | **W16: 5분 ±10초, 시연 1회 무사고 + fallback 영상 100%** |

(M1~M4 매 마일스톤 종료 시 배우자 1시간 임상 리뷰. 상세는 reports/plans/pinchsense.md §2 참조)

---

## 4. BOM 요약

| # | 핵심 부품 | 수량 | 소계(KRW) |
|---|---|---|---|
| 1 | ESP32-C3 SuperMini (어깨 ×6 + 클립 ×3) + ESP32 DevKit 허브 ×3 | 12 | 76,500 |
| 2 | FSR-402 어깨 압력 센서 | 8 | 36,000 |
| 3 | Loadstar iLoad Mini 50N + HX711 ADC (변형 게이지) | 4 | 140,000 |
| 4 | BNO055 9축 IMU | 6 | 108,000 |
| 5 | VL53L1X ToF (체스트 클립 위치) | 3 | 36,000 |
| 6 | WS2812B 144LED/m (1cm×8cm 컷팅 ×4) | 4 | 42,000 |
| 7 | 코인 진동 모터 + LiPo 100~150mAh + CR2032 + Pogo pin 자석 충전 + 4채널 충전 도크 | 1세트 | 122,200 |
| 8 | 직물 슬리브 (폴리에스터 어깨 패드 호환) ×6 + 의료등급 실리콘 포팅 | 1세트 | 83,000 |
| 9 | TPU + PETG 3D 프린팅 케이싱·마운트 | 1세트 | 50,000 |
| 10 | 카시트 시연 목업 (다이치/페도라 5점식 토들러, 중고) | 2 | 400,000 |
| 11 | 영유아 마네킹 ×2 + 두꺼운 외투 ×4 + 개발 보조 + 영상 촬영 보조 | 1세트 | 390,000 |
| **소계** | | | **1,483,700** |
| **예비비 10%** | | | 148,370 |
| **합계** | | | **약 163만원** |

**사내 한도 대비**: 본선 제작비 600~1,000만원 → BOM **163만원은 한도의 16~27% 사용**, 가장 여유. 임상 검증·사용자 테스트 보조비·예비 시제품 추가 발주 여지 충분.

> **단가 검증**: ESP32-C3 5,500원([디바이스마트](https://www.devicemart.co.kr/goods/view?no=15193221)), VL53L1X 12,000원([디바이스마트](https://www.devicemart.co.kr/goods/view?no=12990449)), FSR-402 4,400~7,500원([파츠파츠](https://parts-parts.co.kr/product/pp-a525-interlink-%EC%A0%95%ED%92%88-%EC%95%84%EB%91%90%EC%9D%B4%EB%85%B8-%EC%95%95%EB%A0%A5%EC%84%BC%EC%84%9C-fsr402/545/)), WS2812B 1m 10,200원([11번가](http://www.11st.co.kr/products/2125408632)).

---

## 5. 팀 매칭

| 역할 | 담당 | 비중 |
|---|---|---|
| HW (케이싱 3D 모델·출력 / PCB 시제품·납땜 / 직물 슬리브 봉제 / 실리콘 포팅 / 자석 충전 도크 / 카시트 임베드 가공) | 본인 | 100h / 35% |
| SW (ESP32-C3 펌웨어 / FSR+변형게이지+IMU 다축 센서 퓨전 / 임상 룰 알고리즘 구현 / BLE 메시 4모듈+허브 / LED 색상 맵핑) | 본인 | 130h / 45% |
| 데모 준비 (시연 시나리오·5각도 영상·fallback·슬라이드·리허설 ×3) | 본인 | 58h / 20% |
| 임상 룰베이스 작성 (영유아 8kg / 토들러 15kg 단계별 흉곽 압박 한계 N치 + 적정 텐션 ±15% 룰 5쪽) | 배우자 | 48h / 코드 0 |
| 페르소나 사용자 테스트 (4회 × 1h) | 페르소나 본인 + 지인 부모 | W11~W12 |
| 외부 자원 (직물 슬리브 봉제 외주 fallback) | 외주 | 옵션 |

---

## 6. 자체 평가 4축

| 축 | 점수 | 한 줄 근거 |
|---|---|---|
| ① 실현 가능성 | 19/25 | 인증·양산 분리(슬리브형 → KC 영향 0) + IPX7 포팅 + 자석 충전 도크 부모 액션 1회/월 + BOM 163만원 한도의 16~27% + 축소판 4단계 + 절대 사수 명시. 잔존: W5~W8 슬리브 봉제 비전문 영역 + KC 임상 시험은 사후 |
| ② 독창성 | 22/25 | 양산 동일도 35%, 5점식 하네스 어깨 끈 임베드 실시간 LED 코칭은 양산·크라우드펀딩·특허 모두 미발견. 메커니즘 새롭지만 콤포넌트 자체(장력+LED)는 산업 도메인 재조합 |
| ③ 기술 적합성 | 21/25 | 본인 다축 센서 퓨전 + 임베디드 SW + 캘리브레이션 + 배우자 영유아 흉곽 압박 한계. 양산 3단계(슬리브 → OEM 임베드 → 차량 OEM 통합). 잔존: ADAS 코어(객체 분류·TTC) 시너지 약함, "임베디드 다축 센서 퓨전"에 한정 |
| ④ 고객 지향성 | 22/25 | 한국 겨울 슬립스루 시즌성 + 정부 캠페인 + econmingle 페인 직격 + 페르소나 직접 보유 + 사용자 마찰 정량 답("LED 색 한 번 + 도크 1초/월"). 잔존: SUS 표본 수 미명시, WTP 정량 검증 없음 |
| **합계** | **84/100** + 가산점 8 = **92** | DNA ①③④ + HW 4종 + 시장 75% + 자율주행+의료 모두 살림. 사회적 가치 보수적 미부여 |

(critic 보정: 답변부족 3건은 §11에서 별도 명시. 상세는 reports/evaluations/pinchsense.md §2차 평가 참조)

---

## 7. 시장 차별 각도

1. **하네스 어깨 끈 자체 임베드 LED 실시간 색상 코칭** — Cybex/Evenflo SensorSafe는 체스트 클립 BLE(미체결 알림)에 한정. PinchSense는 끈을 당기는 행동 자체에 적·황·녹 LED 즉각 반응. **부모 시선이 끈을 당기는 손에 있을 때 그 자리에서 색이 바뀐다**(0초 피드백 루프) — 폰 알림은 부모 시선이 폰으로 이동해야 함.
2. **임상 룰 기반 적정 장력 점수** — 영유아 흉곽 압박 한계(배우자 간호사 강점)·토들러 단계별 적정 장력 범위를 압력+장력+IMU 다축 센서 퓨전으로 정량화. 단순 미체결 vs 정량 적합도 점수.
3. **체스트 클립 위치 ToF + 두꺼운 외투 슬립스루 검출** — 한국 겨울 시즌성 직격. 체결 직후 5초 텐션 곡선 패턴으로 외투 0/1/2겹 분리 → 적색 + 진동 + 운전석 후방 디스플레이 알림.

**인용 출처** (market.md):
- [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) — 동일도 50% (체결 모니터, 장력·위치 실시간 LED X)
- [Evenflo SensorSafe](https://www.evenflo.com/pages/sensorsafe) — 동일도 50% (BLE 체스트 클립)
- [Autoliv Smart Seat Belt (Active Pretensioner)](https://www.nextmsc.com/blogs/how-active-seat-belt-systems-are-evolving-for-the-future) — 동일도 40% (차량 시트벨트 한정)
- [Bosch Interior Sensing](https://www.bosch-mobility.com/en/solutions/interior/interior-sensing-solutions/) — 동일도 45% (미체결 검출까지)
- [Mercedes Pre-Safe Child Seat Concept](https://www.autocarindia.com/car-news/mercedes-benz-unveils-new-child-safety-seat-concept-413051) — 동일도 50% (생체 모니터, 장력 코칭 X)
- **관련 특허**: [US7347108B2 시트벨트 장력](https://patents.google.com/patent/US7347108B2/en), [US20040011277A1 카시트 시트벨트 장력](https://patents.google.com/patent/US20040011277), [US6728616B1 스마트 시트벨트](https://patents.google.com/patent/US6728616B1/en)
- [econmingle 롱패딩](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/) — "두꺼운 외투 + 헐거운 하네스 = 슬립스루" + 페르소나 페인 직격
- [클리앙 카시트 잘 태우기](https://www.clien.net/service/board/lecture/18121244)
- [나무위키 유아용 카시트](https://namu.wiki/w/%EC%9C%A0%EC%95%84%EC%9A%A9%20%EC%B9%B4%EC%8B%9C%ED%8A%B8)
- [isafe 행정안전부 카시트 캠페인](https://www.isafe.go.kr/www/downloadBbsFile.do?atchmnflNo=11169)

---

## 8. 수상 DNA 매핑

| DNA | 매핑 근거 |
|---|---|
| ① 직관적 인터랙션 | "끈을 당기다 LED가 녹색이 되면 멈춤" — 매뉴얼·앱 0, 부모는 색만 보면 됨. 2024 ADSM(시선 사이드미러, 대상)·2025 디지로그 락의 "조작 0 / 시각 즉답" DNA |
| ③ 사회적/포용적 가치 | 영유아 교통 안전 + 한국 겨울 롱패딩 슬립스루 시즌성 + 정부 캠페인 + 행정안전부 isafe.go.kr 운영 |
| ④ 명확한 단일 페인포인트 | "두 아이 떼쓰는 와중에 매번 핀치 테스트 실행 못 함" 행동 격차 페인 — econmingle "대충 채워서 출발" 직접 인용. 2024 H-브리즈(수소차 가습)·2025 트레일러 토잉의 "단일 행동 보조" DNA |

---

## 9. 데모 시나리오 5분

| 시간 | 화면/행동 | 메시지 |
|---|---|---|
| 0:00-0:30 | 페인 영상: 한국 부모가 겨울 패딩 입은 영유아를 카시트에 앉히고 하네스를 채우는 영상 (econmingle 인용 + 페르소나 동의 영상). 자막: "한국 영유아 카시트 슬립스루 사고 + 정부 캠페인 운영" | 두꺼운 외투 + 헐거운 하네스 = 슬립스루. 부모는 매번 pinch test를 못 한다 |
| 0:30-2:00 | 메커니즘 슬라이드 + 도해: ① 어깨 모듈(FSR + 변형게이지 + IMU + LED) 단면도 ② 체스트 클립 ToF ③ 끈 당김 → LED 적→황→녹 인터랙션 컷 ④ 임상 룰(영유아·토들러 흉곽 압박 한계 그래프) | pinch test가 카시트 안에 임베드된다. 매뉴얼·앱 불필요 |
| 2:00-4:00 | 실제 시연 (또는 사전 녹화): 마네킹 두 명 + 카시트 두 대. ① 마네킹 A(외투 없음): 적정 → LED 녹색 ② 마네킹 B(두꺼운 패딩): 채움 → 적색 + 진동 + 음성 "외투를 벗기세요" ③ 부모 페르소나 등장 → 외투 벗기고 재체결 → LED 녹색 ④ 운전 중 헐거워짐 시뮬 → BLE 허브가 운전석 후방 미니 디스플레이에 알림 | 부모의 인지 부담 0. 색만 보면 끝. 두꺼운 외투 슬립스루 자동 검출 |
| 4:00-5:00 | 차별 + 양산 + 팀: ① Cybex/Evenflo SensorSafe 비교 표 ② 양산 3단계(애프터마켓 슬리브 → OEM 임베드 → 차량 OEM 통합 BLE) ③ 본인 자율주행 다축 센서 퓨전 + 배우자 영유아 흉곽 임상 + 페르소나 두 아이 부모 | 차별·양산·팀이 한 줄로 닫힌다 |

**Fallback 5각도 영상**: 부모 시점(끈 당기기) / 어깨 LED 클로즈업 / 마네킹 정면(외투 0겹/2겹) / 카시트 측면(하네스 전체) / 충전 도크 1초 부착(사용자 마찰 방어).
**현장 PC 2대**: 라이브 PC + fallback 영상 PC 분리. 라이브 실패 시 30초 내 전환.

---

## 10. 인용 출처

**Market**:
- [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) / [Amazon Cybex Eternis](https://www.amazon.com/Cybex-Eternis-SensorSafe-Lavastone-Standard/dp/B07LBXTCR5)
- [Evenflo SensorSafe](https://www.evenflo.com/pages/sensorsafe)
- [Doona SensAlert](https://www.doona.com/en-us/doonalab/sensalert/shop-sensalert)
- [Next MSC Active Seat Belt (Autoliv)](https://www.nextmsc.com/blogs/how-active-seat-belt-systems-are-evolving-for-the-future)
- [Bosch Interior Sensing](https://www.bosch-mobility.com/en/solutions/interior/interior-sensing-solutions/)
- [Autocar India Mercedes Pre-Safe Child Seat](https://www.autocarindia.com/car-news/mercedes-benz-unveils-new-child-safety-seat-concept-413051)
- [InsideEVs Tesla Vision Seatbelt](https://insideevs.com/news/599709/elon-musk-reveals-tesla-vision-seat-belt-safety-system/amp/)
- [BeSafe Belt Keeper](https://carseatninja.com/products/besafe-belt-keeper)
- [RideSafer Travel Vest](https://ridesafertravelvest.com/)
- [SafeGuard ICS](https://www.imminet.com/products/safeguard/ics/)
- [Smart Kid Belt](https://smartkidbelt.co/products/smart-kid-belt-single-pack)
- [Kickstarter Smart Belt (Harmattan)](https://www.kickstarter.com/projects/smartbelt/the-smart-belt)
- [econmingle 롱패딩](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/)
- [클리앙 카시트 잘 태우기](https://www.clien.net/service/board/lecture/18121244)
- [나무위키 유아용 카시트](https://namu.wiki/w/%EC%9C%A0%EC%95%84%EC%9A%A9%20%EC%B9%B4%EC%8B%9C%ED%8A%B8)
- [Kakao 1boon KCC오토 카시트 100%](https://1boon.kakao.com/KCCAUTO/5c88cecfed94d200011af3e8)
- [isafe.go.kr 영유아 카시트 캠페인](https://www.isafe.go.kr/www/downloadBbsFile.do?atchmnflNo=11169)
- 특허: [US7347108B2](https://patents.google.com/patent/US7347108B2/en) / [US20040011277A1](https://patents.google.com/patent/US20040011277) / [US6728616B1](https://patents.google.com/patent/US6728616B1/en)

**Plan**:
- [디바이스마트 ESP32-C3 SuperMini](https://www.devicemart.co.kr/goods/view?no=15193221)
- [디바이스마트 VL53L1X](https://www.devicemart.co.kr/goods/view?no=12990449) / [eleparts VL53L1X](https://m.eleparts.co.kr/goods/view?no=9704916)
- [파츠파츠 FSR-402 4,400원](https://parts-parts.co.kr/product/pp-a525-interlink-%EC%A0%95%ED%92%88-%EC%95%84%EB%91%90%EC%9D%B4%EB%85%B8-%EC%95%95%EB%A0%A5%EC%84%BC%EC%84%9C-fsr402/545/) / [디바이스마트 FSR-402 7,500원](https://www.devicemart.co.kr/33870)
- [디바이스마트 BNO055](https://www.devicemart.co.kr/goods/view?no=6984694) / [eleparts BNO055](https://www.eleparts.co.kr/goods/view?no=3640969)
- [11번가 WS2812B 1m 144LED](http://www.11st.co.kr/products/2125408632)
- [babyseatmall 다이치 카시트](https://www.babyseatmall.net/)

---

## 11. Open Questions / 본선 진출 시 즉시 보강해야 할 사항

critic 5개 공격 중 **답변가능 2 / 답변부족 3 / 답변불가 0** — 사내 예선 통과 후 본선 4개월 동안 즉시 메워야 할 리스크. 환류 항목 그대로:

### 11-A. 외투 분포 일반화 + 동적 상황 안정성 (critic A, 답변부족)
- **남은 약점**: ① 0/1/2겹 라벨링은 "패딩 1겹"과 "슬리핑 백 1겹"을 같은 클래스로 묶음. 한국 부모 영유아 외투 분포는 다운 패딩·얇은 가디건·우비·슬리핑 백·머프 5종 × 사이즈 3 × 마모도. ② 5초 텐션 곡선은 정적, 토들러 발버둥 동적 상황에서 IMU+압력 곡선이 외투 노이즈와 구별 불가. ③ "1.5겹 중간 두께"가 누락 — 임상적으로 가장 위험.
- **즉시 보강** (M0~M1 1주): plan §7 데이터 1을 **외투 5종 × 사이즈 3 × 장력 3 × 9샘플 ≥ 405건**으로 확장 + "1.5겹 중간 케이스" 별도 클래스. 토들러 발버둥 동적 시나리오 50건 추가. 메트릭 재정의: "F1 ≥0.85" → "**1.5겹 중간 케이스 recall ≥0.80 + 황색 fallback 허용**".

### 11-B. 차별화 — Cybex SensorSafe 카시트 OEM 번들 시 추가 결제 정당화 (critic B, 답변가능)
- **즉시 보강** (M0 1주): plan §6 메커니즘 슬라이드에 **0초 피드백 루프 도해**(부모 시선 = 끈 당기는 손) 추가. 가격 정당화 — 카시트 평균 사용 6년 ÷ 8~12만원 = 월 1.4천원 재시각화. SensorSafe BLE는 "장력 적정성 측정 X"이므로 끈 LED 차별이 18개월 후에도 유지됨을 IP 출원 계획으로 명시.

### 11-C. KC R44/R129 인증 + 자석 분리형 모듈 삼킴 리스크 (critic C, 답변부족 — REVISE 트리거)
- **남은 약점**: ① 어깨 끈 부착물이 충돌 시 끈 변형·미끄러짐 거동 영향 → R44/R129 인증 무효화 가능성 + 다이치는 써드파티 어깨 패드 부착 시 안전 보증 거부 약관 검증 안 됨. ② 자석 분리형 모듈은 **3세 토들러 삼킴 리스크**(소형 부품 유아 삼킴 사고) — plan 어디에도 다뤄지지 않음.
- **즉시 보강** (M0 W2~W3): KATRI/KCL/KTL 자문 1회 + 다이치 한국 본사 약관 확인 1회 마일스톤 추가. **자석 분리형 모듈 → 슬리브 영구 임베드 + USB-C 직결** 재설계 (BOM #12 Pogo pin 자석 충전 32,000원 삭제, 축소판 우선순위 #3 채택). 4모듈 충전 동선은 "월 1회 일요일 저녁 일괄 충전" 부모 사용성 테스트 W11 검증.

### 11-D. 페인 — NHTSA 가이드 우위 + 손가락 핀치 테스트 대체재 (critic D, 답변부족)
- **남은 약점**: NHTSA·SafeKids·isafe.go.kr 1차 가이드 = "두꺼운 외투를 벗기고 카시트에 태우라" → PinchSense 핵심 가치 무용화 가능. 손가락 핀치 테스트는 매뉴얼 5분에 학습 가능. market.md 페인 인용 5건 모두 "외투 위험 인지"이지 "디바이스 LED 색을 보고 싶다" 직접 사용자 보이스 0.
- **즉시 보강** (M0 W2~W4): 페인 재정의 — **"외투 벗기기/핀치 테스트를 알지만 두 아이 떼쓰는 와중에 매번 못 한다"** 행동 격차로 한 줄 갱신. plan §6 0:00~0:30에 이 행동 격차 명시. plan §7 데이터 5로 **부모 페르소나 인터뷰 5명 정량 데이터** 추가 (디바이스 사용 의향 1~7점 척도). plan §6 4:00~5:00 비교 표에 "**손가락 핀치 = 정확하나 매번 실행 못 함 / LED = 끈 당기는 손에 즉시 피드백**".

### 11-E. 발표 — 단순 LED 인디케이터 평가절하 위험 + 5분 임팩트 (critic E, 답변가능)
- **즉시 보강** (M0 W14): 메커니즘 4컷 → **1컷 단일 인포그래픽**("끈 당기는 손 → 다축 센서 퓨전 → 임상 룰 → LED 색"). 시연 멘트 재설계 — 2:00~4:00에 발표자가 "이 LED 색은 카시트가 매번 측정하는 어깨 압력 + 끈 장력 + 끈 각도 3축의 임상 룰 결과입니다" 1회 명시. 운전 중 알림 라이브화 — 미니 디스플레이 시연 책상에 두고 BLE 허브 → 디스플레이 라이브 점등. 마지막 슬라이드를 "**Hyundai/Kia 패밀리 SUV 신차 동봉 시 OEM 인센티브 1쪽**"으로 (2차 critic 약점 #3 직격).

### 즉시 환류 작업 우선순위 (1~2주 안에 완료 후 critic 재평가)
1. **자석 분리형 → 슬리브 영구 임베드 + USB-C** 재설계 (plan_designer, 삼킴 리스크 차단)
2. **W2~W3 KC 인증 자문 마일스톤** 추가 (plan_designer)
3. **페인 재정의** 행동 격차 (idea_generator) + **부모 인터뷰 5명** (plan_designer)
4. **데이터 1 확장** 외투 5종×3×3×9 ≥405건 + 1.5겹 + 동적 50건 (plan_designer)
5. **§6 시연 슬라이드 재설계** 1인포그래픽 + 라이브 미니 디스플레이 + OEM 인센티브 마지막 슬라이드 (plan_designer)
6. **다이치 약관 확인** + **R44/R129 액세서리 영향 사례** (market_researcher)

---

## 산출물 저장
**경로**: `/Users/suyoungchoi/my_workspace/idea_festival_office/reports/final/2026-05-10-pinchsense.md`
