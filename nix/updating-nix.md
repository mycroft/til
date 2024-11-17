# Updating nix

It seems like nix requires updating time to time. To update nix on a linux multi-user system:

```sh
$ su -
# nix-env --install --file '<nixpkgs>' --attr nix cacert -I nixpkgs=channel:nixpkgs-unstable
# systemctl daemon-reload
# systemctl restart nix-daemon
```

On a single-user system:

```sh
$ nix-env --install --file '<nixpkgs>' --attr nix cacert -I nixpkgs=channel:nixpkgs-unstable
```

## Resources

- [Upgrading Nix](https://nix.dev/manual/nix/2.25/installation/upgrading)

