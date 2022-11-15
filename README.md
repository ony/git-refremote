## Idea

Split location of local-only repo part and remote duplicated part of git.
E.g. we want to back-up but don't want to include say 2G of contnet that
already duplicated on remote.

Things to get inspired by:
* `git clone --reference <repo> <remote-repo>`

## How it works?

Keep in `$XDG_CACHE_HOME/git-refremote/references` local bare repositories for
remotes indexed by SHA-1 on URI.

`git-refremote setup` - re-fetch remote into bare repository (cache). Instruct
`git` for current repo to fallback to that cache as if initially cloned with `--reference`.
Note that it will perform `git gc` to remove no longer needed objects.

`git-refremote clone` - first clone into bare/cache repo and then use `git clone --reference ...`.

`git-refremote update` - first update bare/cache repo and then do actual `git update`.

## Existing repo example

In existing git repository (e.g. nixpkgs):
```sh
git-refremote setup origin  # last argument is remote name that can be omitted if only one
du -shx .git $(cat .git/objects/info/alternates)
```
```sh
32M	.git
3.8G	/home/mykola/.cache/git-refremote/references/3a/35efd4f141280f005cf892fe192b077045ab38/objects
```

Can move `~/.cache/git-refremote/references` to a different than home partition
and symlink/bind it to free some space in home folder.
Just don't forget to disable following symlinks during backup or exclude that
folder explicitly.


## Clone repo example

```sh
git-refremote clone https://github.com/ony/git-refremote.git
```
```
Cloning into bare repository '/home/mykola/.cache/git-refremote/references/b7/853f42031980fcc583bc5f8fce02a2ee72ac77'...
remote: Enumerating objects: 15, done.
remote: Counting objects: 100% (15/15), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 15 (delta 2), reused 12 (delta 2), pack-reused 0
Receiving objects: 100% (15/15), done.
Resolving deltas: 100% (2/2), done.
Cloning into 'git-refremote'...
```

Note that it doesn't support any command line options specified before remote
URI. Though target directory still can be specified.
