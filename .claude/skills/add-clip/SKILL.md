---
name: add-clip
description: 유튜브 클립 카드를 index.html 에 추가합니다. 이슈 생성 → feat/#{이슈번호} 브랜치 → 카드 블록 복사·붙여넣기 → 커밋 → 푸시 → PR 까지의 흐름과 충돌 해결을 안내합니다. "클립 추가", "영상 추가", "카드 추가", 유튜브 링크만 주고 "올려줘" 같은 요청에 사용합니다.
---

# 클립 카드 추가

`index.html` 에 유튜브 클립 카드를 **하나 추가**하는 절차입니다.

**기존 카드는 지우지 않습니다.** 카드 블록을 복사해서 목록 아래에 붙이고, 붙인 사본만 고칩니다.
이미 있는 카드를 새 영상으로 바꿔치기하면 다른 팀원의 추천이 사라지고, 나중에 충돌을 해결할 때
"누가 지웠는지" 를 되짚어야 합니다.

## 0. 영상 정보 확인

영상 ID 는 주소에서 뽑습니다. 세 가지 형태 모두 ID 는 같은 11 글자입니다.

```
https://www.youtube.com/watch?v=iwwb-HDfF9I
https://youtu.be/iwwb-HDfF9I?si=...          ← ?si= 뒤는 추적용 꼬리표, 버립니다
https://youtube.com/shorts/iwwb-HDfF9I
                           ~~~~~~~~~~~  ← 이 부분이 영상 ID
```

제목과 채널명이 확실하지 않으면 추측하지 말고 oEmbed 로 확인합니다.

```bash
curl -s 'https://www.youtube.com/oembed?url=https://youtu.be/iwwb-HDfF9I&format=json'
# → {"title":"...","author_name":"...","thumbnail_url":"..."}
```

재생 시간은 oEmbed 에 없습니다. 모르면 `card-channel` 에 채널명만 적고 시간은 비웁니다.
**없는 정보를 지어내지 않습니다.**

## 1. 이슈 생성

브랜치 이름이 이슈 번호에 매달려 있으므로 이슈가 **먼저** 있어야 합니다.

```bash
gh issue list                       # 이미 등록된 이슈인지 먼저 확인
```

해당 이슈가 없으면 `🎬 클립 추가` 템플릿 형식으로 만듭니다.

```bash
gh issue create --title "[클립] Never Gonna Give You Up" --body "$(cat <<'EOF'
### 영상 제목

Never Gonna Give You Up

### 링크

https://youtu.be/dQw4w9WgXcQ

### 추천 이유

링크를 잘못 눌렀을 때 만나게 되는, 인터넷에서 가장 유명한 3분 33초
EOF
)"
```

출력된 URL 끝의 번호가 이슈 번호입니다. **이 번호를 추측하지 않습니다.**
확인할 수 없으면 사용자에게 묻고 멈춥니다.

## 2. 브랜치 생성

```bash
git switch main
git pull --ff-only
git switch -c 'feat/#12'     # 따옴표 필수
```

`#` 는 셸에서 주석 시작이라, 따옴표를 빼면 브랜치 이름이 `feat/` 로 잘립니다.
이슈 하나당 브랜치 하나입니다.

## 3. 카드 블록 복사해서 붙이기

`index.html` 의 카드 그리드에서 아래 두 주석 사이 블록을 **복사**합니다.

```
<!-- ▼▼▼▼▼ 카드 하나 시작 — 이 블록을 복사해서 쓰세요 ▼▼▼▼▼ -->
...
<!-- ▲▲▲▲▲ 카드 하나 끝 ▲▲▲▲▲ -->
```

복사한 것을 목록의 **마지막 카드 아래**(`card-empty` 안내 카드들 앞 또는 그 사이)에 붙이고,
붙인 사본에서 다음을 내 것으로 고칩니다.

| 고칠 곳 | 예시 |
| --- | --- |
| `href` 의 영상 ID | `https://www.youtube.com/watch?v=<영상ID>` |
| `img src` 의 영상 ID | `https://img.youtube.com/vi/<영상ID>/hqdefault.jpg` |
| `img alt` | `Rick Astley - Never Gonna Give You Up 썸네일` |
| `card-title` | 영상 제목 |
| `card-channel` | 채널명 (재생 시간을 알면 `채널명 · 3:33`) |
| `card-summary` | 한두 줄 소개 |
| `card-meta` | `추천 · 내이름` |

**영상 ID 는 두 군데(링크·썸네일)에 들어갑니다.** 한쪽만 바꾸면 썸네일과 실제 영상이 어긋납니다.
바꾼 뒤 확인:

```bash
grep -n '<영상ID>' index.html      # 두 줄이 나와야 합니다
```

안내용 `card-empty` 카드는 자리가 남아 있으면 그대로 둡니다.
`style.css` 는 고치지 않습니다.

## 4. 커밋

```bash
git add index.html
git commit -m "feat: Never Gonna Give You Up 클립 추가"
```

- 클립 추가는 `feat`, 깨진 링크·오타 수정은 `fix` 입니다
- 설명은 명령형, 마침표 없이 (`클립 추가` O / `클립을 추가했습니다.` X)
- 한 커밋에 한 가지만

## 5. 푸시하고 PR

```bash
git push -u origin 'feat/#12'
gh pr create --base main
```

- 대상 브랜치는 항상 `main`
- `.github/PULL_REQUEST_TEMPLATE.md` 를 채우고, 본문 `Closes #` 뒤에 **이슈 번호를 적습니다**.
  비워 두면 머지돼도 이슈가 닫히지 않습니다
- 리뷰 승인 전에 직접 머지하지 않습니다

## 충돌이 났을 때

팀원 모두가 같은 파일의 같은 자리를 고칩니다. **충돌은 정상입니다.**
**먼저 머지된 쪽이 우선이고, 뒤에 오는 PR 이 직접 해결합니다.**

```bash
git switch 'feat/#12'       # main 이 아니라 내 브랜치에서
git fetch origin
git merge origin/main       # 여기서 충돌
# index.html 정리 후
git add index.html
git commit
git push
```

```html
<<<<<<< HEAD
  ... 내 카드 ...            ← 내 브랜치
=======
  ... 먼저 머지된 카드 ...    ← main (이미 머지된 쪽)
>>>>>>> origin/main
```

- **어느 쪽도 지우지 않습니다.** 두 카드가 모두 남아야 합니다
- `main` 쪽 카드를 위에, 내 카드를 아래에 두고 마커 세 줄(`<<<<<<<`, `=======`, `>>>>>>>`)만 지웁니다
- `rebase` 는 쓰지 않습니다. 이 저장소는 `merge` 만 씁니다
- 정리한 뒤 `index.html` 을 브라우저로 열어 카드가 모두 보이는지 확인합니다

## 체크리스트

- [ ] 이슈가 실제로 존재하고 번호를 확인했다
- [ ] `git branch --show-current` 가 `main` 이 아니다
- [ ] 기존 카드를 지우거나 옮기지 않았다
- [ ] 영상 ID 를 링크·썸네일 두 군데 다 바꿨다
- [ ] `style.css` 를 건드리지 않았다
- [ ] PR 본문 `Closes #` 에 이슈 번호를 적었다
