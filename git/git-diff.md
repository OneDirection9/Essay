# git-diff

https://git-scm.com/docs/git-diff

> `A` and `B` can be a commit sha or a branch name.

- `git diff A B`: compare difference between `A` and `B`. synonymous: `git diff A..B`.
- `git diff A...B`: equal to `git diff $(git merge-base A B) B`. `git merge-base A B` outputs a commit of ancestor of `A` and `B`.
- `git diff HEAD~N`: diff the working tree with the N-th previous commit
- `git diff A B -- myfile.cs`: compare `myfile.cs` between `A` and `B`.
- `git diff branch1:/path/to/file branch2:/path/to/file`
