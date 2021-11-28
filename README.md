# Automated Curve Yield Farming Strategy on forked Mainnet with sUSD & DAI collateral (https://curve.fi/)

This mix is configured for use with [Ganache](https://github.com/trufflesuite/ganache-cli) on a [forked mainnet](https://eth-brownie.readthedocs.io/en/stable/network-management.html#using-a-forked-development-network).

## How it Works
### Deposit
In this Strategy we take DAI as collateral. We deposit the Dai into the Curve sUSD Stablecoin pool and get Curve LP tokens, representing out supplied liquidity in return. The LP tokens are deposited into the Curve Rewards Gauge contract. We plan on changing 
### Harvest & Compounding
The reInvest() function compounds the accrued rewards. It first claims the rewards from the Curve Rewards Gauge Contract. Then it converts the CRV & SNX rewards to DAI through the Uniswap V3 Router & reinvests the DAI back into the strategy.

### Withdrawing Funds
When calling the with functtioon, the strategy first unstakes the LP tokens from the Rewards Gauge, and then exchanges the LP tokens for DAI from the Curve sUSD Pool. 

## [Expected Yield]

Fees APY = 1.34%<br>
CRV Rewards APY = 2.73%<br>
SNX Rewards APY = 1.83% <br>

<strong>Total APY = 5.9%</strong>

<strong> Note: staking of the Curve Lp Tokens on convex is currently being built and will be added. This adds some extra complexity since we also have to rebuild the automatic compounding to claim and restake rewards in convex.</strong>


## Installation and Setup

1. Install Brownie & Ganache-CLI, if you haven't already.

2. Copy the .env.example file, and rename it to .env

3. Sign up for Infura and generate an API key. Store it in the WEB3_INFURA_PROJECT_ID environment variable.

4. Sign up for Etherscan and generate an API key. This is required for fetching source codes of the Ethereum mainnet contracts we will be interacting with. Store the API key in the ETHERSCAN_TOKEN environment variable.

Install the dependencies in the package
```
## Javascript dependencies
npm i

## Python Dependencies
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Basic Use

To deploy the Strategy in a development environment:

1. Compile the contracts 
```
  brownie compile
```

2. Run Scripts for Deployment
```
  brownie run deploy
```
Deployment will deploy the strategy


3. Run the test deployment in the console and interact with it
```python
  brownie console
  deployed = run("deploy")

  ## Takes a minute or so
  Transaction sent: 0xa0009814d5bcd05130ad0a07a894a1add8aa3967658296303ea1f8eceac374a9
  Gas price: 0.0 gwei   Gas limit: 12000000   Nonce: 9
  UniswapV2Router02.swapExactETHForTokens confirmed - Block: 12614073   Gas used: 88626 (0.74%)

  ## Now you can interact with the contracts via the console
  >>> deployed
  {
      'deployer': 0x621257F9332b6F98e02e4C371B6C3AF001a0Bf0B,
      'strategy': 0x9E4c14403d7d9A8A782044E86a93CAE09D7B2ac9,
  }
  >>>

  ## Deploy also uniswaps want to the strategy (accounts[0])
  >>> deployed.strategy.underlyingBalanceInModel()
  240545908911436022026

```
## Deployment

You can have a look at the deployment script at (/scripts/deploy.py)

When you are finished testing and ready to deploy to the mainnet:

1. [Import a keystore](https://eth-brownie.readthedocs.io/en/stable/account-management.html#importing-from-a-private-key) into Brownie for the account you wish to deploy from.
2. Run [`scripts/deploy.py`](scripts/deploy.py) with the following command

```bash
$ brownie run deployment --network mainnet
```

You will be prompted to enter your keystore password, and then the contract will be deployed.
