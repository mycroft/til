# Removing files from repository

There is a nice command to help rewriting whole repository without much hassle: 'git-filter-repo'. In this use case, I used it to remove some directory files entirely (so I could push the repository to github)

```sh
$ git filter-repo --force --filename-callback '
  if b"fake_data/" in filename:
    return None
  else:
    return filename
  '
```

If for each entry the name is None, nothing will be kept in the history as all commits with the filename will be rewritten, removing the file in the objects of the commit. If commit becomes empty, it is removed from the history.


