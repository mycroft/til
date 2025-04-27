# notation

## Create testing certificate

```fish
$ notation cert generate-test --default "registry.mkz.me"
generating RSA Key with 2048 bits
generated certificate expiring on 2025-04-18T19:04:06Z
wrote key: /home/mycroft/.config/notation/localkeys/registry.mkz.me.key
wrote certificate: /home/mycroft/.config/notation/localkeys/registry.mkz.me.crt
Successfully added registry.mkz.me.crt to named store registry.mkz.me of type ca
registry.mkz.me: added to the key list
registry.mkz.me: mark as default signing key
```

## Listing certificates

```fish
$ notation cert list
STORE TYPE   STORE NAME        CERTIFICATE
ca           registry.mkz.me   registry.mkz.me.crt

$ notation key ls
NAME                KEY PATH                                                       CERTIFICATE PATH                                               ID   PLUGIN NAME
* registry.mkz.me   /home/mycroft/.config/notation/localkeys/registry.mkz.me.key   /home/mycroft/.config/notation/localkeys/registry.mkz.me.crt
```

## Signing an image

```fish
$ notation sign registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
Successfully signed registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
```

```fish
$ notation ls registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
└── application/vnd.cncf.notary.signature
    ├── sha256:d5f4495b9f5d73054764fd6933dc5efb1312129ac0762a5fecf026116b167d0a
    └── sha256:e9e0feffe6c92690a6997c469a1e3ea3ce7ac9d8a122c1203892893065faf612
```

## Verifying the image

### Create a trustpolicy to be used:

```fish
$ cat trustpolicy.json
{
    "version": "1.0",
    "trustPolicies": [
        {
            "name": "trust-policy-example",
            "registryScopes": [ "*" ],
            "signatureVerification": {
                "level" : "strict"
            },
            "trustStores": [ "ca:registry.mkz.me" ],
            "trustedIdentities": [
                "*"
            ]
        }
    ]
}

$ cp trustpolicy.json ~/.config/notation/
```

### Verify the signature

```fish
$ notation sign registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
Successfully signed registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817

$ notation verify registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
Successfully verified signature for registry.mkz.me/mycroft/golang-cdk8s@sha256:519af160c31be0b3f012ba7ede74137bfef9ac546e1845486a6d579e7266e817
```




