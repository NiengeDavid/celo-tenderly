# How to Debug and Deploy Solidity Smart Contracts on Celo Using Hardhat and Tenderly

## Table of Contents
- [How to Debug and Deploy Solidity Smart Contracts on Celo Using Hardhat and Tenderly](#how-to-debug-and-deploy-solidity-smart-contracts-on-celo-using-hardhat-and-tenderly)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [Requirements](#requirements)
  - [Understanding the Celo Blockchain](#understanding-the-celo-blockchain)
  - [Setting up the Environment](#setting-up-the-environment)
  - [Writing the Smart Contract](#writing-the-smart-contract)
  - [Testing the Smart Contract on Celo using Hardhat](#testing-the-smart-contract-on-celo-using-hardhat)
  - [Debugging with Tenderly](#debugging-with-tenderly)
    - [What is Tenderly](#what-is-tenderly)
    - [How to start debugging with tenderly](#how-to-start-debugging-with-tenderly)
  - [Deploying the Smart Contract on Celo using Hardhat](#deploying-the-smart-contract-on-celo-using-hardhat)
  - [Conclusion](#conclusion)

## Introduction

Solidity is a programming language used to write smart contracts on blockchain platforms like Ethereum and Celo. Writing and deploying a Solidity smart contract can be a challenging task, especially for beginners. Debugging Solidity smart contracts is also a crucial task as it can prevent potential vulnerabilities and errors in the contract.

Hardhat and Tenderly are two popular tools that help developers in the development, deployment, and monitoring of smart contracts. 
Hardhat is an open-source development environment that provides a comprehensive set of tools for building, testing, and deploying smart contracts on Celo. Tenderly, on the other hand, is a blockchain monitoring and debugging platform that allows developers to track the performance of their smart contracts and identify any issues.

In this tutorial, I will walk you through the process of deploying, debugging, and monitoring a Solidity smart contract on Celo using Hardhat and Tenderly. By the end of this tutorial, you will have a good understanding of the development process and best practices for building and deploying Solidity smart contracts on Celo.

## Prerequisites

- Basic knowledge of Solidity
- Basic understanding of Celo, and 
- JavaScript programming skills.

## Requirements

1. Node.js: Install the latest version of Node.js from the official **[website](https://nodejs.org/)**.
2. NPM: NPM is the package manager for Node.js. You can install it by running the command `npm install npm@latest -g` in your terminal.
3. Hardhat: Install Hardhat globally by running the command `npm install -g hardhat` in your terminal.
4. Celo Extension Wallet: Install the Celo Extension Wallet from the Chrome Web **[Store](https://chrome.google.com/webstore/detail/celo-extension-wallet/knlhknnkjdlbdgpkacnchnllnflofpml)**.
5. Tenderly: Sign up for Tenderly **[here](https://dashboard.tenderly.co/signup)**.

## Understanding the Celo Blockchain

The Celo blockchain is a decentralized, open-source blockchain platform that enables users to build and run decentralized applications (dApps) and smart contracts. It is specifically designed to facilitate the development of mobile-first applications that can be used by people without access to traditional financial services.

Celo aims to create a more inclusive financial system by providing a decentralized platform for sending and receiving payments, issuing stablecoins, and building other financial applications. The platform is built on a Proof of Stake (PoS) consensus algorithm, which allows for more efficient transaction processing and lower fees compared to traditional Proof of Work (PoW) blockchains.

One of the key features of the Celo blockchain is its focus on mobile-first design, which means that the platform is optimized for use on mobile devices and is accessible to users with low-end smartphones and limited internet connectivity. This makes it a powerful tool for promoting financial inclusion and providing access to financial services to people in developing countries and other underserved communities.

It is safe to say that the Celo blockchain represents an exciting new frontier in the world of decentralized finance (DeFi), and its focus on mobile-first design and financial inclusion make it a unique and valuable addition to the blockchain ecosystem.

## Setting up the Environment

Now, you will set up the environment for building, testing, and deploying your smart contract on Celo using Hardhat.

1. Create a new directory for your project and navigate into it.

```bash
    mkdir my-project
    cd my-project
```
2. Initialize the project with NPM by running the command npm init -y.

```bash
    npm init -y
```

3. Install the required packages by running the following command.

```bash
   npm install --save-dev hardhat @nomiclabs/hardhat-waffle \
@nomiclabs/hardhat-celo @celo-tools/celo-testkit
```

4. Create a Hardhat configuration file by running the command `npx hardhat`.

```bash
    npx hardhat
```
5. Select "Create a sample project" and choose the default configuration.

```bash
    What do you want to do? » Create a sample project
    Select a starter kit » Empty
    Do you want to add a .gitignore? » Yes
    Successfully created Hardhat project my-project
```

6. Open the `hardhat.config.js` file in your favorite code editor and add the following code.

```javascript
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-celo");
require("@celo-tools/celo-testkit");

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.0",
  networks: {
    hardhat: {},
    celo: {
      url: "https://forno.celo.org",
      chainId: 42220,
      gasPrice: 1000000000,
      accounts: {
        mnemonic: "your-mnemonic"
      }
    }
  },
  mocha: {
    timeout: 20000
  }
};
```

Here, you are importing the necessary plugins for Hardhat, including `@nomiclabs/hardhat-waffle`, `@nomiclabs/hardhat-celo`, and `@celo-tools/celo-testkit`. You will then define the Solidity version we will be using and specify two networks: `hardhat` and `celo`.

The `hardhat` network is the default network used for local development and testing. The `celo` network is used for deploying the contract to the Celo network. We have specified the `url`, `chainId`, `gasPrice`, and `accounts` for the `celo` network.

Note that you will need to replace the mnemonic with your own mnemonic.

## Writing the Smart Contract

In this section, you will write a simple Solidity smart contract that you will be deploying and testing in the following sections. Your smart contract will be a basic ERC20 token contract with the following functionality:

- Minting new tokens
- Transferring tokens between accounts
- Retrieving the balance of an account

Create a new file called `Token.sol` in the `contracts` directory with the following code.

```solidity
// SPDX-License-Identifier: MIT
/**
 * @title Token
 * @dev ERC20 token contract that implements a custom token named MyToken (MTK).
 **/
pragma solidity ^0.8.0;

// Visit https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.0.0/contracts/token/ERC20/ERC20.sol for full code
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
// Visit https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.0.0/contracts/utils/math/SafeMath.sol for full code 
import "@openzeppelin/contracts/utils/math/SafeMath.sol";


contract Token is ERC20 {
    using SafeMath for uint256;

    event Mint(address indexed to, uint256 amount);
    event Transfers(address indexed from, address indexed to, uint256 amount);

    /**
     * @dev Constructor that mints 1,000,000 MyToken (MTK) tokens to the contract deployer.
    **/
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000000 * 10 ** decimals());
    }

    /**
     * @dev Mints a specified amount of MyToken (MTK) tokens to the specified recipient address.
     * @param to - The address of the recipient to receive the tokens.
     * @param amount - The amount of tokens to mint and transfer to the recipient.
     * Requirements:
     * - `to` cannot be the zero address.
     * - `amount` must be greater than zero.
     * Emits a [Mint] event.
     * Emits a [Transfer] event.
    **/
    function mint(address to, uint256 amount) public {
        require(to != address(0), "Invalid recipient address");
        require(amount > 0, "Invalid amount");

        _mint(to, amount);
        emit Mint(to, amount);
        emit Transfers(address(0), to, amount);
    }

    /**
     * @dev Returns - the balance of MyToken (MTK) tokens held by the specified account.
     * @param account - The address of the account to check the balance of.
     * @return accountbalance - The balance of MyToken (MTK) tokens held by the specified account.
    **/
    function balanceOf(address account) public view override returns (uint256 accountbalance) {
        return super.balanceOf(account);
    }
}

```

Let me walk you through the code line by line:

```solidity
    import "@openzeppelin/contracts/token/ERC20/ERC20.sol
```

This is an import statement that imports the `ERC20` contract from the `@openzeppelin/contracts/token/ERC20` package. The `ERC20` contract is a standard implementation of the ERC-20 token standard.

```solidity
    contract Token is ERC20
```

This is a contract declaration statement. It declares a new contract named `Token` that extends the `ERC20` contract.

```solidity
    constructor() ERC20("MyToken", "MTK")
````

This is a constructor function that is called when the contract is deployed. It calls the constructor of the `ERC20` contract, passing in the token name "MyToken" and symbol "MTK". The `_mint` function is then called to mint 1,000,000 tokens and assign them to the contract deployer.

```solidity
    _mint(msg.sender, 1000000 * 10 ** decimals())
```
This is an internal function that mints a specified amount of tokens and assigns them to a specified address. In this case, it is being called to mint 1,000,000 tokens and assign them to the `msg.sender`, which is the address that deployed the contract.

```solidity
    function mint(address to, uint256 amount) public {
        require(to != address(0), "Invalid recipient address");
        require(amount > 0, "Invalid amount");

        _mint(to, amount);
        emit Mint(to, amount);
        emit Transfers(address(0), to, amount);
    }

    function balanceOf(address account) public view override returns (uint256 accountbalance) {
        return super.balanceOf(account);
    }
```
These are three function declarations that define additional behavior for the `Token` contract:

- `mint`: This function can be called by anyone and can be used to mint new tokens and assign them to a specified address.
- `balanceOf`: This function returns the token balance of a specified address. It overrides the implementation of the `balanceOf` function in the `ERC20` contract, but simply calls `super.balanceOf` to retrieve the balance. This is an example of function overriding in Solidity.

You are importing the ERC20 contract from the OpenZeppelin library, which provides a standard implementation of the ERC20 token interface.


## Testing the Smart Contract on Celo using Hardhat

In this section, you will write tests to ensure that the Token contract works as intended.

1. Create a new file called token.test.js in the test directory with the following code.

```javascript
const { expect } = require("chai");

describe("Token contract", function () {
  let Token;
  let token;
  let owner;
  let addr1;
  let addr2;
  const initialSupply = ethers.utils.parseUnits("1000000", 18);

  beforeEach(async function () {
    [owner, addr1, addr2] = await ethers.getSigners();
    Token = await ethers.getContractFactory("Token");
    token = await Token.connect(owner).deploy();
    await token.deployed();
  });

  describe("Deployment", function () {
    it("Should set the right owner", async function () {
      expect(await token.owner()).to.equal(owner.address);
    });

    it("Should assign the total supply of tokens to the owner", async function () {
      const ownerBalance = await token.balanceOf(owner.address);
      expect(ownerBalance).to.equal(initialSupply);
    });
  });

  describe("Minting", function () {
    it("Should mint new tokens", async function () {
      const mintAmount = ethers.utils.parseUnits("1000", 18);
      await token.connect(owner).mint(addr1.address, mintAmount);
      const addr1Balance = await token.balanceOf(addr1.address);
      expect(addr1Balance).to.equal(mintAmount);
    });
  });

  describe("Transferring", function () {
    it("Should transfer tokens between accounts", async function () {
      const transferAmount = ethers.utils.parseUnits("100", 18);
      await token.connect(owner).transfer(addr1.address, transferAmount);
      const addr1Balance = await token.balanceOf(addr1.address);
      expect(addr1Balance).to.equal(transferAmount);

      await token.connect(addr1).transfer(addr2.address, transferAmount);
      const addr2Balance = await token.balanceOf(addr2.address);
      expect(addr2Balance).to.equal(transferAmount);
    });
  });
});
```

In the code above, you are using `chai` to write tests for our `Token` contract. You first defined some variables, including the contract owner `owner`, `addr1`, and `addr2`, and the initial supply of tokens (`initialSupply`).

In the `beforeEach` function, you retrieve the contract factory and deploy a new instance of the contract using the owner's account.

You then have three `describe` blocks: one for testing the deployment, one for testing minting new tokens, and one for testing transferring tokens between accounts.

In the first `it` block, you are testing that the owner is set correctly when the contract is deployed.

In the second `it` block, you test that the total supply of tokens is assigned to the owner when the contract is deployed.

In the third it block, you test that new tokens can be minted and assigned to an account.

In the fourth it block, wyoue test that tokens can be transferred between accounts.

2. Run the tests by running the following command in the terminal.

```bash
    npx hardhat test
```

This will run the tests you just wrote and output the results to the console.

## Debugging with Tenderly

### What is Tenderly
Tenderly is a platform for debugging, monitoring, and analyzing smart contracts on Ethereum. It provides a suite of tools that allow developers to test and verify their smart contracts' functionality and performance in different scenarios.

To use Tenderly for debugging a smart contract, a developer can create a project and connect it to their Ethereum account or network. They can then deploy their smart contract to the network and use Tenderly's tools to monitor its activity and behavior. For example, developers can use Tenderly's transaction simulator to simulate different transactions and see how the contract reacts. They can also use Tenderly's debugger to step through the contract code and analyze its behavior in detail.

In addition to debugging, Tenderly also provides tools for monitoring and analyzing smart contracts in production. This includes tracking contract activity, analyzing gas usage, and detecting potential vulnerabilities. By using Tenderly, developers can gain greater visibility into their smart contracts and ensure that they are functioning as intended.

### How to start debugging with tenderly

1. Create a new account on Tenderly **[here](https://dashboard.tenderly.co/signup).**

2. Create a new project on Tenderly.

3. Edit the hardhat.config.js file and add the following code:

```javascript
require('@nomiclabs/hardhat-celo')

const { PRIVATE_KEY } = process.env
const { TENDERLY_PROJECT_ID, TENDERLY_USERNAME, TENDERLY_API_KEY } = process.env

require('@tenderly/hardhat-tenderly')({
  username: TENDERLY_USERNAME,
  project: TENDERLY_PROJECT_ID,
  apiKey: TENDERLY_API_KEY
})

module.exports = {
  networks: {
    celo: {
      url: 'https://forno.celo.org',
      accounts: [PRIVATE_KEY]
    }
  },
  solidity: {
    version: '0.8.4',
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
}
```
This configuration file now includes the Tenderly integration. You will need to add your Tenderly project ID, username, and API key as environment variables.
4. Initialize the Tenderly CLI and link it to your project.

```bash
    npm install -g @tenderly/cli
```

Next, navigate to your project directory and log in to Tenderly:

```bash
    tenderly login
```

Follow the prompts to log in with your Tenderly account.

Then, create a new project on Tenderly and copy the project ID from the URL. Set the `TENDERLY_PROJECT_ID` environment variable to this ID:

```bash
    export TENDERLY_PROJECT_ID=<your project ID>
```

Finally, link your Hardhat project to your Tenderly project using the Tenderly CLI:

```bash
    npx hardhat tenderly link <your username>/<your project name>
```

This will create a new fork of the Celo blockchain and deploy your contract to it. You can now use Tenderly to debug your contract on this forked chain.

## Deploying the Smart Contract on Celo using Hardhat

Next, you will deploy the Token smart contract to the Celo network using Hardhat.

1. Compile the contract by running the following command.

```solidity
    npx hardhat compile
```

2. Create a new deployment script called `deploy.js` in the `scripts` directory with the following code.

```javascript
const { ethers } = require("hardhat");

async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account:", deployer.address);

  const Token = await ethers.getContractFactory("Token");
  const token = await Token.deploy();

   // The contract is NOT deployed yet; we must wait until it is mined
  await token.deployed();

  console.log("Token deployed to:", token.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

Here, you are using `ethers` to deploy the `Token` contract to the `celo` network. You first retrieve the deployer's account, and then use `ethers.getContractFactory `to get the contract factory for `Token`.

You then deploy the contract using `token.deploy()`, and output the transaction hash to the console. You must wait for the transaction to be mined and the contract to be deployed before you can interact with it, so you use `token.deployed()` to wait for this to happen.

3. Deploy the contract to the Celo network by running the following command.

```bash
    npx hardhat run scripts/deploy.js --network celo
```

This will deploy the `Token` contract to the Celo network using the account specified in the `hardhat.config.js` file.

## Conclusion

Solidity smart contracts are a powerful tool for building decentralized applications on the Celo blockchain. However, like any software development process, building and deploying smart contracts can be challenging and require careful testing and debugging to ensure their correct functionality and security.

In this tutorial, we have covered the basics of Solidity smart contract development, using Hardhat and Tenderly to deploy and debug a sample ERC-20 token contract. We have walked through the step-by-step process of setting up a local development environment, compiling and deploying the contract, and debugging it using Tenderly.

We have also covered best practices for Solidity smart contract development, such as testing contracts using automated test scripts and auditing code for security vulnerabilities.

By following the steps outlined in this tutorial and continuing to learn and explore the Solidity language and the Celo blockchain, you can create powerful, secure, and reliable decentralized applications that can benefit users around the world.





