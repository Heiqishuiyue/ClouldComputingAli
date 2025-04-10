uncommit
```bash
git reset --soft "HEAD^"
```

fetch a remote branch
```bash
git fetch origin <branchName>

git checkout <branchname>
```

fatal: Autentication failed
```bash
git config --global --unset credential.helper
```

staged changes will be added to the previous commit
```bash
git commit --amend
```

rename a branch
```bash
git branch -a

git branch branch_to_rename

git branch -m new_name

git push origin :old_name new_name
```

SSL certificate problem: unable to get local issuer certificate
```bash
git config --global http.sslbackend schannel

git config --global http.proxy ""
```

