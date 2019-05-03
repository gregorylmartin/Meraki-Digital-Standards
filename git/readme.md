# Notes

## Remove Git from Folder
```
rm -rf .git
```
## Attach Username and Password to Git Repo

```
git remote set-url origin https://user-here:password-here@github.com/user-goes-here/repo-name-here.git
```


## Create New Repo

1. Create New Private Repo in GitHub. (https://help.github.com/articles/create-a-repo)
2. Create README file in directory on laptop (in the source directory):
3. Initialize Git:
4. Add all files in source directory to Git for first time:
5. Commit files to the local Repo:
6. Create “remote” named “origin” that points at your GitHub repository:
7. Push your new repo to GitHub:

In local directory...
```
touch readme.md
git init
git add .
git commit -m ‘First commit’
git remote add origin https://github.com/gregorylmartin/my-git-name.git
git push origin master

git init; git add .; git commit -m ‘First commit’; git remote add origin https://github.com/gregorylmartin/my-git-name.git; git push origin master;

```
