## Idea

Split location of local-only repo part and remote duplicated part of git.
E.g. we want to back-up but don't want to include say 2G of contnet that
already duplicated on remote.

Things to get inspired by:
* `git clone --reference <repo> <remote-repo>`
