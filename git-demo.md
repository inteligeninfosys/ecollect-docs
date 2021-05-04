# Git demo steps to merge

#### Git clone master branch
```
$ git clone https://github.com/inteligeninfosys/ecollect10
```

#### Start your feature branch
```
$ git checkout -b new-branch main
```
#### Edit/add some files
```
$ git add .
$ git commit -m "Start a feature"
```
#### Edit/add more files
```
$ git add .
$ git commit -m "Finish a feature"
$ git push origin new-branch
```
#### Merge in the new-feature branch
```
$ git checkout main
$ git merge new-branch
```
#### To delete your feature banch
```
delete local branch
$ git branch -d new-feature
delete remote granch
$ git push origin --delete new-branch
```
### Useful git cmds
```
To see local branches, run this command: git branch.
To see remote branches, run this command: git branch -r.
To see all local and remote branches, run this command: git branch -a.
```
