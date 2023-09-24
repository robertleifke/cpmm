# CPMM3: A gas-efficient design pattern for a constant product market maker

A gas-efficient Constant Function Market Maker (CFMM) implementation using the Constant Product pricing algorithm and a single-contract design pattern.

The **goal** is to enable the decentralized exchange of any two generic ERC-20s on pooled liquidity.

### Pricing Algorithm

A constant function market maker is an autonomous suite of smart contracts deployed on a blockchain that buys or sells a token to keep the reserves of two tokens constant. It does this by using an invariant (math formula) to price a token in terms of another on any pooled liquidity and buying or selling the token *per block* to *arbitraguers* depending on the price.

Uniswap, the most popular CFMM, used for exchanging cryptocurrencies on a blockchain, popularized the *constant product invariant*.

I like yo

#### Calculating a Price

The formula $x * y = k$ keeps the product of two tokens, $k$ constant so that each balance of each token $x$ and $y$, does not change after a trade. By doing so, we can get the price a token in terms of the other. To get the price of $x$ or `tokenA` we calculate:

`tokenA`: $x = y/k$ 

So if we have a pool of ETH/USDC and the balance of ETH is 1400 and USDC is 400 then the 

But liquidity providers need to be compensated for renting liquidity so on each trade, a 30 basis point (bps) fixed fee is charged on the total trade size. The trading fee accrues to the reserves which changes $k$ and is stored in the $kLast$ variable for liquidity providers (LPs) to receive after burning their LP share. 

To illustrate the impact of a trade let's say we have a liquidity pool with 500 $tokenA$ and 2000 $tokenB$, then $x = 500$ and $y = 2000$. Alice, the trader wants to buy 10 of tokenA in terms of tokenB at market price.  

$x * y = \Delta x * \Delta y$ 

$100000 = 59.97 * \Delta y$


$\Delta y = 100000 / 59.97$

$\Delta y = 1667.5004$

$tokenB = 2000 - 1667.5004$

$tokenB = 332.4996$

In essence, the price of 1 tokenA = $\frac{332.4996}{100}$ which is equal to 3.324996 of tokenB. After the trade, the fee accrues to the balance of tokenA and so the balance of each reserve is 60 $tokenA$ and 1667.5004 $tokenB$. 

To maintain prices equal to external markets, arbitrageurs monitor the pools and and sell or buy to capture profits from mispricing. 

### Liquidity providers

Anyone can create a two pair market on Uniswap. To create one, a user known as a liquidity provider (LP) must provide two tokens in a pool. For this case, Bob adds 50 tokenA and 200 tokenB. In return for providing this liquidity, Bob receives a LP share: a token that represents a pro-rata share of the total liquidity pool. Ownership is transferred to the owner Bob who has claim to the underlying assets plus fees at all times. To calculate the amount of LP shares Bob receives, UniswapV2 takes square root of token0 and token1. 

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

Each share in minted as a put option on the two token pairs. 

## Architecture

### Engine (`Engine.sol`)

A single contract the holds all the core logic. Responsbile for storing and initializing all token pairs.

Engine also inherits the ERC-20 token standard.

Different from the traditional design are commands which...

```
enum Commands {
        Swap,
        WrapWETH,
        UnwrapWETH,
        AddLiquidity,
        RemoveLiquidity,
        Accrue,
        CreatePair
    }
```

The engine contract has only two public functions `execute()` and `recieve()`.

`execute()` takes a list of command inputs as arguments and executes them. After the callback address of a contract passed in is called after all the commands have been executed.

#### Functions

`function _addLiquidity()` takes in **three** arguments; 

* `to` which is the address of the user adding liquidity
* `params` a struct with the following arguments
    * `token0`
    * `token1`
    * `scalingFactor` 
    * `amountDesired`
* `account`the user's account


To compare against the original Uniswap V2 contracts, I broke down the code in this medium article. {https://medium.com/@robertleifke/breaking-down-uniswapv2-core-contracts-ebdd48416566}. Jeiwan also has a fantastic article on V2 with slight modifications he made. 



## Deployment and Usage

This project uses [Foundry](https://github.com/foundry-rs/foundry) as the development framework.

### Dependencies

```bash
forge install
```

### Compilation

```bash
forge build
```

### Deploy on a local node

```bash
anvil 
```

### Test

```bash
forge test
```






