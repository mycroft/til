# cosign

## set up new keys

```fish
$ set -x COSIGN_PASSWORD (pwgen -sy 16 1)
$ cosign --verbose generate-key-pair
Private key written to cosign.key
Public key written to cosign.pub
```

## sign an image

```fish
$ docker pull registry.mkz.me/mycroft/golang-cdk8s
$ cosign sign --key cosign.key (docker inspect registry.mkz.me/mycroft/golang-cdk8s | jq -r .[0].RepoDigests[0]) --tlog-upload=false
Pushing signature to: registry.mkz.me/mycroft/golang-cdk8s
```

Note: to add tags, just `-a`: `-a author="Patrick MARIE"`

## verify an image

```fish
cosign verify --key cosign.pub  (docker inspect registry.mkz.me/mycroft/golang-cdk8s | jq -r .[0].RepoDigests[0]) --private-infrastructure=true

Verification for registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817 --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - The signatures were verified against the specified public key

[{"critical":{"identity":{"docker-reference":"registry.mkz.me/mycroft/golang-cdk8s"},"image":{"docker-manifest-digest":"sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817"},"type":"cosign container image signature"},"optional":{"author":"Patrick MARIE"}}]
```
