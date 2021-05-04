# Git demo steps to merge

#### Git clone master branch
```
$ git clone https://github.com/inteligeninfosys/ecollect10
```

#### Start your feature branch
```
$ git checkout -b new-feature main
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
```
#### Merge in the new-feature branch
```
$ git checkout main
$ git merge new-feature
```
#### To delete your feauture banch
```
$ git branch -d new-feature
```

