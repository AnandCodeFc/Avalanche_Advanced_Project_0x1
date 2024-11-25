# Metacrafters Avalanche Advanced Course - Avalanche Subnet

## Table of Contents
1. [Introduction](#avalanche-blockchain-introduction)
2. [Avalanche Subnet](#avalanche-subnet)
3. [Avalanche Subnet Setup](#avalanche-subnet-setup)
4. [Smart Contracts](#smart-contracts)
   - [ERC20 Token Contract](#erc20-token-contract)
   - [Vault Contract](#vault-contract)

---

## Avalanche Blockchain Introduction
Avalanche is a high-performance, scalable, and customizable blockchain platform that supports decentralized applications and enterprise blockchain deployments. It introduces unique consensus mechanisms and allows developers to create and deploy custom blockchains or subnets tailored to specific use cases.

---

## Avalanche Subnet
Avalanche subnets are independent, interoperable networks within the Avalanche ecosystem. They enable developers to create custom blockchain environments with specific rules, governance, and economics. Subnets can be public or private and allow for flexibility in the design of decentralized applications and tokenomics.

### Key Features
- **Customizable Consensus:** Subnets can implement unique consensus algorithms.
- **Interoperability:** Subnets interact seamlessly with the Avalanche Primary Network.
- **Scalability:** Subnets reduce congestion and improve performance.

---

## Avalanche Subnet Setup
To set up an Avalanche subnet:
1. Install the Avalanche-CLI tool.
2. Configure the subnet parameters (e.g., consensus, tokenomics, validators).
3. Deploy the subnet on the Avalanche network.
4. Integrate smart contracts and applications within the subnet.

For detailed instructions, refer to the [Avalanche Developer Documentation](https://docs.avax.network/).

---

## Smart Contracts
This demonstration includes two Solidity smart contracts:
1. An ERC20 Token Contract.
2. A Vault Contract for managing token deposits and withdrawals.

### ERC20 Token Contract
This contract implements a basic ERC20 token with functionalities for minting, burning, transferring, and approving tokens.

#### Key Features
- **Minting:** Create new tokens and add them to the total supply.
- **Burning:** Remove tokens from circulation.
- **Transfer:** Enable token holders to transfer tokens.
- **Approval:** Allow third-party addresses to spend tokens on behalf of the owner.

#### Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Solidity by Example";
    string public symbol = "SOLBYEX";
    uint8 public decimals = 18;

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address sender, address recipient, uint amount) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

---

### Vault Contract
The Vault contract allows users to deposit and withdraw ERC20 tokens. It manages shares representing ownership of the deposited tokens.

#### Key Features
- **Deposit:** Convert token amounts into shares and store them in the Vault.
- **Withdraw:** Redeem shares for the underlying token amount.
- **Dynamic Share Calculation:** Calculate shares and token amounts based on the current Vault balance and total supply.

#### Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint amount) external returns (bool);
}

contract Vault {
    IERC20 public immutable token;
    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        uint shares = totalSupply == 0
            ? _amount
            : (_amount * totalSupply) / token.balanceOf(address(this));
        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}
```

---

## Summary 
This demonstration showcases the setup of an Avalanche subnet and the deployment of custom smart contracts. These contracts are foundational for creating decentralized finance (DeFi) applications and other blockchain-based systems.
