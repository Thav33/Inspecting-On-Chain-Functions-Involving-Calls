# Inspecting-On-Chain-Functions-Involving-Calls
This is a project(bounty) to look at smart contracts that’ve been deployed on-chain to see how functions are used. 

# Introduction

**Protocol Name:** Uniswap V3
**Category:** DeFi
**Smart Contract:** UniswapV3Factory

#Functional Analysis

**Function Name:** createPool
**Block Explorer Link:** https://etherscan.io/address/0x1F98431c8aD98523631AE4a59f267346ea31F984#code
**Function Code:** 
```
    function createPool(
        address tokenA,
        address tokenB,
        uint24 fee
    ) external override noDelegateCall returns (address pool) {
        require(tokenA != tokenB);
        (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
        require(token0 != address(0));
        int24 tickSpacing = feeAmountTickSpacing[fee];
        require(tickSpacing != 0);
        require(getPool[token0][token1][fee] == address(0));
        pool = deploy(address(this), token0, token1, fee, tickSpacing);
        getPool[token0][token1][fee] = pool;
        // populate mapping in the reverse direction, deliberate choice to avoid the cost of comparing addresses
        getPool[token1][token0][fee] = pool;
        emit PoolCreated(token0, token1, fee, tickSpacing, pool);
    }

```

**Used Encoding/Decoding or Call Method:** abi.encode

#Explanation

**Purpose:**
The "createPool" function is used to deploy a new liquidity pool in the Uniswap V3 protocol. It takes two tokens addresses and a fee as inputs and deploys a new pool contract for the specified token pair.

**Detailed Usage:**
Detailed Usage: The createPool function uses `abi.encode` to prepare data for creating a unique salt for the new pool deployment. It encodes three parameters: `token0`, `token1`, and `fee`. These represent the two tokens in the trading pair and the fee tier for the pool. The encoded data is then passed to `keccak256` to create a unique 32-byte value (the salt), which is used in the deploy function call to create the new pool contract.

**Impact**
The `createPool` function is crucial for the Uniswap V3 protocol as it enables the creation of new liquidity pools for a specified pair of tokens with a defined fee. Using `abi.encode` to generate a unique salt ensures that each pool has a deterministic and unique address based on its parameters. This allows for easy verification and discovery of pool addresses without needing to query the factory contract, therby, improving gas efficiency and user experience in the Uniswap ecosystem. 
It also enables the protocol to support multiple fee tiers for the same token pair, a key feature of Uniswap V3.

