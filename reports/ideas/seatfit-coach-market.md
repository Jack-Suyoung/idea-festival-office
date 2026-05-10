# 시장 조사 — SeatFit Coach (휴대형 비전 AI 카시트 설치 점검 도구)
**slug**: `seatfit-coach`
**작성일**: 2026-05-10
**작성자**: market_researcher
**대상 batch**: reports/ideas/2026-05-10-batch2.md

## 종합 판정
| 지표 | 값 |
|---|---|
| 양산품 동일도 | 30% |
| 시장 니즈 강도 | 80% (강) |
| 추천 액션 | **GO** |
| 한 줄 결론 | 카시트 OEM 자체 설치 보조(Britax ClickTight·Cybex SensorSafe·Nuna LUMN 등)는 양산이나 모두 단일 카시트 한정. 부모 휴대형 + 다 모델 비전 AI 진단 + ToF/로드셀 정량 측정 통합 도구는 미발견. 차별축 가장 명확. |

## OEM 양산 매트릭스
| 카시트 OEM | 모델·연식 | 공식 명칭 | 동일/유사 메커니즘 | 동일도 | 출처 |
|---|---|---|---|---|---|
| Britax | 다수 (Advocate, Boulevard, Marathon, Alpine Base) | ClickTight Installation System | 강철 프레임 자체 자동 텐션 + 시각적 인디케이터 | 35% (자체 카시트 한정, 측정 정량화 X) | [Britax ClickTight](https://us.britax.com/why-britax/innovation/clicktight) / [Britax Alpine Base](https://us.britax.com/shop/car-seat-accessories/britax-alpine-infant-car-seat-base-with-clicktight) |
| Cybex | Sirona S/M, Eternis S | SensorSafe 2.0 + Dual Level Indicator | 체스트 클립 BLE → 폰 알림(미체결·과열·과다 착석시간), 카시트 자체 듀얼 레벨 인디케이터 + 앱 설치 가이드 영상 | 40% (앱 가이드 양산, 정량 측정 X) | [Cybex SensorSafe Kit](https://www.cybex-online.com/en/us/sensorsafe-safety-kit_us.html) / [Cybex App](https://apps.apple.com/us/app/cybex/id6474168019) |
| Nuna | LUMN, PIPA, RAVA | True Tension door + Virtual Car Seat Check | 도어 타입 텐션 자동 / 인증 기술자(CPST) Zoom 가상 점검 | 30% (인적 서비스, AI 자동 진단 X) | [Nuna Virtual Car Seat Check](https://nunababy.com/usa/virtual-car-seat-check) |
| Evenflo | SensorSafe 라인업 | SensorSafe Bluetooth Chest Clip | BLE 체스트 클립 + 폰 앱 알림(미체결·과열·과다 착석) | 35% (체결 모니터 한정, 설치 정확도 진단 X) | [Evenflo SensorSafe](https://www.evenflo.com/pages/sensorsafe) |
| Doona | SensAlert | Doona SensAlert | 어린이 점유 감지 + 부모 이격 시 알림 | 25% (점유·방치 알림, 설치 진단 X) | [Doona SensAlert](https://www.doona.com/en-us/doonalab/sensalert/shop-sensalert) |
| Maxi-Cosi | (다수) | 자체 인디케이터 | 등받이 각도 인디케이터 (피지컬 버블·라벨) | 20% (자체 카시트 한정) | [Bambi Baby Comparison](https://www.bambibaby.com/blogs/learning-center/nuna-lumn-vs-britax-grow-with-you-clicktight-harness-2-booster-car-seat-car-seat-comparison) |

**핵심 갭**: 모든 카시트 OEM 솔루션은 ① **자체 카시트 한정** (브랜드 종속), ② 등받이 각도는 자체 피지컬 인디케이터 / 정량 측정 X, ③ 하네스 장력은 pinch test 수동, ④ ISOFIX 잠금 인디케이터·라벨 만료일 자동 인식 X. 부모가 들고 다니며 **다 모델·정량·자동 진단**하는 휴대형 도구는 미발견.

## 경쟁사/스타트업/애프터마켓
- **Cybex App / SensorSafe 2.0** — 카시트 모델별 설치 영상·FAQ 가이드, BLE 체스트 클립 알림. 영상 안내 위주, 자동 측정 X — 동일도 40% — [Cybex App](https://apps.apple.com/us/app/cybex/id6474168019)
- **Nuna Virtual Car Seat Check** — Zoom 화상 인증 기술자 점검, 인적 서비스 — 동일도 30% — [Nuna Virtual](https://nunababy.com/usa/virtual-car-seat-check)
- **Filo Tata Pad** — 카시트 점유 감지 + 부모 이격 알림, BLE — 동일도 20% — [Motherly review](https://www.mother.ly/parenting/safety/car-seat-safety/best-car-seat-sensor-app/)
- **Elepho eClip** — BLE 점유 감지 + 25 ft 이격·온도 알림 — 동일도 20% — [Elepho eClip](https://elepho.com/products/eclip-baby-reminder-for-your-car)
- **Smart Kid Belt** — 안전벨트 변형 액세서리(ECE R44 인증) — 동일도 15% — [Smart Kid Belt](https://smartkidbelt.co/products/smart-kid-belt-single-pack)
- **Clinometer / Car Inclinometer** (Android/iOS 무료 앱) — 단순 폰 자이로 기반 각도 측정. 카시트 ToF 정량 X, 모델별 가이드 매칭 X — 동일도 20% — [Clinometer Play Store](https://play.google.com/store/apps/details?id=com.plaincode.clinometer) / [Car Inclinometer Play Store](https://play.google.com/store/apps/details?id=com.darkcube.carinclinometer)
- **LATCH 보조 가이드 클립 ($7 미만)** — 앵커 위치 인지용 단순 플라스틱 가이드 — 동일도 5% — [Amazon ISOFIX Latch](https://www.amazon.com/isofix-latch/s?k=isofix+latch)
- **종합**: **부모용 휴대형 자가 점검 비전 AI 도구는 미발견**. NHTSA 46% 통계가 시장 통증의 정량 근거.

## 한국 사용자 니즈 증거
- **미주 한국일보 "어린이 카시트 46%가 잘못 사용해"** — NHTSA 발표 인용, 한국 미주 부모 커뮤니티 인지 — [미주 한국일보](http://ny.koreatimes.com/article/20220921/1433647)
- **클리앙 "카시트 잘 태우기에 대한 이야기"** — 부모 자발 가이드 글, 정확한 설치 노하우 공유 부족이 페인 — [클리앙](https://www.clien.net/service/board/lecture/18121244)
- **보배드림 "카시트 장착 안전 수칙 10계명"** — 운전자 커뮤니티에서 카시트 설치 매뉴얼 콘텐츠 — [보배드림](https://m.bobaedream.co.kr/board/bbs_view/strange/2827550)
- **베이비뉴스 "유아용 카시트, 사장님 자리가 제일 안전"** — 정확한 설치 가이드 매체 콘텐츠 다수 — [베이비뉴스](https://www.ibabynews.com/news/articleView.html?idxno=10408)
- **econmingle "롱패딩 입혔는데... 카시트 태울 때 아빠들이 하는 위험한 실수"** — "두꺼운 외투로 인해 벨트가 신체에 밀착되지 않고 느슨해진 상태에서 사고가 발생하면, 충격이 옷을 압축하는 데 먼저 소비되기 때문에 탑승자의 몸이 더 멀리 튕겨 나갈 수 있다", "한국 엄마들이 가장 많이 하는 실수가 아이들이 불편해한다는 이유로 유아벨트를 채우고 난 다음에 밑에 다리 사이 줄을 제대로 당기지 않는 것" — [econmingle](https://econmingle.com/car/i-made-my-kids-wear-long-padded-coat/)
- **GM Korea 블로그 "전문가가 알려주는 나이별 올바른 카시트 장착법"** — OEM 차원에서도 설치 가이드 콘텐츠 제작 — [GM Korea Blog](https://blog.gm-korea.co.kr/5801)
- **CO Dept of Transportation Korean Guide PDF** — 한국어 카시트 가이드 별도 제작될 만큼 한국어 사용자 니즈 큼 — [CDOT Korean Guide](https://www.codot.gov/safety/carseats/assets/multilingual-child-passenger-safety-guides/child-passenger-safety-guide-korean.pdf)
- **종합**: 페인포인트 강도 **강**. NHTSA 46% 오류 통계가 정량 근거 + 한국 매체·커뮤니티 다수가 카시트 설치를 어려워하는 부모를 대상으로 가이드 콘텐츠 제작 중 + CPST 인증 점검소가 한국에 거의 없음(검색에서 확인). 페르소나(간호사·두 아이) 강점과 정확히 일치.

## 차별 가능 각도
1. **휴대형 + 다 모델 + 정량 측정** — Britax/Cybex/Nuna 등 모든 OEM 솔루션은 자체 카시트 한정 + 정성적 인디케이터. 부모 손에 들리고 ToF로 등받이 각도(±1°) + 로드셀로 하네스 장력(±N) 정량화 + 라벨 OCR로 모델 자동 인식하는 도구는 미발견.
2. **임상 룰베이스 + 모델 DB 매칭** — 카시트 모델별 가이드(영유아 45° / 토들러 30~45°, 하네스 어깨 슬롯, 체스트 클립 위치) + 영유아 흉곽 압박 한계(배우자 간호사 강점) + 만료일/리콜 DB. 카시트 OEM 단독으로는 다른 OEM 카시트 가이드를 들고 있을 이유 없음 → 중립적 진단 도구라는 포지션이 차별.
3. **5초 자가 점검 UX** — Nuna Virtual은 Zoom 예약 인적 서비스, CPST는 한국에 거의 없음. "5초 거치 + LED·음성 가이드"는 "당장 차에 달린 채로 점검 후 출발"이라는 새 사용 시나리오.

## 다음 단계 권고
- evaluator 가 평가 시 (1)/(2)/(3)을 "독창성·기술 적합성·고객 지향성·실현 가능성" 4축에 모두 사용 가능. 4축 균형이 가장 좋은 후보.
- critic이 "왜 휴대형 박스인가, 스마트폰 앱이면 안 되나" 공격 시 답변: **ToF 4×4 zone, 2축 로드셀, IR 링 광원은 폰에 없음** + 정량 측정이 안전 기능의 핵심 + Maker's Track HW 자격.
- 양산 경로는 **현대차·기아 신차 구매 시 동봉 / 카시트 OEM(다이치·페도라) 협업 / 서울시 또는 보건복지부 영유아 안전 지원 사업** 등 명시.
- **사내 양산 경로 보강**: 현대차·기아의 패밀리 SUV(Palisade·Carnival·Sorento·Telluride) 신차 구매 시 동봉 액세서리 → 페르소나(카시트 두 아이 부모) 직접 타겟 + 사회적 가치(영유아 교통 안전) 정합성 우수.
