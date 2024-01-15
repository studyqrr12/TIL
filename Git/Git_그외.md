# Git 기타

## git add 를 헀는데 수정사항이 커밋에 추가되지 않는 경우
1. gitignore 에 선언된 파일<br/>
gitignore 에서 제외 설정해야 합니다. `!{pattern}`
2. 기본 설정은 대소문자를 구분하지 않습니다.<br/>
`git config core.ignorecase false` 설정 후 다시 추가해야 합니다.

## gitignore 에 추가했는데 파일이 수정되면 계속 변경상태가 추적되는 경우
1. 이미 git add 로 추가된 적 있다면 tracked files 가 되어 gitignore 에 있더라도 계속 추적됩니다. 삭제 후 다시 커밋하거나 git rm --cached 로 Staging Area 에서 제거합니다.
2. gitignore 파일이 .git 파일이 있는 루트가 아닌 특정 폴더 내부에 있는 경우 해당 위치부터 하위로 적용됩니다.