# Git 변경 이력 관리

## Fetch
원격 저장소에서 최신 메타데이터를 정보를 확인합니다.<br/>
fetch는 원격저장소의 변경된 커밋(이력)만 가져옵니다.<br/>
```
git fetch
```

## Pull
원격 저장소의 변경사항을 로컬 저장소에 반영(다운로드) 합니다.
```
git pull
```

## Add
workspace 에서 작업중인 파일을 index(stage)에 추가합니다.
```
git add {pattern}
```

## Commit
index(stage) 에 추가된 파일을 로컬 저장소에 반영합니다.
```
git commit -m {commit message}
```

## Push
로컬 저장소의 변경사항을 원격 저장소에 반영(업로드) 합니다.
```
git push
```

## User Config
Git 커밋에 사용할 사용자 이름과 이메일을 설정합니다.<br/>
커밋에는 사용자 정보가 필요합니다.

깃 호스팅 가입시 이름과 이메일 주소로 설정합니다.
```
git config user.name {user_name}
git config user.email {user_email}
```

## Git 상태

### Staging Area
- Working Directory : 작업하고 있는 공간
- Staging Area : 커밋에 추가되기 위해 대기하는 파일들의 공간
- Repository : Git 메타데이터와 객체 데이터베이스가 저장되는 공간

### git status
Untracked files: 한번도 커밋한 적 없는 새로운 파일입니다.

git diff 으로 변경사항을 비교할때 Untracked files 는 포함되지 않습니다.

Changes not staged for commit: 수정되었지만 git add 로 Staging Area 에 포함되지 않은 파일들 입니다.

Changes to be committed: git add 를 통해 Stage Area 에 추가된 파일들 입니다. 

다음 커밋시 Stage Area 의 파일들이 포함됩니다.

## .gitignore
의도적으로 추적되지 않은 파일을 무시하도록 지정합니다.

Git 이 버전관리를 위해 git add 할때 제외할 파일들을 패턴으로 지정합니다.

단, 이미 Git add 를 수행해서 추적 파일이 되었다면 파일을 삭제할때까지 변경사항이 계속 깃 이력에 추가됩니다.

주로 패키지 관리자 (Maven, Gradle, NPM) 도구로 관리되는 파일이나 빌드시 자동으로 생성되는 *.class 파일, 설정파일(env, config, properties) 등을 제외할때 사용합니다.

- - -

Reference
- [시작하기-Git-기초](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EA%B8%B0%EC%B4%88)
- [gitignore](https://git-scm.com/docs/gitignore)