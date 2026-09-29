# git

## Consecutive checkout

1. 假设master的commit从旧到新依次是1，2，3，4，我当前checkout到了2。我现在希望checkout到3。但是假设我不知道3的commit id。我应该怎么做

   ```bash
   git checkout $(git rev-list --topo-order HEAD..master | tail -n 1)
   ```

1. 假设master的commit从旧到新依次是1，2，3，4，我当前checkout到了2。我现在希望checkout到1。但是假设我不知道1的commit id。我应该怎么做

   ```bash
   git checkout HEAD~1
   ```

## Multi git remotes sync

1. Track all remote branches:

   ```bash
   for remote in $(git branch -r | grep -v '\->' | grep origin); do git branch --track "${remote#origin/}" "$remote"; done
   ```

1. Add multi git remotes:

   ```bash
   tmp=$(git remote -v | grep origin | head -n1 | awk '{print $2}'); tmp="${tmp##*/}"; tmp="${tmp%.git}"; git remote add gitee git@gitee.com:ysl2/"$tmp".git; git remote -v
   ```

1. Push to all remotes:

   ```bash
   git remote | xargs -I {} -P 0 git push {} --all
   git remote | xargs -I {} -P 0 git push {} --all --force
   ```

## CRLF to LF

### crlf2lf

> Ref: <https://github.com/XadillaX/node-crlf2lf>

```bash
npm install -g crlf2lf
crlf2lf -r .
```

### dos2unix

- For Linux/macOS:

  ```bash
  # brew install dos2unix
  find . -type f -exec dos2unix {} +
  ```

- For Windows PowerShell:

  ```powershell
  (Get-Content "filename.txt") -replace "`r`n", "`n" | Set-Content "filename.txt"
  ```

## Make git case-sensitive (might cause bugs when renaming files only by case)

```bash
# Check current setting
git config core.ignorecase

# Set to false to make git case-sensitive
git config core.ignorecase false
```

## Fetch from upstream

```bash
git remote add upstream git@example.com:user/repo.git
git fetch upstream
git rebase upstream/master
```

## Only add new file / deleted file

```bash
# Only add new file
git ls-files --others --exclude-standard -z | xargs -0 git add

# Only add deleted file
git ls-files --deleted -z | xargs -0 git rm --cached
```

## Git LFS

```bash
brew install git-lfs
git lfs install
```
