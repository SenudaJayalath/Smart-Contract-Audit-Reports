# Nouns DAO Findings & Analysis Report

# Table of Contents
1. [Overview](#Overview)
2. [Scope](#Scope)
3. [Medium Risk Findings](#Medium-Risk-Findings)
## Overview
Nouns is a generative non-fungible token project on the Ethereum blockchain. Nouns are 32x32 pixel characters based on people, places, and things. The official website of Nouns can be found [here](https://nouns.wtf/). The audit took place between July 3—July 13, 2023

## Scope

The code under review can be found within the [C4 Nouns DAO repository](https://github.com/code-423n4/2023-07-nounsdao), and is composed of 33 smart contracts written in the Solidity programming language and includes 9,098 lines of Solidity code.

## Medium Risk Findings

### Issue 01

Assume a scenario where a user calls `withdrawFromForkEscrow` function and passes in an array of tokens, where the last one is not owned by him/her. This will lead to the Escrow transferring the first set of tokens back to the user(which are owned by the caller) and the function getting reverted on the last tokenID, without the `numTokensInEscrow` getting decremented.

Then the actual number of tokens in the Escrow and the value of `numTokensInEscrow` will be different

##### **Proof of Concept**
![alt text](https://i.ibb.co/9bQpN6F/Screenshot-2023-07-09-at-21-39-59.png)

In the above piece of code, in the `for` loop, if any token is not owned by the `owner` an error is thrown and the function reverts. Since you are providing an array of tokens to be transferred, if by any chance a non existing tokenID or a tokenID owned by another person is added to the list, this function would revert. At the time of reverting, if any tokens had been transferred, you cannot get it back and the `numTokensInEscrow` won't be updated as it is done after successfully iterating through the `for` loop, which in this case won't happen.

#### **Tools Used**
VS Code, manual inspection

#### **Recommended Mitigation Steps**

First check if all the tokenIDs do exist and is owned by the owner before transferring any of the tokens
