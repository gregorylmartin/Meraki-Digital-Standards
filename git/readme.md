#Notes

## Remove Git from Folder
```
rm -rf .git
```

## Create New Repo

1. Create New Private Repo in GitHub. (https://help.github.com/articles/create-a-repo)
2. Create README file in directory on laptop (in the source directory):
```
touch readme.md
```
3. Initialize Git:
```
git init
```
4. Add all files in source directory to Git for first time:
```
git add .
```
5. Commit files to the local Repo:
```
git commit -m ‘First commit’
```
6. Create “remote” named “origin” that points at your GitHub repository:
```
git remote add origin https://github.com/gregorylmartin/my-git-name.git
```
7. Push your new repo to GitHub:
```
git push origin master
```
