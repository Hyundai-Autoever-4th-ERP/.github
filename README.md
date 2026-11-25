# 현대파츠 부품 ERP 팀 컨벤션

현대오토에버 모빌리티 SW 스쿨 4차 프로젝트 공통 저장소(`.github`)입니다. 조직 내 모든 저장소에 기본 적용되는 이슈·PR 양식과 커밋 훅, 협업 규칙을 관리합니다.

## 역할 분담

| 영역 | 담당 | 주요 업무 |
| --- | --- | --- |
| Sales | 송승현 | 지점 발주 요청, 본사 승인·출고, 지점 도착 확인 API |
| Procurement | 백상준 | 공급사 관리, 구매 발주, 본사 입고 API |
| User/Auth | 백상준 | 로그인·인증(JWT), 역할·소속 지점 기준 권한 처리 API |
| Inventory | 차원우 (팀장) | 창고·지점 재고 조회, 재고 차감, 수불 원장 API |
| Item / 알림 | 송승준 | 부품 마스터·단가 관리 API, 알림 서비스(선택 시) |
| 인프라 | 송승준 | Docker Compose, API Gateway, CI/CD |
| 프론트엔드 | 우희정 (PM) | 초기 공통 세팅(레이아웃·사이드바·라우터·Axios) 및 웹 화면 개발 |

## 1. 작업 흐름 (이슈 -> 브랜치 -> PR)

1. 이슈 등록
   작업 전에 GitHub 이슈를 먼저 만들고 제목에 도메인을 적는다.
   - 예: `feat: [Sales] 지점 발주 출고 처리 기능 구현`

2. 브랜치 생성
   `develop` 브랜치에서 이슈 번호를 넣어 작업 브랜치를 만든다. `main`은 검수·발표용 안정 버전만 유지한다.
   - 예: `feat/12-sales-출고처리`, `fix/15-inventory-재고차감`

3. 커밋 메시지
   `말머리: 한글 요약` 형식으로 작성한다. 이슈 번호 표기는 선택이다.
   - 말머리: `feat:`(기능), `fix:`(버그), `refactor:`(정리), `chore:`(설정), `docs:`(문서), `test:`(테스트)
   - 예: `feat: 지점 발주 승인 API 구현 (#12)`

4. PR 및 리뷰
   기능 단위로 나누어 올리고 본문에 `Closes #12`처럼 연결 이슈를 적는다. 리뷰는 가급적 당일 확인하며 최소 1명 승인(Approve) 후 머지한다.
   (이슈 자동 종료는 저장소의 기본 브랜치로 머지할 때 동작하므로, 각 저장소의 기본 브랜치를 `develop`으로 두면 편하다.)

## 2. 이슈 · PR 양식

조직 내 다른 저장소에서 이슈나 PR을 열면 아래 양식이 자동으로 불러와진다.

- 작업 이슈: [`.github/ISSUE_TEMPLATE/feature.md`](.github/ISSUE_TEMPLATE/feature.md) (작업 배경/목적, 할 일 체크리스트)
- 버그 이슈: [`.github/ISSUE_TEMPLATE/bug.md`](.github/ISSUE_TEMPLATE/bug.md) (발생 상황, 원인)
- PR 양식: [`.github/PULL_REQUEST_TEMPLATE.md`](.github/PULL_REQUEST_TEMPLATE.md) (연결 이슈, 변경 내용, 테스트 방법, 환경변수/API 변경 유무)

## 3. 커밋 메시지 훅 설정 (최초 1회)

커밋할 때 메시지가 `말머리: 한글 요약` 규칙에 맞는지 자동으로 확인해 주는 스크립트([`.githooks/commit-msg`](.githooks/commit-msg))다. 규칙에 맞지 않으면 커밋 전에 안내 문구를 띄우고 막아주며, 터미널과 IntelliJ 커밋 창 모두 동일하게 동작한다. (`Merge`, `Revert`, `fixup!`, `squash!` 자동 커밋은 예외 통과)

Git은 보안상 클론한 폴더 안의 스크립트를 자동으로 실행하지 않으므로, 처음 한 번만 아래 순서대로 경로를 등록하면 된다.

### 1단계: 프로젝트 폴더 구성
작업용 상위 폴더(예: `erp`)를 하나 만들고, 그 안에 이 공통 저장소와 본인이 개발할 서비스 저장소들을 나란히 클론한다. (원격 저장소 이름인 `.github` 그대로 클론하면 숨김 폴더가 되므로 `team-conventions` 이름으로 받는 것을 권장한다.)

```sh
mkdir erp && cd erp
git clone https://github.com/Hyundai-Autoever-4th-ERP/.github.git team-conventions
```

### 2단계: 훅 자동 연결 (최초 1회)
`team-conventions` 폴더에서 본인 OS에 맞는 명령어를 그대로 복사해 실행한다.

Mac / Linux (터미널):
```sh
cd team-conventions
git config --file ~/.gitconfig-erp core.hooksPath "$(pwd)/.githooks"
git config --global "includeIf.gitdir:$(cd .. && pwd)/.path" ~/.gitconfig-erp
```

Windows (Git Bash):
```sh
cd team-conventions
git config --file ~/.gitconfig-erp core.hooksPath "$(pwd -W)/.githooks"
git config --global "includeIf.gitdir:$(cd .. && pwd -W)/.path" ~/.gitconfig-erp
```

위 명령을 한 번만 실행해 두면 상위 폴더(`erp/`) 안에 있는 모든 저장소와 앞으로 새로 클론할 저장소에 훅이 자동 적용된다. 상위 폴더 밖의 다른 개인 프로젝트에는 영향을 주지 않는다.

(만약 상위 폴더 전체가 아니라 특정 저장소 하나에만 직접 걸고 싶다면, 해당 서비스 폴더 안에서 `git config --local core.hooksPath "$(cd ../team-conventions/.githooks && pwd)"`를 실행하면 된다.)

### 3단계: 적용 확인
작업할 서비스 저장소 폴더 안에서 아래 명령을 쳤을 때 `.githooks` 경로가 출력되면 정상 연결된 것이다.

```sh
git config --get core.hooksPath
```

## 4. API · MSA 데이터 규칙

- 서비스 간 데이터 접근: 각 서비스는 자기 DB만 조회·수정한다. 다른 서비스 데이터가 필요하면 반드시 API로만 주고받고, 타 서비스 DB 테이블에 직접 접근하거나 JOIN하지 않는다.
- 이름 표기: 프론트와 백엔드가 주고받는 JSON 필드는 `camelCase`(`unitPrice`, `warehouseCode`), DB 테이블·컬럼은 `snake_case`(`unit_price`, `warehouse_code`)로 통일한다.
- 에러 응답: 모든 에러 응답에는 에러 코드와 화면에 표시할 한글 안내 메시지를 함께 내려준다.

| HTTP 상태 | 적용 기준 |
| --- | --- |
| 400 | 잘못된 입력값 |
| 401 | 미인증(로그인 필요, 토큰 만료) |
| 403 | 직급·역할 권한 부족 |
| 404 | 존재하지 않는 데이터 또는 타 지점 데이터 접근 |
| 409 | 재고 부족, 중복 처리 등 현재 상태 충돌 |
