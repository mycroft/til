# Setting a replication between gitea and github

You'll need some github credentials (login and api key) with proper authorization.

## Setting up a token in github

In [github's tokens/settings](https://github.com/settings/tokens), create a classic token with "public_repo" access and a meaningful name, such as "gitea to github replication".

Also, create an empty repository that will receive the replication. Note the `http` url.


## Set up the replication

In your [gitea](https://gitea.com) instance, in an existing repository settings, in the `Mirror settings` section, set the `Git Remote Repository URL` to the github repository url, set `username` to your github's username and use the token (prefixed with `ghp_`) as a password.

Set `sync when commits are pushed` and that should do it.

## Ressources

- [gitea - Repository Mirror](https://docs.gitea.com/usage/repo-mirror)
- [github - Scopes for OAuth apps](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)

