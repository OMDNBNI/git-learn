# Git Basics

## Prepare your workspace

```bash
mkdir ~/git-workshop
cd ~/git-workshop

# Making a dummy old project
mkdir project_prev
cd project_prev
echo "# Old Project" > README.md;
echo "We'll need to add some version control into this project" >> README.md
cd ..
```

## Initialization

```bash
# Making a bare repo
git init --bare project_bare
cd project_bare
ls -lsa
git status
cd ..

# Making a new work repo
git init project_work
cd project_work
ls -lsa
git status
ls -lsa .git
cd ..

# Initializing work repo on an old project
cd project_prev
git init .
ls -lsa
git status
ls -lsa .git

# Removing the repository
rm -rf .git
ls -lsa
git status
git init .
git status
ls -lsa
cd ..
```

## Configuration

```bash
cd project_work

git config [--system|--global|--local]
#git config [--system|--global|--local] --list --show-origin #(>=v2.8)
```

| argument | location | semantics |
|:---------|:-------- |:--------- |
| --system | `/etc/gitconfig` | System wide default configuration
| --global | `~/.gitconfig`   | User configuration.<br/>*includes & overrides the `--system` config*
| --local  | `./.git/config`  | Repo configuration.<br/>*includes & overrides the `--global` config*

```bash
git config --list
sudo git config --system core.editor nano
git config user.name "Тестер Тестић"
git config user.email "tester.testic@example.com"
git config --list --system
git config --list --global
git config --list --local

git config --edit
git config --list
```

## Inspecting the repo

```bash
git status; # Current repo state status

git log; # Repo state modification dependency graph

git reflog; # Repo state modification history
```

## CRUD Operations

### Creating content

```bash
echo "file_new.txt line 00" > file_new.txt;
echo "file_rem.log line 00" > file_rem.log;
echo "file_del.txt line 00" > file_del.txt;
git status;
```

### Staging changes

```bash
git add *.txt
git status

git add .
git status
```

### Un-staging changes

```bash
git rm --cached file_rem.log
git status
cat file_rem.log # Content still here
```

### Committing changes

```bash
git commit -m "Commit #01"

git status
git log
git log --summary
git log --format=raw
```

### Ignoring content

```bash
git status

echo "*.log" > .gitignore
git status

git add .gitignore
git commit -m "Commit #02"
git status
git log --summary
```

### Modifying content

```bash
echo "file_new.txt line 01" >> file_new.txt
git status

git commit              # Oups! Nothing staged
git add file_new.txt
git status

git commit -m "Commit #03"
git status
git log --summary

echo "file_new.txt line 02" >> file_new.txt
git commit -a -m "Commit #04"               # ⬅ Note the "-a" argument
git status
git log --summary

echo "file_del.txt line 01" >> file_del.txt
git commit -a -m "Commit #05"
git status
git log
```

### Simplifying your life with aliases

```bash
git log --decorate
git log --decorate --graph
git log --decorate --graph --oneline
git log --decorate --graph --oneline --all

git config --local alias.glog "log --decorate --graph --oneline --all"

# Some other more complex variations on the subject
git log --decorate --graph --all --color --date=format:"%F %T" --pretty=format:"%C(auto)%h%C(reset) [%C(auto)%ad%C(reset) ~ %C(ul)%ar%C(reset)] %C(reverse)%an%C(reset) | %C(bold)%s%C(reset) %C(auto)%d%C(reset)"

# ~/.bashrc | ~/.bash_aliases
alias ll="ls -lhF  --group-directories-first"
alias la="ls -lhFA --group-directories-first"
```

### Undoing file deletion

```bash
cat file_del.txt
rm file_del.txt
git status
ls -lsa

# Restore from the repo
git checkout HEAD file_del.txt
git status
cat file_del.txt

# Actually delete a file
git rm file_del.txt
git status
ls -lsa
git commit -a -m "Commit #06"
git status
git glog

# Restore the same way
git checkout @ file_del.txt     # Oups. There's nothing there
git glog                        # Copy the previous commit ID and paste it
git checkout <CommitID> file_del.txt   # That's better we'll talk more about this
git status
ls -lsa
cat file_del.txt
git commit -m "Commit #7"       # ⬅ The number padding error is intentional
git status
git glog
```

### Amending commits

```bash
git status
git glog

echo "file_new.txt line 03" >> file_new.txt
echo "file_dep.txt line 00" >> file_dip.txt
git commit -a --amend -m "Some new comment"
git status
git glog

git add .
git commit --amend --no-edit
git status
git glog

git commit --amend -m "Commit #07"
git status
git glog
```

### Renaming|Moving files

```bash
git glog --summary
mv file_dip.txt file_dep.txt
git status
git add .
git status

git reset HEAD --hard         # ⬅ To be explained later

git mv file_dip.txt file_dep.txt
git status
git commit -a --amend --no-edit
git status
git glog --summary
```

### Viewing content differences

```bash
git diff
git difftool

git diff @
git diff @^
git diff @^^
git diff @^^^
git diff @~3

git difftool @~3

git show @
```

### Resetting the HEAD

```bash
git glog

echo "file_dep.txt line 01" >> file_dep.txt
git status
git add .

echo "file_dep.txt line 02" >> file_dep.txt
git status
git add .

git status
git reset @^ --soft
git glog            # Oups! Were missing the "Commit #07"
git status          # But the files are still here and staged

git reflog @
git reset HEAD@{1} --mixed
git glog            # The "Commit #07" is back
git status          # The file is unstaged this time

git reset @ --hard
git glog            # No changes
git status          # The Working Tree is cleared of all changes
```

### Reverting changes

```bash
cat file_new.txt
git diff @^ -- file_new.txt

# Revert to previous state
git revert HEAD --no-edit
git status
git glog
cat file_new.txt

# Undo revert
git revert HEAD --no-edit
git status
git glog
cat file_new.txt
```

### Checking out & Tagging revisions (commits)

```bash
git checkout @
git checkout @^^    # Detached HEAD state! Carefully

git tag -a "v1.0" -m "Tag #01"
git show v1.0
git status
git glog

git checkout master
git status
git glog

git diff master v1.0

echo "file_tag.txt line 00" >> file_tag.txt
git add file_tag.txt
git commit -a -m "Commit #08"
git status
git glog

echo "file_tag.txt line 01" >> file_tag.txt
git commit -a --amend --no-edit
git status
git glog

```

### Branching && Merging
<!-- TODO@ion: Continue from here -->
--FF
--No-Conflict
--Conflicted
git mergetool