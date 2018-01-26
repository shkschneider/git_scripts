Definition
==========

> Decentralized system to manage a project' code's history by tracking changes.

> (Basically only adds changes and avoids removing information.)

Characteristics
===============

* Git handles patches and manages them via references (a pointer to a change within the history). Git then makes you work with snapshots of the code.
* Git features branches, which are alternate history paths but with common ancestry history.
* Almost all operations are done locally. You only pusblish/share the history you got with a remote. A remote is a central point of collect of diffs.

```
 local                                             |   .git/remote
[Wording Directory] => [Index] <=> [Staging Area] <=> [Repository]
 status                         |   fetch          |
 add, rm, mv                    |   pull           |
 commit
```

Commands
========

init
----

Makes the working tree a (local) Repository. `--bare` makes it a centralized Repository (used on servers).

> git init

> git init --bare

clone
-----

Gets a Repository client into a (new) Working Directory, referencing a remote (defaults to `origin`) from another (local) Repository or a (centralized) server.
Points to `origin/HEAD` by default, or specify `--branch BRANCH`.

> git clone REPOSITORY [PATH]

> git clone REPOSITORY -b BRANCH [PATH]

status
------

Show (local) changes in Working Directory and Index.

> git status --branch

add
---

Puts changes from the Working Directory into the Index.

> git add PATH

rm
--

Removes a known (unless `--force` is specified) path from the local Repository. `--cached` removes from the Index.

> git rm [-f] PATH

> git rm [-f] --cached PATH

mv
--

Moves a file inside the Index, keeping track in the form of `rm` and `add`.

> git mv PATH PATH

reset
-----

Restores HEAD somewhere else. Is destructive for the Working Directory.
`--mixed` preserves Working Directory.
`--hard` is destructive for both.

> git reset # cleans Index

> git reset --hard REFERENCE # destructive

diff
----

Shows current changes from Wording Directory to Index. Can you changes from Index to Staging Area using `--cached`.

> git diff

> git diff --cached # Index

commit
------

Records changes in the (local) history. From Index to Staging Area. Omits Wording Directory.
`-a` adds all files known to Index.
`--ammend` rewrite the last commit. Destructive as it replaces the HEAD commit with a new one.

> git commit -a

> git commit -m "MESSAGE"

checkout
--------

Switchout branch. Also restores Working Directory path using ` -- <>`.

> git checkout -b NEW --track PARENT

branch
------

Manages branches.

> git branch -avv

> git branch --set-upstream-to=origin/BRANCH

> git branch --unset-upstream

> git branch -d BRANCH # avoid -D

show
----

Shows a specific, recorded change.

> git show HEAD

log
---

Prints the history.

> git log --oneline --decorate

> git log --all --oneline --decorate

apply
-----

Applies changes from a patch (`show`).

cherry-pick
-----------

Picks a single commit and applies put it on the current HEAD (same commit???). Goes directly in Staging Area.

> cherry-pick --no-commit HASH

revert
------

Commits the exact change's opposite of a specified commit, cancelling it.

> git revert HEAD^

rebase
------

Moves the base of the current divergente history.
Reapplies commits before current tip. Is preservative.
`--interactive` allows to re-write (local) history but is destructive.

blame
-----

Shows per-line history of a file.

merge
-----

Joints histories. (Try to pull into rather than onto.)
In non-fast-forward mode, creates a merge commit that resolves both HEADs.

> git merge --no-ff BRANCH

Clean your Working Directory and Index before merging.

Offers different strategies:

> git merge -s ours ...

> git merge -X theirs ...

> git merge --abort

fetch
-----

Updates references from remote(s).

> git fetch [--all] --tags --pruned

stash
-----

Only command to save off-record changes. Puts all Working Directory and Index changes out of the current working tree.

> git stash

> git stash list

> git stash show stash@{N}

> git stash apply stash@{N}

> git stash drop stash@{N}

> git stash clear

reflog
------

History of actions and HEADs.
Usefull to cancel some destructive action that put you out of the desired history path.

> git reflog

Just `reset --hard HASH`.

tag
---

Avoid same names for branches and tags. Prefixing tags with 'v' or keeping a semver format is recommended.

> git tag --list

> git tag --contains HASH

> git tag [-f] TAG [COMMIT]

> git tag [-f] -d TAG

> git push --tags

remote
------

> git remote -v
> git push REMOTE :REFERENCE # can remove a tag or branch from a remote

Tips And Tricks
===============

> git rev-parse --abbrev-ref @{u}  # shows tracking branch

> git add --update && git ls-files --deleted -z | xargs -0 git rm 2>/dev/null # removes and adds

> git log --format='%aN <%cE>' | sort -u # authors

> cat .git/config

> reset --mixed # unadds

> git config pull.default upstream

> git config push.default tracking

> git rev-list $(git rev-parse --abbrev-ref @{u})..HEAD # commits ahead

> git rev-list HEAD..$(git rev-parse --abbrev-ref @{u}) # commits behind

> git reset--hard HEAD@{1} # undo

GitIgnore
=========

> filename
> filename.*
> dir/filename
> */filename
> **/filename
> !dir/filename

* git-scm.com/docs
* github.com/git-game/git-game
