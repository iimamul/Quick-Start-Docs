
# gitignore added after project(.Net) pushed to github

If someone created a project without adding gitignore in vs code/command line and pushed it to GitHub. After that, if they notice some unwanted files have been pushed to the repository and then they add gitignore. Although gitignore is added when it is pushed to GitHub previous unwanted files will be still there. It requires removing the git cache to remove those files from git as described following process.


## Add gitignore file to dotnet projects

From .Net core 3 SDK, this command has included adding gitignore file to the .Net projects.

```bash
dotnet new gitignore
```
    
    
## Remove item from git cache/index

The following commands below will remove all of the items from the Git index/Cache but not from the working directory or local repository, and then will update the Git index/Cache, while respecting Git ignores.

```bash
git rm -r --cached .
git add .

git commit -am "Remove ignored files"
```
Above commands can be written in one line.

 ```bash
git rm -r --cached . && git add . && git commit -am "Remove ignored files"
```
## Acknowledgements

 - [Rafal Pienkowski blog for adding gitignore](https://dev.to/rafalpienkowski/easy-to-create-gitignore-for-the-dotnet-developers-1h42)
 - [Matt Frear answer about git cache remove](https://stackoverflow.com/questions/1274057/how-do-i-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore)
 
