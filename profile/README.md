# DHL xArm Vision Pick-and-Place

xArm + OAK-D 뎁스카메라 + GPU 서버로 **특정 물체를 인식해서 집어 옮기는** 픽앤플레이스 시스템.

## 시스템 구성

```
OAK-D(팔 끝) ──USB──> 라즈베리파이 ──WiFi──> GPU 서버 ──유선LAN──> xArm
                       (영상 중계)         (인식·좌표변환·제어)    (집기 실행)
```

| 장치 | 역할 | Repo |
|------|------|------|
| 라즈베리파이 | OAK-D 영상/뎁스 중계 | [`rpi4-server`](https://github.com/dhl-xarm/rpi4-server) |
| GPU 서버 | 인식 + 좌표변환 + xArm 제어 | [`gpu-server`](https://github.com/dhl-xarm/gpu-server) |
| (기구) | 3D 출력물 (카메라 마운트 등) | [`hardware`](https://github.com/dhl-xarm/hardware) |

## 처음이라면 여기부터

1. [문서 홈](https://github.com/dhl-xarm/docs)
2. [ArUco 보드 만들기](https://github.com/dhl-xarm/docs/blob/main/01-aruco-board.md)
3. [핸드아이 캘리브레이션](https://github.com/dhl-xarm/docs/blob/main/02-hand-eye-calibration.md)
4. [픽앤플레이스 + 처음 셋업 프로토콜](https://github.com/dhl-xarm/docs/blob/main/03-pick-place.md)

## 운영/인수인계 (자리 바꿀 때마다)

`gpu-server/setup_positions.py` 1회 실행 → 로봇을 손으로 움직여 **테이블 높이 · 스캔 위치 · 놓는 위치**를 잡으면 `config.ini [positions]` 가 자동으로 채워짐. 이후 `pick/pick_place.py` 실행 시 무조건 **① 로봇 홈(initial, gohome) → ② 스캔 위치** 순으로 자동 이동. (코드 수정 불필요)

## 진행 상황

- [x] 카메라(OAK-D) 뎁스 + 3D 좌표 취득
- [x] 라즈베리파이 → GPU 서버 전송
- [x] 핸드아이 캘리브레이션 (5축 보정 포함)
- [x] 흰색 포장물 검출(contour) + **단일 픽앤플레이스** ✅
- [x] **여러 개 자동 루프** — 검출품질로 집기/펼치기(push) 자동판단, PLACE로 이동 ✅
- [x] **운영 자동화** — 시작 시 초기 위치 자동 이동, 자리 의존값 config화 + 셋업 스크립트 ✅
- [ ] **심한 겹침 대응** — UOIS/SAM2 인스턴스 분할 ← 다음(고전 CV 한계)
