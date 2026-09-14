# 진선 커피타임 — 최초 설정

`index.html`의 Firebase 프로젝트는 첨부 파일의 **jscoffee**로 연결해 두었습니다.
이번 한 번 아래 설정과 GitHub 파일 교체를 마치면, 이후에는 앱에서 PDF를 올리거나 이름·회의를 수정하는 즉시 공용 DB에 저장됩니다. 인터넷에 연결된 다른 이용자에게는 새로고침 없이 반영됩니다.

**HTML만 교체하는 것으로 Firebase 관리자 계정이나 보안 규칙이 만들어지지는 않습니다.** 이 설정은 프로젝트 소유자가 Firebase 콘솔에서 진행해야 합니다.

## 1. 관리자 계정 만들기

1. [Firebase 콘솔](https://console.firebase.google.com/project/jscoffee/overview)에서 `jscoffee`를 엽니다.
2. **Authentication → 로그인 방법(Sign-in method)**에서 **이메일/비밀번호**를 사용 설정합니다.
3. **사용자(Users) → 사용자 추가**에서 계정을 만듭니다.
   - 이메일: `admin@jscoffee.app`
   - 비밀번호: 이전에 지정하신 관리자 비밀번호
4. 만들어진 사용자의 **UID**를 복사합니다.

앱의 관리자 로그인에는 이 이메일을 기본 입력해 두었습니다. 기존 관리자 이메일을 사용하려면 로그인 창의 이메일을 바꾸면 됩니다. 기본값을 바꾸려면 HTML에서 `ADMIN_EMAIL`을 검색하세요. 이메일 인증 메일을 보내는 기능은 사용하지 않습니다.

## 2. Firestore와 관리자 권한 설정

1. **Firestore Database**를 엽니다. DB가 없다면 **데이터베이스 만들기 → Standard → `(default)` → 프로덕션 모드**로 생성합니다. 이미 있으면 그대로 사용합니다.
2. **데이터(Data) → 컬렉션 시작**에서 아래 문서를 만듭니다.

| 항목 | 입력값 |
| --- | --- |
| 컬렉션 ID | `admins` |
| 문서 ID | 1단계에서 복사한 관리자 UID |
| 필드 이름 | `enabled` |
| 필드 유형 | **boolean** |
| 필드 값 | **true** |

`true`를 문자열(string)로 입력하면 관리자 권한이 부여되지 않습니다.

## 3. 보안 규칙 적용

**Firestore Database → 규칙(Rules)**에 함께 제공한 **firestore.rules 전체 내용**을 붙여 넣고 **게시(Publish)**합니다.

이 규칙은 방문자의 시간표 읽기를 허용하고, `admins/{UID}`에 `enabled: true`로 등록된 계정만 변경할 수 있게 합니다. 화면에서 자물쇠를 임의로 해제하더라도 DB 쓰기 권한은 생기지 않습니다.

기존 프로젝트에서 다른 앱도 Firestore를 사용한다면, 해당 앱 규칙은 유지하고 이 파일의 `school/current` 및 `admins` 규칙을 통합하세요. 모든 문서의 쓰기를 허용하는 기존 테스트 규칙은 제거해야 합니다. 허용 규칙이 겹치면 더 넓게 허용한 규칙도 적용됩니다.

## 4. GitHub 파일 교체와 최초 등록

1. GitHub Pages에서 쓰는 파일을 새 `index.html`로 교체합니다.
2. 배포가 끝나면 사이트를 열고 **관리자 로그인**을 누릅니다.
3. 1단계 계정으로 로그인한 뒤 **첨부 시간표로 공용 DB 시작**을 누릅니다.
4. 기존 자료 **교사 68명 · 학반 33개 · 직책 및 담임배정표**가 공용 DB에 최초 등록됩니다.

이미 공용 DB가 등록되어 있으면 최초 등록으로 덮어쓰지 않습니다. 이후 시간표를 바꾸려면 **시간표 PDF 교체**를 사용하세요.

## 실전 확인

1. 일반 창과 시크릿 창에서 같은 사이트를 엽니다.
2. 일반 창에서 관리자 로그인 후 부장회의 시간을 하나 추가합니다.
3. 시크릿 창의 **부장회의** 탭에도 바로 나타나는지 확인합니다.
4. 테스트 회의 옆 `×`를 누르면 다른 창에서도 사라지는지 확인합니다.

현재 작업 환경에서는 실제 Firebase 프로젝트 설정을 변경하지 않았습니다. 콘솔 설정 후 위 확인으로 최종 연결 상태를 점검해 주세요.

## 이후 관리 방법

- PDF를 올리면 **추출한 시간표 데이터**가 공용 DB에서 전체 교체됩니다. 원본 PDF 파일 자체는 별도로 보관하지 않습니다.
- 교사별/학반별 PDF를 함께 선택하면 둘 다 읽고 검증한 다음 한 번에 교체합니다. 잘못된 PDF나 저장 오류가 나면 기존 데이터는 유지됩니다.
- 교사별 PDF만 선택하면 교사 데이터 전체를, 학반별 PDF만 선택하면 학반 데이터 전체를 교체합니다. 선택하지 않은 종류는 유지합니다.
- 배정표는 미리보기에서 이름을 확인한 뒤 전체 교체합니다. 공백·중간점이 섞인 직책명과 표 위치를 기준으로 읽는 기존 개선 사항을 유지했습니다.
- 직책·담임 이름은 입력을 멈춘 뒤 약 0.7초 후 자동 저장됩니다. 회의 추가·삭제도 공용 DB에 반영됩니다.
- 동시에 편집한 자료로 다른 관리자의 변경을 덮어쓰지 않도록 버전을 확인합니다. 충돌 안내가 나오면 **입력 취소하고 최신 자료 불러오기**를 누른 뒤 다시 수정하세요.
- 네트워크가 끊기면 마지막으로 받은 자료임을 표시합니다. 저장이 확인되기 전에는 저장 성공으로 표시하지 않습니다.
- 관리자 잠금 해제는 현재 창에서만 유지됩니다. 새로고침하면 다시 로그인합니다.
- HTML 안의 `initial-data`는 최초 등록용입니다. 실제 운영 데이터는 Firestore의 `school/current`에 있습니다. 매번 HTML을 다시 올릴 필요가 없습니다.

## GitHub 연필 버튼으로 수정하기

앱 코드는 압축하지 않았습니다. GitHub에서 `index.html` → 연필 버튼을 누르고 아래 이름으로 검색하면 수정 위치를 찾을 수 있습니다.

| 검색어 | 수정 내용 |
| --- | --- |
| `<title>` / `class="brand"` | 진선 커피타임 이름 |
| `id="usage-guide"` | 첫 사용 안내 |
| `class="schedule-alert"` | 시간표 변동 확인 문구 |
| `const FIREBASE_CONFIG` 또는 `var FIREBASE_CONFIG` | Firebase 프로젝트 설정 |
| `ADMIN_EMAIL` | 관리자 이메일 기본값 |
| `TIMES` | 교시 시간 |
| `ROLE_NAMES` | 직책 이름 |
| `createSharedStore` | 공용 저장과 전체 교체 |
| `parseAssignmentPages` | 배정표 PDF 인식 |

Firebase와 PDF.js는 버전을 고정한 외부 모듈을 읽습니다. 별도 빌드나 설치 없이 GitHub Pages에서 실행하지만 인터넷 연결이 필요합니다. 관리자 비밀번호는 HTML에 저장하지 않습니다.

## 참고한 공식 문서

- [Firestore 실시간 업데이트](https://firebase.google.com/docs/firestore/query-data/listen)
- [Firestore 트랜잭션](https://firebase.google.com/docs/firestore/manage-data/transactions)
- [이메일·비밀번호 인증](https://firebase.google.com/docs/auth/web/password-auth)
- [보안 규칙과 인증](https://firebase.google.com/docs/firestore/security/rules-conditions)
