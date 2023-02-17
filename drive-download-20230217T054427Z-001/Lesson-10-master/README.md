# Lesson 10 - TokenSale.sol

## Challenge explanation

* Smart Contract Features
  * Buy ERC20 tokens with ETH for a fixed ratio
    * Ratio _r_ means that 1 ETH should buy _r_ tokens
  * Withdraw ETH by burning the ERC20 tokens at the contract
  * Mint a new ERC721 for a configured price
    * Price _p_ means that 1 NFT should cost _p_ tokens
  * Allow users to burn their NFTs to recover half of the purchase price
  * Update owner withdrawable amount whenever a NFT is sold
  * Allow owner to withdraw tokens from the contract
    * Only half of sales value is available for withdraw
* Architecture overview
* Contract external calls

## Tests layout

* (Review) TDD methodology
* Best practices on external calls
* Dealing with decimals and divisions
  * Shifting decimal points
  * Underflow
  * Overflow
* (Review) Test syntax
* (Review) Positive and negative tests
* Integration tests

### References
<https://consensys.github.io/smart-contract-best-practices/development-recommendations/general/external-calls/>

<https://docs.soliditylang.org/en/latest/types.html#division>

<https://github.com/wissalHaji/solidity-coding-advices/blob/master/best-practices/rounding-errors-with-division.md>

### Test code reference

    import { expect } from "chai";
    import { ethers } from "hardhat";

    describe("NFT Shop", async () => {
      beforeEach(async () => {});

      describe("When the Shop contract is deployed", async () => {
        it("defines the ratio as provided in parameters", async () => {
          throw new Error("Not implemented");
        });

        it("uses a valid ERC20 as payment token", async () => {
          throw new Error("Not implemented");
        });
      });

      describe("When a user purchase an ERC20 from the Token contract", async () => {
        beforeEach(async () => {});

        it("charges the correct amount of ETH", async () => {
          throw new Error("Not implemented");
        });

        it("gives the correct amount of tokens", async () => {
          throw new Error("Not implemented");
        });
      });

      describe("When a user burns an ERC20 at the Shop contract", async () => {
        it("gives the correct amount of ETH", async () => {
          throw new Error("Not implemented");
        });

        it("burns the correct amount of tokens", async () => {
          throw new Error("Not implemented");
        });
      });

      describe("When a user purchase a NFT from the Shop contract", async () => {
        it("charges the correct amount of ERC20 tokens", async () => {
          throw new Error("Not implemented");
        });

        it("gives the correct nft", async () => {
          throw new Error("Not implemented");
        });

        it("updates the owner pool account correctly", async () => {
          throw new Error("Not implemented");
        });

        it("update the public pool account correctly", async () => {
          throw new Error("Not implemented");
        });

        it("favors the public pool with the rounding", async () => {
          throw new Error("Not implemented");
        });
      });

      describe("When a user burns their NFT at the Shop contract", async () => {
        it("gives the correct amount of ERC20 tokens", async () => {
          throw new Error("Not implemented");
        });
        it("updates the public pool correctly", async () => {
          throw new Error("Not implemented");
        });
      });

      describe("When the owner withdraw from the Shop contract", async () => {
        it("recovers the right amount of ERC20 tokens", async () => {
          throw new Error("Not implemented");
        });

        it("updates the owner pool account correctly", async () => {
          throw new Error("Not implemented");
        });
      });
    });

### References
<https://fravoll.github.io/solidity-patterns/>

<https://dev.to/jamiescript/design-patterns-in-solidity-1i28>

---

## Homework

* Create Github Issues with your questions about this lesson
* Read the references
