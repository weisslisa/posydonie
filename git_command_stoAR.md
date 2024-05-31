# Git command for CROCO master branch dev_2024_stoAR

* Add the new or modified files to the Git staging area
```
cd ./CROCO/croco
git branch   ## Ensure you are on the correct branch dev_2024_stoAR
git add .    
git status   ## to see which files will be committed
```
* If you don't want to commit the changes to a file
```
git restore --staged file.bash
git status
```
* Commit and Push
```
git commit -m "Added STOGEN directory and scripts"
```

* to configure the name and email associated with the commit 
```
git config --global --edit
git commit --amend --reset-author
```
