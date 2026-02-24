# 100대명산 발자국 웹 서비스

대한민국 지도 위에 블랙야크 100대명산 위치를 표시하고, 현장에서 GPS로 발자국을 찍어 SQLite DB로 관리하는 웹 서비스입니다.

## 기능

- 카카오맵에 100대명산 마커 표시 (도로/위성/위성+도로 전환)
- 산 클릭 시 정보 창 + **발자국 찍기** (정상 반경 500m 이내에서만 가능)
- 내 방문 목록 사이드바 (기기별 `user_key`로 구분)
- REST API: `/api/mountains`, `/api/footprints` (GET/POST)

## 사전 준비

- Node.js 18+
- Python 3 (DB/CSV 초기화용, 선택)

## 설치 및 실행

### 1. 의존성 설치

```bash
npm install
```

### 2. DB 준비

처음 한 번 CSV에서 SQLite DB를 생성합니다.

```bash
python csv_to_sqlite.py
```

이미 DB 파일만 있고 `footprints` 테이블이 없다면:

```bash
python migrate_footprints.py
```

### 3. 서버 실행

```bash
npm start
```

브라우저에서 `http://localhost:8080/blackyak.html` 로 접속합니다.

- API + 정적 파일 모두 같은 서버(8080)에서 제공됩니다.
- DB 파일 경로: `블랙야크_100대명산.db` (프로젝트 루트). `DB_PATH` 환경변수로 변경 가능합니다.

## 배포

- **권장**: 서버가 있는 환경 (Railway, Render, Fly.io, VPS 등)에 배포.
- `npm start`가 곧 `node server.js`이므로, 배포 플랫폼에서 **Start Command**를 `npm start` 또는 `node server.js`로 설정하면 됩니다.
- **환경변수**
  - `PORT`: 서버 포트 (기본 8080)
  - `DB_PATH`: SQLite 파일 절대 경로 (선택, 기본: 프로젝트 내 `블랙야크_100대명산.db`)
- 배포 시 DB 파일이 유지되도록 **영속 디스크(Volume)**를 사용하거나, 배포 플랫폼의 디스크 경로에 `DB_PATH`를 지정하세요.
- **카카오맵**: 배포 도메인을 [카카오 개발자 콘솔](https://developers.kakao.com) 앱 설정의 "플랫폼 > Web"에 등록해야 합니다.

## API

| 메서드 | 경로 | 설명 |
|--------|------|------|
| GET | `/api/mountains` | 100대명산 목록 (지도 마커용) |
| GET | `/api/mountains/:id` | 산 상세 |
| POST | `/api/footprints` | 발자국 등록 (body: `mountain_id`, `latitude`, `longitude`, `user_key`) |
| GET | `/api/footprints?user_key=xxx` | 해당 사용자 방문 목록 |

## 파일 구성

- `server.js` - Express API + 정적 파일 서빙
- `blackyak.html` - 지도 + 발자국 UI
- `csv_to_sqlite.py` - CSV → SQLite (mountains + footprints 테이블)
- `migrate_footprints.py` - 기존 DB에 footprints 테이블만 추가
- `블랙야크 100대명산 DB.csv` - 산 원본 데이터
- `블랙야크_100대명산.db` - SQLite DB (실행 후 생성)
