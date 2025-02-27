<div align="center" style="margin-top: 1em; margin-bottom: 3em;">
  <h1>ethereum.nix = Ethereum + Nix 🫶</h1>
</div>

<p align="center">
  <a href="https://ethereum.org/">
    <img src="https://img.shields.io/static/v1?label=&labelColor=1B1E36&color=1B1E36&message=ethereum%20ecosystem&style=for-the-badge&logo=data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz48c3ZnIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgd2lkdGg9IjQwIiBoZWlnaHQ9IjQwIiB2aWV3Qm94PSIwIDAgNDAgNDAiIGZpbGw9Im5vbmUiIHZlcnNpb249IjEuMiIgYmFzZVByb2ZpbGU9InRpbnktcHMiPjx0aXRsZT5FdGhlcmV1bSBsb2dvPC90aXRsZT48cGF0aCBkPSJNOSAyMi4xMTZMMTkuOTk5OSAyOS4wNDI0VjM4LjAwMDIiIGZpbGw9IiM1M0QzRTAiPjwvcGF0aD48cGF0aCBkPSJNMzAuOTk5OSAyMi4xMTZMMjAgMjkuMDQyNFYzOC4wMDAyIiBmaWxsPSIjNUE5REVEIj48L3BhdGg+PHBhdGggZD0iTTkgMTkuODc3NUwxOS45OTk5IDEzLjY5ODVWMUw5IDE5Ljg3NzVaIiBmaWxsPSIjRkZFOTREIj48L3BhdGg+PHBhdGggZD0iTTE5Ljk5OTkgMjYuODAzOVYxMy42OTg1TDkgMTkuODc3NUwxOS45OTk5IDI2LjgwMzlaIiBmaWxsPSIjQTdERjdFIj48L3BhdGg+PHBhdGggZD0iTTIwIDFWMTMuNjk4NUwzMC45OTk5IDE5Ljg3NzVMMjAgMVoiIGZpbGw9IiNGRjlDOTIiPjwvcGF0aD48cGF0aCBkPSJNMjAgMTMuNjk4NVYyNi44MDM5TDMwLjk5OTkgMTkuODc3NUwyMCAxMy42OTg1WiIgZmlsbD0iI0Q3OTdEMSI+PC9wYXRoPjwvc3ZnPg==" alt="Ethereum Ecosystem"/>
  </a>
  <a href="https://nixos.org/">
    <img src="https://img.shields.io/static/v1?logo=nixos&logoColor=white&label=&message=Built%20with%20Nix&color=41439a&style=for-the-badge" alt="Built with nix" />
  </a>
  <a href="https://github.com/nix-community/ethereum.nix/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/license-MIT%20v3.0-brightgreen.svg?style=for-the-badge" alt="License" />
  </a>
</p>

This is an experimental Nix project for integrating the most interesting / important projects in the Ethereum ecosystem as Nix packages / NixOS modules.

Many of the packages found ~~~~here will be added to `nixpkgs` repository once they're stable / mature enough. But for some others, more experimental ones, they can reside here.

This project is developed entirely in [Nix Flakes](https://nixos.wiki/wiki/Flakes).

## Usage

<details>
  <summary>As a flake (recommended)</summary>

```nix
{
  inputs = {
    ethereum-nix = {
      url = "github:nix-community/ethereum.nix";
      inputs.nixpkgs.follows = "nixpkgs";
    };
    nixpkgs.url = "nixpkgs/nixos-unstable";
  };

  outputs = { self, ethereum-nix, nixpkgs }: {
    nixosConfigurations.nixos = nixpkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules = [
        ({ pkgs, ... }: {
          nixpkgs.overlays = [ ethereum-nix.overlays.default ];
        })
      ];
    };
  };
}
```

</details>

<details>
  <summary>As an overlay</summary>

```nix
# configuration.nix
{ pkgs, ... }: {
  nixpkgs.overlays = [
    (import "${fetchTarball "https://github.com/nix-community/ethereum.nix/archive/main.tar.gz"}/overlays.nix")
  ];
  environment.systemPackages = with pkgs; [
    teku
    lighthouse
    # <...>
  ];
}
```

</details>

## Setup

### NixOS

If you're on NixOS, chances are you know what you're doing. If you don't have installed `direnv`, clone this repository and when entering inside the folder, just execute `nix develop`. It will load a `devShell` environment ready to be used.

Optional: [install direnv](https://github.com/direnv/direnv#basic-installation), so whenever you enter inside the directory, it will run `nix develop` for you automatically.

### Non-NixOS

To get started, run the following:

1. [Install Nix](https://nixos.org/download.html):

```bash
$ curl -L https://nixos.org/nix/install | sh
```

2. Clone this repository and when entering inside the folder, just execute `nix develop`. It will load a `devShell` environment ready to be used.

Optional: [install direnv](https://github.com/direnv/direnv#basic-installation), so whenever you enter inside the directory, it will run `nix develop` for you automatically.

## Development

### Docs

To serve the docs run `, docs-serve`. You can edit the docs in `./docs`.

To build the docs run `, docs-build`. The output will be inside of `./result`.

### Running tests

To run all tests you can use `nix flake check`, it will build all packages and run all tests.

To execute a specific test you can use `, test -h` which will provide more information.

### Formatting

You can manually format using the `format` command like so:

```bash
, fmt
```

## Applications

> Note: every command has a local and a remote variant. The local variant requires
> that the command is run from within the cloned repo. The remote variant can be run
> from wherever.
>
> Local: nix run .#my-app-name
>
> Remote: nix run github:nix-community/ethereum.nix#my-app-name
>
> For brevity and consistency, all the commands are listed in the local variant

### Executables provided

#### Consensus Clients

- [prysm](https://github.com/prysmaticlabs/prysm):
  - beacon-chain: `nix run .#prysm-beacon-chain`
  - client-stats: `nix run .#prysm-client-stats`
  - prysmctl: `nix run .#prysm-ctl`
  - validator: `nix run .#prysm-validator`
- [teku](https://github.com/ConsenSys/teku): `nix run .#teku`
- [lighthouse](https://github.com/sigp/lighthouse): `nix run .#lighthouse`

#### Execution Clients

- [besu](https://github.com/hyperledger/besu): `nix run .#besu`
- [erigon](https://github.com/ledgerwatch/erigon): `nix run .#erigon`
- [geth](https://github.com/ethereum/go-ethereum):
  - abidump: `nix run .#abidump`
  - abigen: `nix run .#abigen`
  - bootnode: `nix run .#bootnode`
  - clef: `nix run .#clef`
  - devp2p: `nix run .#devp2p`
  - ethkey: `nix run .#ethkey`
  - evm: `nix run .#evm`
  - faucet: `nix run .#faucet`
  - geth: `nix run .#geth`
  - rlpdump: `nix run .#rlpdump`

#### Validators

- [vouch](https://github.com/attestantio/vouch): `nix run .#vouch`

### MEV Utilities

- [mev-boost](https://github.com/flashbots/mev-boost/): `nix run .#mev-boost`

#### Signers

- [web3signer](https://github.com/ConsenSys/web3signer): `nix run .#web3signer`
- [dirk](https://github.com/attestantio/dirk): `nix run .#dirk`

#### Utilities / Tools / Other

- [ethdo](https://github.com/wealdtech/ethdo): `nix run .#ethdo`

## NixOS Modules

We provide modules for configuring and running various services. Some process arguments have been nix'ified. For those
which aren't there is typically and an `extraArgs` array that can be passed to the process.

### Geth

```nix
services.ethereum.geth.mainnet = {
    enable = true;
    openFirewall = true;
    service.supplementaryGroups = [users.groups.keys.name];
};

services.ethereum.geth.goerli = {
    enable = true;
    openFirewall = true;
    args = {
      network = "goerli";
      dataDir = "/data/ethereum/goerli/geth";
      authrpc.jwtsecret = sops.secrets.geth_jwt_secret.path;
      service.supplementaryGroups = [users.groups.keys.name];
    }
};
```

### Prysm Beacon Chain

```nix
services.ethereum.prysm.beacon.mainnet = {
    enable = true;
    args = {
      jwt-secret = secrets.prysm_jwt_secret.path;
      service.supplementaryGroups = [users.groups.keys.name];
    };
};

services.ethereum.prysm.beacon.goerli = {
    enable = true;
    args = {
      network = "goerli";
      dataDir = "/data/ethereum/goerli/prysm-beacon";
      jwt-secret = secrets.prysm_jwt_secret.path;
      service.supplementaryGroups = [users.groups.keys.name];
      checkpoint.sync-url = "https://goerli.checkpoint-sync.ethpandaops.io";
      genesis.beacon-api-url = "https://goerli.checkpoint-sync.ethpandaops.io";
    };
};
```

### Erigon

```nix
services.ethereum.erigon.sepolia = {
  enable = true;
  openFirewall = true;
  args = {
    chain = "sepolia";
    datadir = "/data/ethereum/sepolia/erigon";
    http = {
      enable = true;
      addr = "0.0.0.0";
      api = ["eth" "erigon" "engine" "sealer" "net"];
      vhosts = ["localhost" "dione"];
    };
  };
};
```

## Libraries

Some crypto projects may need specific libraries to be available to compile properly. Below you can find the list of included ones:

### Libraries provided

- [evmc](https://github.com/ethereum/evmc/)
- [bls](https://github.com/herumi/bls)
- [blst](https://github.com/supranational/blst)
- [mcl](https://github.com/herumi/mcl)

## Contribute

We welcome any kind of contribution or support to this project but before to do so:

- Make sure you have read the [contribution guide](/.github/CONTRIBUTING.md) for more details on how to submit a good PR (pull request).

In addition you can always:

- Add a [GitHub Star 🌟](https://github.com/nix-community/ethereum.nix/stargazers) to the project.
- Tweet about this project.

## Acknowledgements

This project has been inspired by the awesome work of:

- [`cosmos.nix`](https://github.com/informalsystems/cosmos.nix) by [Informal Systems](https://github.com/informalsystems) which this repository takes inspiration on it's README and several other places.

- [willruggiano](https://github.com/willruggiano) on his work done in [`eth-nix`](https://github.com/willruggiano/eth-nix) repository that served as the initial kick-start for working on this project.
