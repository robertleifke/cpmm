# Constant product algorithm implementation 

Modified from the zuniswap codebase orginally authored by jeiwan {https://github.com/Jeiwan/zuniswapv2}

The contracts allows for the decentralized exchange of any two generic ERC-20 tokens using the formula $x * y = k$ to price any of the two tokens in terms of the other. The original Uniswap V2 contracts are broken down in my medium article {https://medium.com/@robertleifke/breaking-down-uniswapv2-core-contracts-ebdd48416566}

The constant product formula enforces the rule that the product $k$ of each token's reserve balances $x$ and $y$ does not change after a trade.

On each trade a 30 basis point (bps) fixed fee is charged on the total trade size. The trading fee accrues to the reserves which changes $k$ and is stored in the $kLast$ variable for liquidity providers (LPs) to receive after burning their LP share. 

To illustrate the impact of a trade let's say we have a liquidity pool with 500 $token0$ and 2000 $token1$, then $x = 500$ and $y = 2000$. Alice, the trader wants to buy 10 of token0 in terms of token1 at market price.  

$x * y = \Delta x * \Delta y$ 

$100000 = 59.97 * \Delta y$


$\Delta y = 100000 / 59.97$

$\Delta y = 1667.5004$

$token1 = 2000 - 1667.5004$

$token1 = 332.4996$

In essence, the price of 1 token0 = $\frac{332.4996}{100}$ which is equal to 3.324996 of token1. After the trade, the fee accrues to the balance of token0 and so the balance of each reserve is 60 $token0$ and 1667.5004 $token1$. 

To maintain prices equal to external markets, arbitrageurs monitor the pools and and sell or buy to capture profits from mispricing. 

### Liquidity providers

Anyone can create a two pair market on Uniswap. To create one, a user known as a liquidity provider (LP) must provide two tokens in a pool. For this case, Bob adds 50 token0 and 200 token1. In return for providing this liquidity, Bob receives a LP share: a token that represents a pro-rata share of the total liquidity pool. Ownership is transferred to the owner Bob who has claim to the underlying assets plus fees at all times. To calculate the amount of LP shares Bob receives, UniswapV2 takes square root of token0 and token1. 

$LP_{shares} = \sqrt{50*200}$

$LP_{shares} = \sqrt{10000}$

$LP_{shares} = 100$

Now that Bob has 10 LP shares, if Bob decides to remove liquidty, these LP shares get burned by calling function removeLiquidity(). 
 
### Attack vectors

On Ethereum where their is transaction ordering and a public memepool there is Maximal Extractable Value (MEV) which is where a malicious actor can manipulate the transaction ordering to gain a profit. The term includes arbitrage as well as attack vectors. 

### Frontrunning

A point of attack is frontrunning. Specifically adding a swap transaction before one occurs raising the price and making the difference. //TODO

### LP Share Minting

On top of that, the protocol enforces the ratio for minting LP shares as well. So if the market ratio is 1:4 then... //TODO

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
