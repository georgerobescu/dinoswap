unifork

`npx saddle compile && npx saddle test`

    /*
    For example:

    	1 DAI = 1 ETH for simplicity
        
    	supply 100 ETH.
    	boost borrow 100 ETH, and supply it (now 200 ETH supplied)
    	borrow 133 DAI (200 * 1/150%)
    	trade 133 DAI into 133 ETH (execute bytecode for this)
    	repay boost 100 ETH
    	supply the remaining 33 ETH
    	the user now has a 233 ETH supply, and 133 DAI outstanding borrow (and no cash)
    	leverage: 233/100 = 2.33x
    	collateral ratio: 200/133 = 175%
    	learn morehttps://ian.pw/posts/2018-01-25-maximum-leverage-on-maker.html

    Compare this to what can be achieved normally in iteration
    	100 ETH => 66 DAI borrow. Supply 66 ETH, now you have 166 ETH supplied.
    	leverage: 166/100 = 1.66x
    	collateral ratio: 166/66 = 250%

    The max you can go with this approach is 3x (@ 150% collateral ratio):
    supply 100 ETH.
    	boost borrow 200 ETH, and supply it (now 300 ETH supplied)
    	borrow 200 DAI (300 * 2/3)
    	trade 200 DAI into 200 ETH (execute bytecode for this)
    	repay boost 200 ETH
    	supply the remaining 0 ETH
    	end: user has a 300 ETH supply, and 200 DAI outstanding borrow (and no cash)
    	leverage: 300/100 = 3x
    	collateral ratio: 300/200 = 150%
    	learn morehttps://ian.pw/posts/2018-01-25-maximum-leverage-on-maker.html
    */

https://www.npmjs.com/package/eth-saddle


## Saddle

Saddle is a simple framework for developing Ethereum Smart Contracts. Think of it like a lean and mean version of truffle.

## Philosophy

**What saddle does**

  * Allows you to compile and deploy your contracts.
  * Add fast, parallelized tests for your contracts.

** What saddle does not**

  * Migrations. It's just "deploy".
  * Solidity-language tests
  * npm-based Solidity plugins
  * solcjs
  * complex configuration
  * bloat

** What saddle wants to do... one day**

  * Smart code coverage (via vm trace's)
  * Smart revert messages (e.g. backtraces for Solidity errors)
  * Verify your contracts on Etherscan

## Installing Saddle

To install saddle, simply create a new npm or yarn project and run:

```
yarn add eth-saddle
# or
npm install eth-saddle --save
```

## Using Saddle

After you've installed saddle, you can compile, test or deploy your contracts. See `saddle help` for more information. See `configuration` below for configuration help.

Let's assume you define a simple Ethereum contract:

`contracts/MyContract.sol`

```javascript
pragma solidity ^0.5.9;

contract MyContract {
    function myFunc() pure external returns (uint256) {
        return 55;
    }
}
```

Now, you can compile your contract with:

```bash
npx saddle compile
```

`saddle compile` will compile your contracts and store the built output in `./build/contracts.json`. This is the raw output of solc compile.

After you've compiled, you can deploy your contracts:

```bash
npx saddle deploy -n development
```

This will deploy your comiled contracts to development (for this, you should have [ganache](https://github.com/trufflesuite/ganache) running). For more information on configuring your deployment for rinkeby or mainnet, see the configuration section below.

After you have deployed, you will also see the contract address listed in `./build/development.json`, if you want to keep track of your deployments.

## Testing Smart Contracts

Note: before testing, you currently have to compile your contracts. To run your tests, then, run:

```bash
npx saddle compile && npx saddle test
```

`saddle test` runs your tests. To add tests, create a directory `/tests` and add some simple tests, e.g.

`tests/MyTest.js`

```javascript
describe('View', () => {
  test('deploy and read contract', async () => {
    let ctx = await deploy('MyContract', []);

    expect(await call(ctx.methods.myFunc())).toEqual(55);
  });
}
```

Saddle provides a few helper functions for your tests, which are:

* `web3` - A web3 instance connected to the chosen provider
* `account` - The default account for web3
* `accounts` - A list of unlocked accounts for web3
* `deploy(contract: string, args: any[], sendOptions: SendOptions={})` - Deploys a contract
* `call(callable, callOptions: CallOptions={})` - Call a function on a contract
* `send(sendable, sendOptions: SendOptions={})` - Send a transaction on a contract

You can really get by without using most of these functions (except maybe deploy), since they are light wrappers around web3 functions, but the wrappers will allow saddle to provide better helpers and diagnostics in the future.

## Configuration

Saddle comes with reasonable default configuration, but you can override it. The core of the configuration is a list of "sources" for any given configuration-item, allowing the framework to look at say an environment variable for a provider, or if that is missing, a file with the provider information, or if that is missing, use a default http endpoint. This would be described as:

```javascript
...
provider: [{env: "PROVIDER"}, {file: "~/.ethereum-provider"}, {http: "http://rinkeby.infura.io"}]
```

To set your own configuration, simply run: `saddle init` and this will create a file `saddle.config.js`. Make any changes to the configuration you need in that file

## Contributing

Please create an issue for any questions. In the interest of keeping saddle as simple as possible, we will not try to mimic all features of more complex frameworks-- so it's better to discuss first before submitting PRs.

Thanks and enjoy!
