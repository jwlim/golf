# 골프 기록

폰에서 홀별로 입력하고 Google Sheets에 저장하는 개인 골프 기록 페이지입니다.

- `index.html`: 화면 전체 (GitHub Pages에 올리는 파일)
- `Code.gs`: Google Sheets에 붙이는 Apps Script (읽기·쓰기 담당)

`index.html`만 올려도 동작하지만, 그 상태에서는 기록이 그 기기의 브라우저에만 저장됩니다.

## 1. Google Sheet 준비

1. Google Sheets에서 새 스프레드시트를 만듭니다. 탭(`courses`, `rounds`, `holes`)은 처음 실행 때 자동으로 생깁니다.
2. 메뉴 **확장 프로그램 > Apps Script**를 열고, 기본 코드를 지운 뒤 `Code.gs` 내용을 붙여넣습니다.
3. 맨 위 `const KEY = 'CHANGE-ME';`의 값을 본인만 아는 문자열로 바꾸고 저장합니다.
4. **배포 > 새 배포**에서 유형을 **웹 앱**으로 고르고, 실행 계정은 **나**, 액세스 권한은 **모든 사용자**로 설정해 배포합니다. 처음에는 권한 승인 창이 뜹니다.
5. 표시되는 웹 앱 URL(`https://script.google.com/macros/s/…/exec`)을 복사합니다.

`Code.gs`를 나중에 고치면 **배포 > 배포 관리**에서 새 버전으로 다시 배포해야 반영됩니다.

## 2. GitHub Pages에 올리기

1. `index.html`의 `const API_URL = '';`에 위에서 복사한 URL을 넣습니다.
2. GitHub에서 공개 저장소를 만들고 `index.html`을 올립니다.
3. 저장소 **Settings > Pages**에서 Source를 **Deploy from a branch**, 브랜치를 `main` / `(root)`로 지정합니다.
4. 잠시 뒤 `https://<아이디>.github.io/<저장소 이름>/`으로 접속합니다. 폰 브라우저의 "홈 화면에 추가"로 앱처럼 쓸 수 있습니다.

## 3. 사용

- 첫 라운드를 저장할 때 비밀키를 한 번 묻습니다. `Code.gs`의 `KEY`와 같은 값을 넣으면 그 기기에 기억됩니다.
- 입력 중인 라운드는 홀을 넘길 때마다 폰에 저장되고, 라운드를 마치면 시트로 보냅니다. 전송에 실패하면 첫 화면에 "다시 보내기"가 나타납니다.
- 처음 가는 코스는 홀마다 파를 함께 입력합니다. 9홀을 모두 입력한 코스는 `courses` 탭에 저장되어 다음부터 파가 채워집니다.
- 저장된 파를 라운드 중에 바꾸면 확인을 한 번 묻고, 라운드를 저장할 때 `courses` 탭도 갱신합니다.

## 시트 구조

| 탭 | 열 |
|---|---|
| `courses` | club, course, p1 … p9 (9홀 코스 단위) |
| `rounds` | id(날짜 YYMMDD, 예: 261115. 같은 날 두 번째부터 261115b, c…), date, club, front, back + 요약 열(색 배경): holes, score, par, diff, putts, fw_hit, fw_n, gir, penalty |
| `holes` | round_id, hole, par, strokes, putts, fairway(hit/left/right), penalty |

통계와 `rounds` 탭의 요약 열은 모두 `holes` 탭에서 계산하므로, 잘못 입력한 값은 앱의 수정 버튼이나 시트에서 고치면 됩니다. 라운드를 지우려면 `rounds`와 `holes`에서 해당 id의 행을 지웁니다.

- 페어웨이 안착률: 파4·파5 중 페어웨이를 기록한 홀 기준
- GIR: 타수 − 퍼트 ≤ 파 − 2 인 홀
- 평균과 추이: 18홀을 모두 입력한 라운드만 포함
