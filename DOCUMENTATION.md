Definition
==========

> Decentralized system to manage a project' code's history by tracking changes.

> (Basically only adds changes and avoids removing information.)

* Released 7 april 2005
* GNU GPL v2/v2.1
* Original author: Linus Torvalds

Characteristics
===============

* Git handles patches and manages them via references (a pointer to a change within the history). Git then makes you work with snapshots of the code.
* Git features branches, which are alternate history paths but with common ancestry.
* Almost all operations are done locally. You only share the history you got with a remote. A remote is a central point of collect of changes.

```
                                  |  local      |  remote
[Stash] | [Workspace] => [Index] <=> [Staging] <=> [Repository]
        |  status                 |   fetch     |
        |  add, rm, mv            |   pull      |
        |  commit                 |   merge     |  push
```

A `HEAD` is a tip reference in the history. `origin` has one, branches have one, Workspace has one.
`HEAD^` references `HEAD` - 1. `HEAD~42` references `HEAD` - 42.

Commands
========

init
----

Makes the working tree a (local) Repository. `--bare` makes it a centralized Repository (used on servers).

> git init [--bare]

clone
-----

Gets a Repository client into a (new) Workspace, referencing a remote (defaults to `origin`) from another (local) Repository or a (centralized) server.
Points to `origin/HEAD` by default, or specify `--branch BRANCH`.

> git clone REPOSITORY [--branch BRANCH] [PATH]

status
------

Show (local) changes in Workspace and Index.

> git status --branch

log
---

Prints the history.

> git log [--all] --oneline --decorate [REFENRECE]

You can reference a point or a range:

> git log HEAD

> git log 1.0.0..2.0.0 # REF1..REF2, old to new

checkout
--------

Switchout branch. Also restores Workspace path using ` -- <>`.

> git checkout -b NEW --track PARENT

> git checkout -- PATH # restores

remote
------

> git remote -v

> git push REMOTE :REFERENCE # can remove a tag or branch from a remote

branch
------

Manages branches. `-r` shows remotes.

> git branch [-r] -vv

> git branch --set-upstream-to=REMOTE/BRANCH

> git branch --unset-upstream

> git branch -d BRANCH # avoid -D

show
----

Shows a specific, recorded change.

> git show HEAD

diff
----

Shows current changes from Workspace to Index. Shows from Index to Staging using `--cached`.

> git diff

> git diff --cached # Index

add
---

Puts changes from the Workspace into the Index.

> git add PATH

> git add -u # tracked, updated

rm
--

Removes a known path from the local Repository (`-r` for recursive). To change the index, specify `--cached`.

> git rm [-r] PATH

> git rm [-r] --cached PATH

mv
--

Moves a file inside the Index, keeping track in the form of `rm` and `add`.

> git mv PATH PATH

commit
------

Records changes in the (local) history. From Index to Staging. Omits Workspace.
`-a` adds all files known to Index.
`--ammend` rewrite the last commit. Destructive as it replaces the HEAD commit with a new one.

> git commit -a

> git commit -m "MESSAGE"

> git commit --amend # be sure to have a clean state

revert
------

Commits the exact change's opposite of a specified commit, cancelling it. This is usefull when reverting a change that got shared, since it is not destructive, only adds history (in oposition to `reset`).

> git revert HEAD^

cherry-pick
-----------

Picks a single commit and put in in the current history at HEAD. Goes directly in Staging unless `--no-commit` is specified (which then will change its hash).

> git cherry-pick [--no-commit] HASH

reset
-----

Restores HEAD somewhere else. Can be destructive for the Workspace.
`--mixed` preserves Workspace.
`--hard` is destructive for both.

> git reset # cleans Index

> git reset --hard REFERENCE # destructive

merge
-----

Joints histories. (I dislike merging to a tracking branch. It should merge child branches.)
In non-fast-forward mode, creates a merge commit that resolves both HEADs.
Fast-forward mode (enforced by `--ff-only`) is like a `rebase`.

> git merge --no-ff BRANCH

(Clean your Workspace and Index before merging.)

Offers different strategies:

> git merge -s ours ...

> git merge -X theirs ...

> git merge --abort

rebase
------

Moves the base of the current divergente history.
Reapplies commits before current tip. Is preservative.

> git fetch && git rebase REMOTE/BRANCH

`--interactive` allows to re-write (local) history but is destructive.

tag
---

Avoid same names for branches and tags. Prefixing tags with 'v' or keeping a semver format is recommended.

> git tag --list

> git tag --contains HASH

> git tag [-f] TAG HASH

> git tag [-f] -d TAG

> git push --tags

fetch
-----

Updates references from remote(s).

> git fetch [--all] --tags --pruned

pull
----

Gets changes from REMOTE. _I dislike its usage without rebase!_ Also does a `fetch` beforehand.

> git pull --rebase REMOTE BANCH

_Use this when getting changes from REMOTE._

push
----

Publishes / distributes local changes.

> git push -u [REMOTE BRANCH] [--tags]

apply
-----

Applies changes from a patch (`show`). Goes into the Index.

> git apply PATH

blame
-----

Shows per-line history of a file.

> git blame [REFERENCE] PATH

reflog
------

History of actions and HEADs.
Usefull to cancel some destructive action that put you out of the desired history path.

> git reflog

Just `reset --hard HEAD@{N}`.

stash
-----

Only command to save off-record changes. Puts all Workspace and Index changes out of the current working tree.

> git stash

> git stash list

> git stash show stash@{N}

> git stash apply stash@{N}

> git stash drop stash@{N}

> git stash clear

Workflow
========

Branches
--------

* `master` is stable and represents production.
* `dev` (child of `master`) is a work-in-progress codebase, with finished features. Bugfixes might happen directly on it.
* Feature branches (children of `dev`) are topic focused and unstable.

> git checkout -b dev --track [REMOTE/]master

> git checkout -b feature --track [REMOTE/]dev

Merging
-------

* When the feature completes, `dev` is updated and `merge` the feature.

> git checkout dev

> git pull --rebase origin dev

> git merge --no-ff feature

> git push origin dev

> git branch -d feature

* When ready for production, `master` `merge` `dev` _seamlessly_. 

> git checkout master

> git pull --rebase origin master

> git merge --no-ff dev

* A tag is then applied and `push` to `origin`.

> git tag 1.0.0

> git push --tags origin master

Deployment
----------

> git fetch --tags --prune origin

> git reset --hard HEAD

> git rev-parse TAG && git reset --hard TAG || exit 1

> ...

> git rebase

Tips And Tricks
===============

> git rev-parse --abbrev-ref @{u}  # shows tracking branch

> git add --update && git ls-files --deleted -z | xargs -0 git rm 2>/dev/null # removes and adds

> git log --format='%aN <%cE>' | sort -u # authors

> cat .git/config

> git reset --mixed # unadds

> git config pull.default upstream

> git config push.default tracking

> git rev-list $(git rev-parse --abbrev-ref @{u})..HEAD # commits ahead

> git rev-list HEAD..$(git rev-parse --abbrev-ref @{u}) # commits behind

> git reset--hard HEAD@{1} # undo

> git push origin :REFERENCE # pushes nothing into X (remote deletes)

GitIgnore
=========

> filename
>
> filename.*
>
> dir/filename
>
> */filename
>
> **/filename
>
> !dir/filename

Under The Hood
==============

> cat .git/HEAD

> ls -l .git/refs/heads/

> ls -l .git/refs/remotes/

> cat .git/refs/heads/master


Links
=====

* [git-scm.com/docs](git-scm.com/docs)
* [github.com/git-game/git-game](github.com/git-game/git-game)
* [https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)
* [semver.org](semver.org)
