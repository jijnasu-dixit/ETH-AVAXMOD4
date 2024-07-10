---

# Degen Token (DGN)

This repository contains the implementation of the Degen Token (DGN) smart contract, a simple ERC20 token with additional functionalities for minting, burning, and transferring tokens. The contract is designed to be deployed on the Avalanche network.

## Contract Overview

The `Points` contract inherits from OpenZeppelin's ERC20 and Ownable contracts. The key features of the contract include:

- **Minting**: Only the owner of the contract can mint new tokens.
- **Decimals**: The token does not use decimals (decimals are set to 0).
- **Balance Query**: Users can check their token balance.
- **Token Transfer**: Users can transfer tokens to another address.
- **Token Burn**: Users can burn (destroy) their tokens.
- **Redeem**: Users can redeem tokens from another address.

## Smart Contract

Here is the complete code for the Degen Token contract:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.3.0/contracts/token/ERC20/ERC20.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.3.0/contracts/access/Ownable.sol";

contract Points is ERC20, Ownable {
    constructor() ERC20("Degen", "DGN") {}

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    function decimals() override public pure returns (uint8) {
        return 0;
    }

    function getBalance() external view returns (uint256) {
        return this.balanceOf(msg.sender);
    }

    function transferTokens(address _receiver, uint256 _value) external {
        require(balanceOf(msg.sender) >= _value, "You do not have enough Degen Tokens");
        approve(msg.sender, _value);
        transferFrom(msg.sender, _receiver, _value);
    }

    function burnTokens(uint _value) external {
        require(balanceOf(msg.sender) >= _value, "You do not have enough Degen tokens");
        _burn(msg.sender, _value);
    }

    function redeem(address _from, uint _amount) external {
        transferFrom(_from, msg.sender, _amount);
    }
}
```

## Deployment on Avalanche

To deploy this contract on the Avalanche network, follow the steps below:

### Prerequisites

1. **Node.js and npm**: Ensure you have Node.js and npm installed.
2. **Hardhat**: Install Hardhat, a development environment for Ethereum-compatible blockchains.

```bash
npm install --save-dev hardhat
```

3. **Avalanche Wallet**: Create an Avalanche wallet and obtain some AVAX for gas fees.
4. **Infura or Alchemy**: Set up an Infura or Alchemy account to connect to the Avalanche network.

### Setup Hardhat Project

1. Initialize a Hardhat project:

```bash
npx hardhat
```

Follow the prompts to create a new Hardhat project.

2. Install OpenZeppelin Contracts:

```bash
npm install @openzeppelin/contracts
```

3. Create a deployment script (`scripts/deploy.js`):

```javascript
async function main() {
    const [deployer] = await ethers.getSigners();
    console.log("Deploying contracts with the account:", deployer.address);

    const balance = await deployer.getBalance();
    console.log("Account balance:", balance.toString());

    const Points = await ethers.getContractFactory("Points");
    const points = await Points.deploy();
    console.log("Contract deployed to address:", points.address);
}

main()
    .then(() => process.exit(0))
    .catch((error) => {
        console.error(error);
        process.exit(1);
    });
```

### Configure Network Settings

1. Update `hardhat.config.js` with Avalanche network settings:

```javascript
require("@nomiclabs/hardhat-waffle");

module.exports = {
  solidity: "0.8.9",
  networks: {
    avalanche: {
      url: `https://api.avax.network/ext/bc/C/rpc`,
      accounts: [`0x${YOUR_PRIVATE_KEY}`]
    }
  }
};
```

Replace `YOUR_PRIVATE_KEY` with the private key of your Avalanche wallet.

### Deploy the Contract

1. Compile the contract:

```bash
npx hardhat compile
```

2. Deploy the contract to the Avalanche network:

```bash
npx hardhat run scripts/deploy.js --network avalanche
```

After running the deployment script, you should see the contract address where your `Points` contract is deployed on the Avalanche network.

## Interacting with the Contract

Once deployed, you can interact with your contract using Hardhat tasks, scripts, or a frontend application. For instance, you can create additional scripts to call the `mint`, `burnTokens`, and other functions of your contract.

## License

This project is licensed under the MIT License.

---
