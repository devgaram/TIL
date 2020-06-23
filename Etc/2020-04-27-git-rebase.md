# git rebase

Date: Apr 27, 2020

- Title : git rebase
- Date : 2020-04-27
- Category : Git

## git rebase 하기

```bash
git checkout develop

git fetch origin develop

git checkout feature

git rebase origin/develop

// conflict 발생 시 충돌 해결 후
git add <수정된 파일 이름>

git rebase --continue
```

- git fetch origin develop을 하면 로컬의 origin/develop 에 원격저장소 develop의 최신 커밋들이 싱크된다. 그래서 rebase 대상이 develop이 아니라 origin/develop 이다.
- 충돌 해결 후 파일을 commit 이 아니라 **꼭 add만 해야 한다.**

## git rebase 취소

이미 remote 까지 push 했을 때 rebase 취소하는 방법

```bash
git reflog <브랜치명>

// 돌아가고 싶은 커밋 확인
git reset --hard <128e6d4>

git push -f origin <브랜치명>
```

## 생각하기

### `git rebase origin/develop` 과 `git rebase develop` 의 차이점

### `git fetch origin develop` 과 `git pull origin develop` 의 차이점

pull 은 내부적으로 `fetch + merge` 를 실행한 것과 같다. 즉 pull 을 실행하면 원격 저장소의 내용을 가져오고(fetch) 병합작업(merge)를 수행하게 된다. 만약 단순히 원격 저장소의 내용을 확인만 하고 로컬 데이터와 병합은 하고 싶지 않다면 fetch를 수행하면 된다. fetch를 하게되면 최신 커밋들은 로컬에 `FETCH_HEAD` 라는 이름으로 가져오게 된다. 이 상태에서 원격 저장소의 내용을 로컬 저장소에 통합하고 싶다면 `FETCH_HEAD` 브랜치를 merge 하거나 pull 하면 된다.
