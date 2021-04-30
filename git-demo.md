# Git demo steps to merge

#### Git clone master branch
```
$ git clone https://github.com/amaroko/ecollect10
```

#### Start your feature branch
```
$ git checkout -b new-feature master
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
$ git checkout master
$ git merge new-feature
$ git branch -d new-feature
```
#### To delete your feauture banch
```
$ git -d new-feature
```

