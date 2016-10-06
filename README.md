This is the workflow we are trying to use/build - feel free to make pull requests if there is something you want to change, a workflow you want to describe etc.


### Table of Contents
 * [Setup](#setup)
 * [Git Aliases](#git-aliases-gitconfig)
 * [Workflow](#workflow)
   * [Working on Project xx](#working-on-project-xx)
   * [Cleaning up your branches](#cleaning-up-your-branches)
   * [Setup of a new project on GitHub](#setup-of-a-new-project-on-github)
 * [Ressources](#ressources)

#Setup 
 To setup this workflow, we first need to install the gem *GHI* the command `gem install ghi` can/should be used for this.

 After it is installed we need to set it up - this is done with the command `ghi config --auth <username>` when it is done correctly it will add a token to your gitconfig.

 Once GHI is installed you need to install hub - this can be downloaded as a binary at [hub github release](https://github.com/github/hub/releases) The easiest is to place this exe (from the bin folder in the zip) in your ruby bin folder, as this is already in your PATH.

 Once hub is installed (you can test it is working by using the command `hub`) we need to make it a alias of git - this is done with using the command `alias git=hub`

 Now all the requirements for the setup is done, now to the important part, which is our aliases copy the aliases from the section Git Aliases and place them into your .gitconfig  (it is in  your home folder).

 You are now all set :smile: - now read through the Workflow section for some of the most used workflows. 

#Git Aliases (.gitconfig) 
 These are our aliases which are placed after a [alias] in your .gitconfig folder.
```
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
  issues = "!f(){ ghi list -L 'Status - workable'; }; f"

```

#Workflow
These are some of the more normal workflows and some of the ones you might end up needing.

##Working on project xx
 Working on issue xx would normally mean you start by looking at the issue on the issue tracker, and finding out which ones you should start on, this should be done 100% through the command line.

 You first find the issue you should work on by doing `git issues`, if you are in a git repository while doing this it shows the issues there are workable in this repository, elseit will show you the ones overall assigned to you.

 When you have found a issue you should work on go to that repository (using `cd`)  and issue the command `git work-on [ID] ` (the ID is the first part of each line of the result from `git issues`).

 This will change your branch to a branch specific to work on this.

 Once you have finished your work on this issue you should wrap the code up - this is done with `git wrapup`  (it already knows what issue you want to close) - this commits your changes.

 When you are all done with this issue you should deliver it for review, this is done with `git deliver`, this also changes your branch to a called deliverd/ this is so you easily can see that you are done with this issue.

##Cleaning up your branches
 When you have worked on issues for some time you get alot of delivered/ branches, these can be cleared with the command `git purge-all-delivered` which deletes the branches starting with delivered/ on your local repository.


##Setup of a new project on GitHub
 First we need to clear the old labels and set our labels to be assigned - this can be done with the following commands 

 ```
  ghi label -l | sed "s/\'/\\\'/g" | xargs -I %lb sh -c 'ghi label -D "%lb"'
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
 ```

 Once this is changed then we should begin to setup waffle

#Ressources
 We are to this setup using the following projects:
 - [GHI](https://github.com/stephencelis/ghi) - We use this for a easy way to show the issues on the github project
 - [Hub](https://github.com/github/hub) - This is used to make pull requests etc.
 - [Praqma workflow](http://www.praqma.com/stories/a-pragmatic-workflow/) - The inspiration for this project and 
