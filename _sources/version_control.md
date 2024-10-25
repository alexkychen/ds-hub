# Git version control 

Use Git and Github for project version control

## Start with Github repos

1. Create a repo on Github.com
2. Clone a repo on your local machine. Under your project folder, enter
```{code-block} bash
git clone https://github.com/USER_NAME/REPO_NAME.git
```

## Start with project folders 

1. Under your project folder (`-b main` can be left out)
```{code-block} bash
git init -b main
```
2. Add files to your local repo
```{code-block} bash
git add .
```
3. Commit your files 
```{code-block} bash
git commit -m "first commit"
```
4. Link to your local repo to the remote repo on Github (need to create your Github repo and URL first)
```{code-block} bash
git remote add origin https://github.com/USER_NAME/REPO_NAME.git
```
5. To verify the linked remote repo
```{code-block} bash
git remote -v
``` 
6. Push to git repo
```{code-block} bash
git push
```

## Work on a branch
1. Create a new branch on local
```{code-block} bash
git checkout -b [branch_name]
```

```{hint} 
- To check your current branck, use `git branch`.
- To switch between branches, use `git checkout [branch_name]`.

```

2. Edit your files

3. Stage and commit your changes on local
```{code-block} bash
git add .
git commit -m "update branch"
```

4. Push to remote repo first time
```{code-block} bash
git push --set-upstream origin [branch_name] | git push -u origin [branch_name]
```
**Note**: After the new branch is created on remote repo, you can just use `git push`. One can use `-u` to replace `--set-upstream` for short.


5. Create a pull request on Github repo page 

6. Merge changes of one branch to main branch
```{code-block} bash
# first switch to main branch
git checkout main

# perform merging
git merge [branch_name]

# push to remote repo
git push
```

**Note**: Merge action also can be done on Github repo page

