# bus-runner

버스편(bus.factpane.com)과 막차편(makcha.factpane.com)의 매일 수집·빌드·배포를 공개 저장소 `andonghun/bus-runner`에서 돌리기 위한 파일이다.

버스편 수집은 TAGO 터미널 쌍 약 33만 개를 훑느라 하루 210분을 쓴다. sites는 비공개 저장소라 Actions 무료분이 월 2,000분이고, 2026-11-09에 11월 무료분을 9일 만에 다 써서 모든 사이트 워크플로가 멈췄다. 공개 저장소의 Actions는 무료·무제한이라 버스편을 그쪽으로 옮겼다. 버스편을 빼도 sites가 월 2,700분쯤 써서 하루 30분 남짓 쓰는 막차편도 옮겼다.

## 구조

- `.github/workflows/bus.yml`, `makcha.yml`이 bus-runner 저장소에 그대로 들어간다. 매 실행마다 `andonghun/sites`의 main을 읽기 전용 배포 키(`SITES_DEPLOY_KEY`)로 받아 sites의 같은 이름 워크플로와 같은 단계를 밟는다. 코드를 고치려면 sites에서 고친다. 여기 워크플로 파일을 고쳤으면 bus-runner 저장소에도 같은 파일을 올린다.
- 배포는 bus-site·makcha-site 저장소의 쓰기 배포 키(`DEPLOY_KEY_BUS`, `DEPLOY_KEY_MAKCHA`)로 한다. sites에 있던 키와는 다른 키다.
- 데이터(버스 SQLite·OSM 약 120MB, 막차 수백 KB)는 bus-runner의 Actions 캐시로 넘긴다. 캐시가 없으면 bus-runner 릴리스 `seed`의 `bus-data.tar.gz`, `makcha-data.tar.gz`로 시작한다. seed는 sites의 `bus-export` 워크플로(입력 `site`)로 만든다.
- 공개되는 것은 실행 로그뿐이다. 로그에는 수집 건수와 오류 요약만 나오고 키는 GitHub가 가린다.

## bus-runner 저장소의 비밀값·변수

| 이름 | 종류 | 값 |
|---|---|---|
| `SITES_DEPLOY_KEY` | secret | sites 읽기 전용 배포 키의 개인 키 |
| `DEPLOY_KEY_BUS` | secret | bus-site 쓰기 배포 키의 개인 키 |
| `DEPLOY_KEY_MAKCHA` | secret | makcha-site 쓰기 배포 키의 개인 키 |
| `TAGO_API_KEY` | secret | 공공데이터포털 인증키(sites의 같은 이름 secret과 같은 값) |
| `INDEXNOW_KEY` | secret | IndexNow 키(sites의 같은 이름 secret과 같은 값) |
| `GA_MEASUREMENT_ID_BUS`, `GA_MEASUREMENT_ID_MAKCHA`, `ADS_CLIENT` | variable | sites 저장소 변수와 같은 값 |

## 옮긴 뒤

bus-runner 첫 실행이 배포까지 성공하면 sites의 `.github/workflows/bus.yml`, `makcha.yml`에서 `schedule`을 뺀다. 두 곳이 같은 날 배포하지 않게 하려는 것이다.
