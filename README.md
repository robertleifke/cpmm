# Constant product algorithm
Modified zuniswap implementation by jeiwan

The contracts allows for the decentralized exchange of any two generic ERC-20 tokens using the formula x * y = k to price any of the two tokens in terms of the other. 

## Local development

This project uses [Foundry](https://github.com/foundry-rs/foundry) as the development framework.

### Dependencies

```bash
forge install
```

### Compilation

```bash
forge build
```

### Test

```bash
forge test
```

Use Anvil for deploying the contracts on your local node and test from a connected frontend.
