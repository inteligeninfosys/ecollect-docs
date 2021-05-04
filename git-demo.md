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
#### Merge changes into master branch
Create a new pull request\
Enter brief details about the pull request and click Create pull request. You can scroll down and see a diff of the files that were changed as well as the commits.

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/git_pull_request.png?raw=true)

#### Accept pull requests
To accept the pull request, click the Pull Requests tab to see a summary of pending pull requests. If you are happy with the changes, click Merge Pull request to accept the pull request and perform the merge. You can add in a comment if you want.

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/git_accept_pull_request.png?raw=true)

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

Downloading & Integrating with git pull
$ git pull origin master
```
