# Halo experiments

Halo is a project from the [Omni](https://omni.network/) team that sits between CometBFT and an Ethereum Execution Layer node (e.g., Nethermind or Geth), translating Comet's ABCI calls into Engine API calls.

Note that some important work in Halo appears to be [in development](https://github.com/omni-network/omni/blob/fdaae16f6d28691edc984df7adbcb21c29e74aa5/halo/attest/keeper/keeper.go#L586) at this time. If Halo were to be used in production as a translation layer for an L1, this would need to be investigated further. Additionally, Halo is designed to be used in the context of the Omni network, which has other "rollup interoperability" goals that don't concern us here. A stripped-down version of Halo may be more suitable for a production-ready setup.

This repo contains a devnet that is mostly taken from the Omni e2e tests (two validator nodes), with the Omni-specific items removed. It uses geth nodes for the Execution Layer, but any Ethereum client would work (and any Engine API-compliant server, for that matter).

# Usage

To start (you may see a few warnings and errors as the components start up, but those should go away once they initialize):

```bash
docker compose up
```

To finish, press Ctrl-C. Run the following to clean up:

```bash
docker compose down
# These commands may require `sudo`, depending on your Docker setup.
git clean -fd
git reset --hard
```

# Alternatives

Bearachain's [Polaris](https://github.com/berachain/polaris/tree/main) serves a similar function. Unlike Halo, Polaris is [meant to be used](https://github.com/berachain/polaris/tree/main/cosmos/x/evm) as a Cosmos SDK module. In Polaris, Engine API calls are manually triggered via a Cosmos SDK message, whereas Halo [automatically translates](https://github.com/omni-network/omni/blob/fdaae16f6d28691edc984df7adbcb21c29e74aa5/halo/app/app.go#L110-L122) ABCI calls into Engine API calls.

Another option is to manually instrument an Execution Layer client to fulfill the ABCI. While a proper implementation will be more efficient by only storing chain data at the CometBFT layer, it has a number of drawbacks, including:

1. Difficult to get right. The EVM still expects some chain data to be available (see the `BLOCKHASH` opcode), so an efficient implementation cannot disregard storage altogether. It will need to modify the EVM and/or storage layer with an appropriate in-memory cache.
1. Client diversity and development speed. By wrapping the Engine API with translation logic, Halo and Polaris can use any battle-tested client implementations out of the box, improving security and decreasing maitenance burden.
