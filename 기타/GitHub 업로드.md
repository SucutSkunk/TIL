# TIL: VS Code에서 GitHub에 바로 업로드하는 법

VS Code의 Source Control 기능만으로 로컬 프로젝트를 GitHub에 올릴 수 있다. 핵심은 **Stage → Commit → Publish** 세 단계이고, 올리기 전에 `.gitignore`로 제외할 파일을 반드시 정리해야 한다.

## 1. 올리기 전에: `.gitignore` 점검

Public 저장소가 될 수 있으므로 아래 파일은 올라가지 않게 막아야 한다.

| 대상 | 이유 |
| --- | --- |
| `.env` | API 키 등 민감 정보 유출 위험 |
| `.venv/` | 가상환경이라 용량이 크고 불필요 |
| 데이터셋 이미지, zip | 용량이 크고 원본 이미지의 재배포 조건이 있음 |

```ignore
.env
.venv
fashion_dataset.zip
fashion_dataset/images/
```

- `.gitignore`를 저장하면 Source Control의 변경 파일 수가 크게 줄어든다. (이번에는 `10K` → `16`개)
- 이미 `git add`를 했던 파일은 `.gitignore`만으로 제외되지 않는다. 이 경우 `git rm -r --cached <경로>`로 스테이징을 풀어야 한다.
- `.env` 대신 `.env.example`(키 값 없이 형식만 담은 파일)을 올리면 다른 사람이 설정 방법을 알 수 있다.

## 2. Stage와 Commit

1. 왼쪽의 **Source Control** 아이콘을 누른다. (`Ctrl+Shift+G`)
2. `Changes` 줄에 마우스를 올리면 나타나는 **`+` (Stage All Changes)** 를 누른다.
3. `Message` 입력창에 커밋 메시지를 적고 **Commit**을 누른다.

### 막혔던 부분: `COMMIT_EDITMSG` 탭

- `Message` 입력창을 비워 둔 채 Commit을 누르면 `COMMIT_EDITMSG`라는 편집 탭이 열리고, 커밋이 끝나지 않아 로딩이 계속 돈다.
- **1번째 줄**에 커밋 메시지를 쓰고 → `Ctrl+S`로 저장 → 탭을 닫으면 커밋이 완료된다.
- `#`로 시작하는 줄은 Git이 자동으로 무시하므로 지우거나 주석 처리할 필요가 없다.
- 단, 내 메시지 맨 앞에 `#`를 붙이면 그 줄도 주석이 되어 **빈 메시지로 커밋이 취소**된다.
- 처음부터 `Message` 입력창에 메시지를 적고 Commit하면 이 탭은 열리지 않는다.

## 3. 브랜치 이름 바꾸기

기본 브랜치가 `master`로 만들어져 있어서 `main`으로 바꿨다.

```bash
git branch -M main
```

## 4. GitHub에 Publish

1. 커밋 후 파란색 **Publish Branch** 버튼을 누른다.
2. 상단 입력창에서 저장소 이름을 정한다. (기본값은 폴더 이름)
3. **private / public** 중 하나를 고른다.
4. GitHub 로그인 창이 뜨면 브라우저에서 인증을 허용한다.
5. `Successfully published ...` 알림이 뜨면 완료. **Open on GitHub**으로 확인한다.

> Stage와 Commit은 **내 컴퓨터(로컬)** 에만 기록된다. Publish(Push)를 해야 비로소 GitHub에 올라간다.

## 5. private vs public

| 구분 | 내용 |
| --- | --- |
| private | 본인과 초대한 사람만 볼 수 있음 |
| public | 인터넷에서 누구나 볼 수 있음 |

- 보통은 **private으로 먼저 올리고, 제출 직전에 public으로 전환**한다.
- 한 번 public으로 공개되면 완전히 되돌리기 어려우므로, 올리기 전에 파일 목록을 꼭 확인한다.

## 6. 이후 작업 흐름

```text
파일 수정 → Stage(+) → Message 입력 후 Commit → Sync Changes (Push)
```

----------------------
