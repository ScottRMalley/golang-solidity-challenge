# Golang-Solidity Challenge

## Introduction

The goal of this challenge is to gauge skills in Solidity smart contracts and interaction with Solidity smart contracts in Golang. The idea is to create a simple set of smart contracts and a Golang service to interact with them.

Important points:

* **Document your decisions**: your solution should include a README that justifies the choice of any frameworks, libraries, or design decisions you used to solve the problem
* **Unit tests**: testing in both Solidity and Golang is an essential part of any solution
* **Keep it simple**: you don't need to design a production system, so don't worry about setting up a database or complicated messaging systems. Do, however, create a solution that would later be extendible with any features you forsee being important for future production development.
* **Try to minimize gas fees**: first try to get a working solution, and if you have extra time, consider playing around with your contract to get the minimal gas fees necessary on deposits and withdraws.//

## The Challenge

We want to create a smart contract where users can deposit and withdraw their FUD tokens. We will then airdrop WIN tokens every X minutes to each user who has had FUD tokens deposited during this interval. The amount of airdropped tokens is equal to 5% of the average FUD token deposit over the last X blocks (X should be configurable).

```
# WIN tokens in airdrop = 0.05 * (# FUD tokens deposited) * (# blocks deposited) / ( total # blocks)
```

Every deposit or withdraw of a FUD token should emit an event that can be picked up by our Golang backend. The event should contain the number of FUD tokens depositied or withdrawn, and the address of the depositer (you can also include any other information you'd like in the event, such as the new deposit balance).

The Golang backend should monitor the on-chain contracts for any events, and airdrop the WIN tokens to the depositer's address every X blocks.

Overall, there should be three Solidity contracts with the following interfaces. The token contracts should both implement the standard ERC20 interface defined [here](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/).

```
pragma solidity ^0.8.13

interface FudToken {
	// ... standard ERC20 interface
}

interface WinToken {
	// ... standard ERC20 interface
	
	// the WIN token is also mintable, so we include the following with the onlyMinter modifier
	function mint(address account, uint256 amount) public returns(bool);
}

interface AirVault {
	deposit(uint256 amount) public returns(bool);
	withdraw(uint256 amount) public returns(bool);
	balanceOf(address account) external view returns(uint256);
}
```

The Golang backend should have control of a private key corresponding to the minter role on the WIN token. This will allow it to airdrop tokens to the deposit addresses after the block interval.

For the moment, do not worry about implementing proper key management in the Golang backend. However, do consider how it would be implemented, and add a description in the README for how your code would need to be updated for proper protection of the private minter key.