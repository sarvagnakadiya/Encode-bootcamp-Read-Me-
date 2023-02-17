# Lesson 18 - Gas limit and loops

## Gas limit and loops

* (Review) Block, block size and gas
* Gas limit network variable
* Transaction stack
* Optimization patterns
* O(n) versus N*O(1)

### References
<https://dl.acm.org/doi/10.1145/3324884.3416626>

<https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad>

## Creating a big transaction

* Converting strings to bytes
* Comparing strings
* Dealing with bytes arrays
* Loop syntaxes
* Hitting gas limit

### Code references

Contract:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract SortedBallot {
        struct Proposal {
            bytes32 name;
            uint256 voteCount;
        }

        Proposal[] public proposals;

        constructor(bytes32[] memory proposalNames) {
            for (uint256 i = 0; i < proposalNames.length; i++) {
                proposals.push(Proposal({name: proposalNames[i], voteCount: 0}));
            }
        }

        function sortProposals() public {
            uint256 i = 1;
            uint256 sorted = 0;
            while (true) {
                Proposal memory prevObj = proposals[i - 1];
                if (uint256(prevObj.name) > uint256(proposals[i].name)) {
                    proposals[i - 1] = proposals[i];
                    proposals[i] = prevObj;
                    sorted++;
                }
                i++;
                if (i >= proposals.length) {
                    if (sorted == 0) break;
                    sorted = 0;
                    i = 1;
                }
            }
        }
    }

Script:

    import { ethers } from "hardhat";
    import words from "random-words";
    import { SortedBallot } from "../typechain-types";

    const HARDCODED_SAFE_LOOP_LIMIT = 1000;
    const BLOCK_GAS_LIMIT = 30000000;

    function convertStringArrayToBytes32(array: string[]) {
      const bytes32Array = [];
      for (let index = 0; index < array.length; index++) {
        bytes32Array.push(ethers.utils.formatBytes32String(array[index]));
      }
      return bytes32Array;
    }

    async function main() {
      const ballotFactory = await ethers.getContractFactory("SortedBallot");
      let wordCount = 100;
      try {
        for (let loop = 0; loop < HARDCODED_SAFE_LOOP_LIMIT; loop++) {
          console.log(`Loop ${loop}: Testing with ${wordCount} words`);
          const proposals = words({ exactly: wordCount });
          const ballotContract: SortedBallot = await ballotFactory.deploy(
            convertStringArrayToBytes32(proposals)
          );
          await ballotContract.deployed();
          console.log("Sorting proposals");
          const sortTx = await ballotContract.sortProposals();
          console.log("Awaiting confirmations");
          const sortReceipt = await sortTx.wait();
          console.log("Completed");
          const percentUsed = sortReceipt.gasUsed
            .mul(100)
            .div(BLOCK_GAS_LIMIT)
            .toNumber();
          console.log(
            `${sortReceipt.gasUsed} units of gas used at ${ethers.utils.formatUnits(
              sortReceipt.effectiveGasPrice,
              "gwei"
            )} GWEI effective gas price, total of ${ethers.utils.formatEther(
              sortReceipt.effectiveGasPrice.mul(sortReceipt.gasUsed)
            )} ETH spent. This used ${percentUsed} % of the block gas limit`
          );
          const props = [];
          for (let index = 0; index < wordCount; index++) {
            const prop = await ballotContract.proposals(index);
            props.push(ethers.utils.parseBytes32String(prop.name));
          }
          console.log(`Passed ${wordCount} proposals:`);
          console.log(proposals.join(", "));
          console.log("Sorted proposals: ");
          console.log(props.join(", "));
          wordCount +=
            percentUsed > 95
              ? 1
              : percentUsed > 90
              ? 2
              : percentUsed > 75
              ? 10
              : percentUsed > 50
              ? 25
              : percentUsed > 30
              ? 30
              : 100;
        }
      } catch (error) {
        console.log(
          `Congratulations! You broke the block limit while sorting ${wordCount} words in a Smart Contract`
        );
        console.log({ error });
      }
    }

    main().catch((error) => {
      console.error(error);
      process.exitCode = 1;
    });

### References
<https://docs.soliditylang.org/en/latest/types.html#bytes-and-string-as-arrays>

## Fixing the contract

* Loop best practices
* Refactoring for N*O(1)
* Keeping track of operation progress
* Iteration until operation is completed
* Backups and safeguards
* Avoiding loops

### Code references

Contract:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract CorrectSortedBallot {
        struct Proposal {
            bytes32 name;
            uint256 voteCount;
        }

        Proposal[] public proposals;
        Proposal[] public proposalsBeingSorted;
        uint256 public swaps;
        uint256 public sortedWords;
        uint256 public savedIndex;

        constructor(bytes32[] memory proposalNames) {
            for (uint256 i = 0; i < proposalNames.length; i++) {
                proposals.push(Proposal({name: proposalNames[i], voteCount: 0}));
            }
            savedIndex = 1;
            proposalsBeingSorted = proposals;
        }

        function restartSorting() public {
            swaps = 0;
            sortedWords = 0;
            savedIndex = 1;
            proposalsBeingSorted = proposals;
        }

        function sortProposals(uint256 steps) public returns (bool) {
            uint256 step = 0;
            while (sortedWords < proposalsBeingSorted.length) {
                if (step >= steps) return (false);
                // TODO
                step++;
            }
            proposals = proposalsBeingSorted;
            return (true);
        }

        function sorted() public view returns (bool isSorted) {
            isSorted = sortedWords == proposals.length;
        }
    }

Script:

    import { ethers } from "hardhat";
    import words from "random-words";
    import { CorrectSortedBallot } from "../typechain-types";

    const BLOCK_GAS_LIMIT = 30000000;
    const WORD_COUNT = 300;
    const STEP_SIZE = 5000;

    function convertStringArrayToBytes32(array: string[]) {
      const bytes32Array = [];
      for (let index = 0; index < array.length; index++) {
        bytes32Array.push(ethers.utils.formatBytes32String(array[index]));
      }
      return bytes32Array;
    }

    async function main() {
      const ballotFactory = await ethers.getContractFactory("CorrectSortedBallot");
      const proposals = words({ exactly: WORD_COUNT });
      const ballotContract: CorrectSortedBallot = await ballotFactory.deploy(
        convertStringArrayToBytes32(proposals)
      );
      await ballotContract.deployed();
      let completed = false;
      while (!completed) {
        console.log("Sorting proposals");
        const sortTx = await ballotContract.sortProposals(STEP_SIZE);
        console.log("Awaiting confirmations");
        const sortReceipt = await sortTx.wait();
        console.log("Operation completed");
        const percentUsed = sortReceipt.gasUsed
          .mul(100)
          .div(BLOCK_GAS_LIMIT)
          .toNumber();
        console.log(
          `${sortReceipt.gasUsed} units of gas used at ${ethers.utils.formatUnits(
            sortReceipt.effectiveGasPrice,
            "gwei"
          )} GWEI effective gas price, total of ${ethers.utils.formatEther(
            sortReceipt.effectiveGasPrice.mul(sortReceipt.gasUsed)
          )} ETH spent. This used ${percentUsed} % of the block gas limit`
        );
        const [sortedWords, savedIndex, swaps] = await Promise.all([
          ballotContract.sortedWords(),
          ballotContract.savedIndex(),
          ballotContract.swaps(),
        ]);
        const props = [];
        for (let index = 0; sortedWords.gt(index); index++) {
          const prop = await ballotContract.proposalsBeingSorted(index);
          props.push(ethers.utils.parseBytes32String(prop.name));
        }
        console.log(
          `So far it has sorted ${sortedWords} words. Currently at position ${savedIndex}, where the current loop found ${swaps} words out of place `
        );
        completed = await ballotContract.sorted();
        console.log(
          `The sorting process has${completed ? " " : " not "}been completed`
        );
        if (completed) {
          console.log(`Passed ${WORD_COUNT} proposals:`);
          console.log(proposals.join(", "));
          console.log(`Sorted ${sortedWords} proposals: `);
          console.log(props.join(", "));
        }
      }
    }

    main().catch((error) => {
      console.error(error);
      process.exitCode = 1;
    });

### References

<https://github.com/wissalHaji/solidity-coding-advices/blob/master/best-practices/be-careful-with-loops.md>

---

## Homework

* Create Github Issues with your questions about this lesson
* Read the references
