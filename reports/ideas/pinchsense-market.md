# 시장 조사 — PinchSense (5점식 하네스 장력·위치 실시간 LED 코칭 모듈)
**slug**: `pinchsense`
**작성일**: 2026-05-10
**작성자**: market_researcher
**대상 batch**: reports/ideas/2026-05-10-batch2.md

## 종합 판정
| 지표 | 값 |
|---|---|
| 양산품 동일도 | 35% |
| 시장 니즈 강도 | 75% (강) |
| 추천 액션 | **GO** |
| 한 줄 결론 | 카시트 점유·체스트 클립 BLE(Cybex/Evenflo SensorSafe) + 차량 시트벨트 텐션 센서(Autoliv 등) 양산이나 모두 점유·미체결 알림에 한정. 5점식 하네스 어깨 끈에 임베드된 실시간 장력·위치 LED 코칭은 미발견. |

## OEM 양산 매트릭스
| 카시트/차량 OEM | 모델·연식 | 공식 명칭 | 동일/유사 메커니즘 | 동일도 | 출처 |
|---|---|---|---|---|---|
| Cybex | Sirona S/M, Eternis S | SensorSafe 2.0 (체스트 클립) | 체스트 클립 BLE → 폰 알림(미체결·과열·과다 착석시간) | 50% (체결 모니터, 장력·위치 실시간 LED X) | [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) / [Amazon Cybex Eternis](https://www.amazon.com/Cybex-Eternis-SensorSafe-Lavastone-Standard/dp/B07LBXTCR5) |
| Evenflo | SensorSafe 라인업 | SensorSafe BLE Chest Clip | BLE 체스트 클립 + 폰 앱 알림 | 50% | [Evenflo SensorSafe](https://www.evenflo.com/pages/sensorsafe) |
| Doona | SensAlert | Doona SensAlert | 어린이 점유 감지, 부모 이격 알림 | 30% | [Doona SensAlert](https://www.doona.com/en-us/doonalab/sensalert/shop-sensalert) |
| Autoliv | BMW 7-series, Mercedes E-Class | Smart Seat Belt (Active Pretensioner) | 충돌 사전 인장기 + 장력 측정. 차량 시트벨트 한정 | 40% (메커니즘 일부, 카시트 하네스 X, 실시간 코칭 X) | [Next MSC Active Seat Belt](https://www.nextmsc.com/blogs/how-active-seat-belt-systems-are-evolving-for-the-future) |
| Bosch | (다수 OEM 양산) | Interior Sensing | 룸미러 인근 카메라로 후석 안전벨트 미체결 감지 | 45% (미체결 검출까지, 장력 정량화·LED 코칭 X) | [Bosch Mobility](https://www.bosch-mobility.com/en/solutions/interior/interior-sensing-solutions/) |
| Mercedes-Benz | Pre-Safe Child Seat Concept | Pre-Safe Child Seat | 카시트 내장 카메라·센서, 어린이 체온/맥박/호흡 모니터링 | 50% (생체 모니터, 하네스 장력 코칭 X) | [Autocar India Mercedes](https://www.autocarindia.com/car-news/mercedes-benz-unveils-new-child-safety-seat-concept-413051) |
| Tesla | (전 모델) | Tesla Vision Seatbelt | 캐빈 비전 카메라로 안전벨트 착용 검출 | 40% (착용 검출, 장력 코칭 X) | [InsideEVs Tesla Vision Seatbelt](https://insideevs.com/news/599709/elon-musk-reveals-tesla-vision-seat-belt-safety-system/amp/) |

**관련 특허**: 
- US7347108B2 (시트벨트 장력 센서) — [Google Patents](https://patents.google.com/patent/US7347108B2/en)
- US20040011277A1 (어린이 카시트 시트벨트 장력 감지 장치) — 카시트 고정용 시트벨트 장력 감지에 한정, 5점식 하네스 자체 LED 실시간 코칭 X — [Google Patents](https://patents.google.com/patent/US20040011277)
- US6728616B1 (스마트 시트벨트 제어) — [Google Patents](https://patents.google.com/patent/US6728616B1/en)

**핵심 갭**: 양산은 모두 ① 차량 시트벨트(카시트 하네스 X), ② 점유·미체결·충돌 사전 인장(실시간 장력 적정성 코칭 X), ③ LED 색 변화로 부모 가이드 X. PinchSense의 "어깨 끈 자체 LED + 실시간 장력 적정성 색상 코칭"은 양산·특허 모두 미발견.

## 경쟁사/스타트업/애프터마켓
- **5 Point Plus Anti-Escape Harness Vest** — 패시브 anti-escape 패드, 능동 측정 X — 동일도 15% — (구글 검색 시 사이트 다수)
- **Smart Kid Belt** — 안전벨트 변형 액세서리(ECE R44 인증), 경고 LED·센서 X — 동일도 10% — [Smart Kid Belt](https://smartkidbelt.co/products/smart-kid-belt-single-pack)
- **BeSafe Belt Keeper** — 하네스 어깨 끈 슬립 방지 클립(피지컬), 능동 측정 X — 동일도 10% — [BeSafe Belt Keeper](https://carseatninja.com/products/besafe-belt-keeper)
- **RideSafer Travel Vest** — 안전벨트 통합 조끼형 (장력 LED 측정 X) — 동일도 10% — [RideSafer Travel Vest](https://ridesafertravelvest.com/)
- **SafeGuard ICS** — 통학버스용 카시트 시스템 (정량 장력 LED X) — 동일도 15% — [SafeGuard ICS](https://www.imminet.com/products/safeguard/ics/)
- **Cognex Seatbelt Inspection** — 산업용 비전(자동차 제조 라인 시트벨트 품질 검사) — 동일도 10% (제조 라인용, 카시트 임베드 X) — (배치2 자가 검증 인용)
- **Kickstarter "Smart Belt" (Harmattan Design)** — 성인 벨트, 카시트 무관 — 동일도 5% — [Kickstarter Smart Belt](https://www.kickstarter.com/projects/smartbelt/the-smart-belt)
- **종합**: **5점식 하네스 어깨 끈 임베드 실시간 LED 코칭 + 체스트 클립 ToF 위치 측정 모듈은 양산·크라우드펀딩 모두 미발견**.

## 한국 사용자 니즈 증거
- **econmingle "롱패딩 입혔는데... 카시트 태울 때 아빠들이 하는 위험한 실수"** — "두꺼운 패딩을 입은 채 아이를 카시트에 태우는 일이 치명적인 결과로 이어질 수 있다는 실험 결과", "충돌 시 두꺼운 외투로 인해 카시트에서 몸이 빠져나오는 '슬립스루' 현상 발생", "한국 엄마들이 가장 많이 하는 실수가 아이들이 불편해한다는 이유로 유아벨트를 채우고 난 다음에 밑에 다리 사이 줄을 제대로 당기지 않는 것" — 페르소나 페인 직격 — [econmingle](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/)
- **clien "카시트 잘 태우기에 대한 이야기"** — 한국 부모 자가 가이드, 적정 장력에 대한 부모 간 자발 정보 공유 활발 — [클리앙](https://www.clien.net/service/board/lecture/18121244)
- **나무위키 "유아용 카시트"** — "엄마들은 아이에게 상황에 따라 카시트 각도를 눕혔다, 세웠다 하는데 이건 잘못된 방법이며, 카시트를 고정하고 있는 벨트는 항상 타이트해야 합니다" — [나무위키](https://namu.wiki/w/%EC%9C%A0%EC%95%84%EC%9A%A9%20%EC%B9%B4%EC%8B%9C%ED%8A%B8)
- **kakao 1boon "지금까지 잘못 쓰고 있었어? 카시트 100% 활용하는 법"** — KCC오토 매체에서 부모 잘못된 카시트 사용 콘텐츠 제작 — [1boon](https://1boon.kakao.com/KCCAUTO/5c88cecfed94d200011af3e8)
- **isafe 행정안전부 "영유아용 카시트 올바로 장착해야 안전 담보"** — 정부 기관 캠페인 운영 중 — [isafe.go.kr](https://www.isafe.go.kr/www/downloadBbsFile.do?atchmnflNo=11169)
- **종합**: 페인포인트 강도 **강**. 한국 부모 사이에서 "두꺼운 외투 + 헐거운 하네스 = 슬립스루 사고" 인지 + 자발 가이드 콘텐츠 다수 + 정부 기관 캠페인 + 페르소나 페인(두 아이를 한 명씩 채우면서 다른 아이가 떼를 쓰면 대충 채움)이 강력히 일치. 겨울철 시즌성도 한국 시장 강한 트리거.

## 차별 가능 각도
1. **하네스 어깨 끈 자체 임베드 LED 실시간 색상 코칭** — Cybex/Evenflo SensorSafe는 체스트 클립 BLE(미체결 알림). PinchSense는 끈을 당기는 행동 자체에 적·황·녹 LED 즉각 반응. "끈을 당기다가 LED가 녹색이 되면 멈춤" → 부모는 매뉴얼·앱 없이 끝.
2. **임상 룰 기반 적정 장력 점수** — 영유아 흉곽 압박 한계(배우자 간호사 강점)·토들러 단계별 적정 장력 범위 임상 룰을 압력+장력+IMU 다축 센서 퓨전으로 정량화. 단순 미체결 vs 정량 적합도 점수.
3. **체스트 클립 위치 ToF 측정 + 두꺼운 외투 시나리오 검출** — 겨울 패딩 입은 슬립스루 위험을 두께/압력 패턴으로 감지하고 LED 적색 + 진동 경고. 한국 겨울 시즌성 강타.

## 다음 단계 권고
- evaluator 가 평가 시 (1)/(2)/(3)을 "독창성·고객 지향성·기술 적합성"에 사용. 특히 (3)은 한국 시즌성·페르소나 정합성 강력.
- critic이 "어깨 끈 전자부품 임베드 안전성/세탁성/인증(KC/CCC/FMVSS)" 공격 시 답변: **데모용 액세서리 형태로 시연 + 양산은 카시트 OEM 협업** 분리. 직물 세탁성은 IPX7 모듈 + 분리형 자석 마운트로 회피.
- **양산 경로 보강**: 다이치(국내 1위 카시트)·페도라·브라이택스 한국 협업 + 현대차·기아 패밀리 SUV 신차 구매 시 카시트 번들 액세서리. (#3 SeatFit Coach와 양산 경로 시너지 — 둘 다 패밀리 SUV 신차 동봉 패키지로 묶일 수 있음)
- 차별 한 줄 (수정 권장): "체스트 클립 BLE 알림(Cybex SensorSafe)은 '미체결'만, PinchSense는 **체결된 상태에서 장력 적정성을 끈 자체 LED로 실시간 코칭**한다 — 두꺼운 외투 슬립스루 방지가 핵심 새 가치."
- **약점**: 5점식 하네스 직물 속에 슬림 모듈 임베드는 메이커 환경에서 시연 수준 한계. 본선용 데모 영상은 카시트 1대 + 마네킹 + 두꺼운 외투 시나리오 영상 필수.
