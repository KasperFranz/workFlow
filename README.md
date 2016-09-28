

ghi label "Action - awaiting feed-back" -c 6EB82C
ghi label "Action - needs grooming"     -c 009800
ghi label "Prio 1 - must have"          -c E83D0F
ghi label "Prio 2 - should have"        -c EB6420
ghi label "Prio 3 - could have"         -c E8850F
ghi label "Prio 4 - won't have"         -c E8A80F
ghi label "Size 0 - Briefing"           -c C7DEF8
ghi label "Size 1 - small"              -c 20B4E5
ghi label "Size 2 - medium"             -c 208FE5
ghi label "Size 3 - large"              -c 0052CC
ghi label "Size 4 - too big"            -c 100B6B
ghi label "Status - duplicate"          -c 111111
ghi label "Status - workable"           -c EDEDED
ghi label "Status - in progress"        -c EDEDED
ghi label "Status - up Next"            -c EEEEEE
ghi label "Type - Bug"                  -c b60205
ghi label "Type - Feature"              -c 5319e7
 

 Git Aliases (.gitconfig) 
```
[alias]
  co = checkout
  ci = commit
  st = status
  br = branch
  hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
  type = cat-file -t
  dump = cat-file -p
  addremove = add -A .
  co = checkout
  purge-all-delivered = "!f() { git co `git default-branch`; git branch | grep 'delivered/' |   sed 's/delivered\\///g' | xargs -I %br sh -c 'git branch -D delivered/%br; git   push origin :%br' 2>/dev/null; }; f "
  default-branch = "!f(){ if git show-ref refs/heads/master >/dev/null 2>&1; then   echo master; elif git show-ref refs/heads/gh-pages >/dev/null 2>&1; then echo   gh-pages; fi; }; f"
  issue-branch = "!f() { MATCH=#$1:; ghi show $1 2>/dev/null | grep ^$MATCH | sed   s/$MATCH/$1/g | sed 's/ /-/g' | sed s/[:\\']//g; }; f"
  issue-wip = "!f() { ghi label $1 'Status - in progress'; }; f"
  work-on	= "!f() { BRANCH=`git issue-branch $1`; git fetch origin; git co $BRANCH   2> /dev/null || git co -b $BRANCH origin/`git default-branch`; git issue-wip $1; ghi assign $1;  }; f"
  wrapup    = "!f() { MSG='close #'`git symbolic-ref --short HEAD | sed 's/-/ /g'`; echo $MSG > ~/WRAPUP_EDITMSG; git addremove; git commit -F ~/WRAPUP_EDITMSG; rm ~/WRAPUP_EDITMSG; }; f"
  deliver   = "!BRANCH=`git symbolic-ref --short HEAD`;REMOTEBRANCH=$BRANCH; MSG='close #'`git symbolic-ref --short HEAD | sed 's/-/ /g'`; git push origin $BRANCH:$REMOTEBRANCH && git branch -m delivered/$BRANCH && hub pull-request -f -h $REMOTEBRANCH -m 'close #'$MSG"

  ```

gems:

```
gem install ghi
ghi update --version
download https://github.com/github/hub/releases og flyt den til rubys bin 
```

.bashrc
```
alias git=hub
```