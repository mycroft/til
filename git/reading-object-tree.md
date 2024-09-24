# Reading the object tree

`git cat-file` allows opening and reading commits/tree/blogs without any hassle. Starting with a commit `hash`, it is possible to find out what's there is in the given commit, to which tree it is attached, parent, etc.

```sh
$ git rev-parse HEAD
39f40cf0d0b31b16e235f07853d74167c72c1d8b

$ git cat-file -p 39f40cf0d0b31b16e235f07853d74167c72c1d8b
tree 5ee8a97718dd4662373ea5e722b008f03b7d229e
parent 79b04c1d8b3f275c37a6f4bd0b42d35ccaf42505
author Patrick Marie <pm@mkz.me> 1726849801 +0200
committer Patrick Marie <pm@mkz.me> 1726849801 +0200

Updating scylla/scylla-operator from v1.13.0 to v1.14.0

$ git cat-file -p 5ee8a97718dd4662373ea5e722b008f03b7d229e
100644 blob e2b5b73f808c496be73f4e399090a6ae63818bf2	.drone.yml
100644 blob 55e7b6e6698a2a415ef1f4d5df92a8ec77817cc9	.gitignore
100644 blob 20f1ead4b88ab9abadb951cf2a226aecd7aa633b	.golangci.yaml
100644 blob bcc630c55a74759a730809306bdedcae93a5c68c	.justfile
...

```

Reading a blob is similar:

```sh
$ git cat-file -p e2b5b73f808c496be73f4e399090a6ae63818bf2
kind: pipeline
type: docker
name: default

trigger:
  branch:
...

```


## Resources

- [git cat-file](https://git-scm.com/docs/git-cat-file)
