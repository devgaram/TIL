# Git cherry-pick으로 필요한 커밋만 골라내기

Date: Jun 17, 2020

- Title : Git cherry-pick으로 필요한 커밋만 골라내기
- Date : 2020-03-17
- Category : Git

# Git Cherry-pick

현재 상황

- feature/cherry에 작업한 결과를 Pull Request 보낸 상태
- 브랜치가 꼬여서 해당 브랜치에서 작업한 커밋 외의 것도 보이는 상태

원하는 결과

- feature/cherry 에서 원하는 커밋만 남긴 후 다시 푸시 하고 싶음(PR 재 요청X)

## 실습

### feature/cherry 브랜치 로그 확인

```bash
git log
```

`결과 예시: commit 4564415dfbdsfgsdgsd (feature/cherry)` 

### 커밋들을 저장할 임시 브랜치 생성(base branch에서 새 브랜치 생성)

```bash
git checkout -b feature/cherry-correct
```

### cherry-pick 실행

```bash
git cherry-pick 4564415dfbdsfgsdgsd(가져 가고 싶은 커밋넘버)
```

### feature/cherry 브랜치 reset

```bash
git checkout feature/cherry
git reset feature/cherry-correct --hard
git push origin feature/cherry -f
```

`--hard` 옵션이 없을 경우 기존 브랜치에서 작업한 내용이 `unstaged`된 채 남아 있으니 필요한 경우가 아니라면 옵션을 추가 하는 것이 좋다.

참고

[https://medium.com/react-native-seoul/git-cherry-pick-사용법-fe1a3346bd27](https://medium.com/react-native-seoul/git-cherry-pick-%EC%82%AC%EC%9A%A9%EB%B2%95-fe1a3346bd27)
