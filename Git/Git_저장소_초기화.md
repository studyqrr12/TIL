# Git 저장소 초기화

Git 을 사용한 버전관리를 위해서는 현재 디렉터리 (또는 상위 디렉터리) 에 .git 파일이 있어야 합니다.

이미 원격 저장소에 프로젝트가 있다면 원격 저장소에서 클론 받으면 .git 파일이 생성됩니다.

하지만 원격 저장소에 없는 신규 프로젝트라면 초기화(init)를 통해 .git 파일을 생성할 수 있습니다.

## Clone
원격 저장소를 현재 디렉터리에 내려받습니다.<br/>
이미 생성된 Git 저장소를 내려받기 때문에 git init 이 필요하지 않습니다.
```
git clone {remote_url}
```

## Init
현재 디렉터리에 Git 저장소를 초기화 합니다. (.git 생성)
```
git init
```

init, clone 차이점은 git clone 은 이미 있는 저장소를 내려받기 때문에 이력 파일이 존재하고 원격 저장소 주소를 참조하게 됩니다.

git init 은 신규로 초기화한 저장소이기 때문에 아직 이력 파일이 존재하지 않고 참조하는 원격 저장소가 없기 때문에 push 를 위해서는 원격 저장소 주소를 추가해야 합니다.

## Remote add
원격 저장소를 추가합니다.<br/>
원격저장소 별칭으로 origin 을 주로 사용합니다.
```
git remote add {remote_alias} {remote_url}
```

Reference
- [git-clone](https://git-scm.com/docs/git-clone)
- [git-init](https://git-scm.com/docs/git-init)
- [git-remote](https://git-scm.com/docs/git-remote)