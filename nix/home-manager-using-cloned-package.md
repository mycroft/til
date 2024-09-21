# Using a package clone in home-manager

When proposing PRs on any opensourced software, you might want to use your fork instead of the currently nix packaged original source, especially when using nixos or home-manager.


```nix
# In modules/tlrc/default.nix
{ pkgs, ... }:
{
  nixpkgs.overlays = [ (self: super: { tlrc = super.callPackage ../../nix/tlrc.nix { }; }) ];
}
```

Add this module into your imported modules

```nix
# In modules/default.nix
...
  ./mmtc
...
```

Copy/clone the existing official module from nixpkgs in your tree, and prepare to patch the `fetchFromGithub` parameters, such as `owner`, `rev` and `hash`es.

```nix
# In nix/tlrc.nix
# Copy https://github.com/NixOS/nixpkgs/blob/nixos-24.05/pkgs/by-name/tl/tlrc/package.nix
...
  src = fetchFromGitHub {
    owner = "mycroft";
    repo = pname;
    rev = "c85b0234d0eda1b2f94903d302d0ac6ce2564dc1";
    hash = "sha256-grLUb2rG7uXpfEdRpnNJ4fijE211RWaqFtzDZG0QrN4=";
  };
...
```

To get the correct hashes, simply run `home-manager switch` (or `nixos-rebuild switch`) that will eventually fail, priting the expected `hash`.

Eg:

```sh
$ home-manager build
error: hash mismatch in fixed-output derivation '/nix/store/riydnqssk2d5kg0hgxvnqxpc7aln7fbr-source.drv':
         specified: sha256-3KS/KN6/RO+PxoxbCVryymnTyWcmfXuCoc9E+asdU/A=
            got:    sha256-grLUb2rG7uXpfEdRpnNJ4fijE211RWaqFtzDZG0QrN4=
error: 1 dependencies of derivation '/nix/store/xl08aqspf45h4wv649kqgz5npwn6fsr6-tlrc-1.9.3-adding-branch.drv' failed to build
...
```

# Resources

- [adding forked tlrc in my home-manager config](https://github.com/mycroft/nix-home-env/commit/34ae1acc9a1292a72b8ff739d6f8f47341ee6eee)
