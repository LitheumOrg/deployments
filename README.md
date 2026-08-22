# deployments

Version-controlled deployment artifacts (contract addresses, content hashes, ABIs)
per Litheum network. Consumed as a git submodule (mounted at `deployments/`) by
`Dapps`, `DeMSG`, and `LitheumCore`.

No secrets here — only public addresses, hashes, and ABIs. Keys stay in
`~/.litheum/`.

## Layout

```
deployments/<network>/
  servicemarketplace        # contract address, 0x… (42 chars)
  dappstore                 # contract address, 0x…
  dappstore.abi / idapp.abi # ABI JSON
  dequic-service-address    # 0x…
  declaw-service-address    # 0x…
  <name>-hash               # content hash, bare hex (no 0x)
```

Addresses are `0x`-prefixed; `*-hash` values are bare hex.

## `LITHEUM_NETWORK`

The active network is a directory name under `deployments/` selected by
`LITHEUM_NETWORK`:

```bash
export LITHEUM_NETWORK=local     # -> deployments/local/
```

- **Reading** (wasm build, gateway/declaw config, TUI): `LITHEUM_NETWORK` is
  optional and **defaults to `mainnet`** when unset. Only fails if the resolved
  network directory is missing — no zero-address fallbacks.
- **Writing** (deploy scripts): `LITHEUM_NETWORK` is **required** and scripts fail
  loudly if it is unset. Creating deployment artifacts must be explicit about the
  target network.

## Setup

```bash
git submodule update --init                       # existing clone
git clone --recurse-submodules <parent-repo-url>  # fresh clone
```

## Networks

- **Shared** (`mainnet`, `testnet`): live on the default branch. Run deploy scripts,
  commit the changed `deployments/<network>/` files, open a PR, bump the submodule
  pointer in consumers.
- **Local** (`LITHEUM_NETWORK=local`): keep your artifacts on a personal branch, not a
  shared one:

  ```bash
  cd deployments
  git checkout -b local-<you>
  # deploy locally; scripts write deployments/local/
  git add local && git commit -m "local: <you> dev chain"
  git push origin local-<you>
  ```
