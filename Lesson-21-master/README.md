# Lesson 21 - Part 1: Blockchain scaling solutions

## Scaling overview

* The scalability trilemma
* Layer 1 solutions
* Layer 2 solutions
* Consensus mechanisms for scaling
* Bridges
  * Trusted
  * Trustless

### References

<https://www.gemini.com/cryptopedia/blockchain-trilemma-decentralization-scalability-definition>

<https://learn.bybit.com/deep-dive/blockchain-trilemma/>

<https://www.ledger.com/academy/what-is-the-blockchain-trilemma>

<https://ethereum.org/en/bridges/>

## L2 example: Polygon (Matic)

* Polygon project
* Plasma bridge
* PoS bridge
* Interacting with matic using Ethers.js library
* Matic.js library

### Code reference

    function setupProvider() {
        const rpcUrl = process.env.CUSTOM_RPC_URL_MATIC;
        const provider = new ethers.providers.JsonRpcProvider(rpcUrl);
        return provider;
    }

### References

<https://docs.ethers.io/v5/api/providers/jsonrpc-provider/>

<https://coincentral.com/what-is-polygon-matic-a-guide-on-the-ethereum-layer-two-solution/>

<https://wiki.polygon.technology/docs/develop/getting-started>

<https://docs.polygon.technology/docs/develop/ethereum-polygon/getting-started/>

<https://cryptoslate.com/polygon-matic-to-raise-gas-fee-to-30-gwei-to-prevent-spam-transactions/>

<https://wallet.polygon.technology/>

<https://maticnetwork.github.io/matic.js/docs/get-started/>

## Ethereum Roadmap

* Beacon chain
* The merge
* Sharding

### References

<https://ethereum.org/en/upgrades/>

# Lesson 21 - Part 2: Solidity advanced content

## Encoding and string manipulation

* What is ABI encoding
* Using ABI encoding to manipulate bytes
* Packing and unpacking
* Using ABI encoding to manipulate strings
* Using libraries
  * Solidity Utilities by [_willitscale_](https://github.com/willitscale/solidity-util)
  * String utils by [_Arachnid_](https://github.com/Arachnid/solidity-stringutils)

### Code reference

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    import {Strings} from "./imported/solidity-utils_imported.sol";

    contract StringUtilsTest {
        using Strings for string;
        string public stored1;
        string public stored2;
        bool public stringsEqual;

        function concatenateUsingAbiEncode(string memory s1, string memory s2)
            internal
            pure
            returns (string memory)
        {
            return string(abi.encodePacked(s1, s2));
        }

        function concatenateUsingLibrary(string memory s1, string memory s2)
            internal
            pure
            returns (string memory)
        {
            return s1.concat(s2);
        }

        function concatenateAndSaveUsingAbiEncode(
            string calldata s1,
            string calldata s2
        ) public {
            stored1 = concatenateUsingAbiEncode(s1, s2);
        }

        function concatenateAndSaveUsingLibrary(
            string calldata s1,
            string calldata s2
        ) public {
            stored2 = concatenateUsingLibrary(s1, s2);
        }

        function compareUsingAbiEncode(string storage s1, string storage s2)
            internal
            pure
            returns (bool)
        {
            return
                keccak256(abi.encodePacked(s1)) == keccak256(abi.encodePacked(s2));
        }

        function compareUsingLibrary(string storage s1, string storage s2)
            internal
            pure
            returns (bool)
        {
            return s1.compareTo(s2);
        }

        function compareAndSaveUsingAbiEncode() public {
            stringsEqual = compareUsingAbiEncode(stored1, stored2);
        }

        function compareAndSaveUsingLibrary() public {
            stringsEqual = compareUsingLibrary(stored1, stored2);
        }
    }

### References

<https://docs.soliditylang.org/en/latest/contracts.html#libraries>

<https://docs.soliditylang.org/en/develop/abi-spec.html>

<https://betterprogramming.pub/solidity-playing-with-strings-aca62d118ae5>

## Assembly

* Syntax overview for Yul
* Inline assembly
* Use cases
* Solidity conventions for using inline assembly

### Code references

Contract:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    import {GetCode} from "./imported/get-code.sol";

    contract AssemblyTest {
        using GetCode for address;

        function getThisCode() public view returns (bytes memory code) {
            return address(this).at();
        }
    }

Library:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    library GetCode {
        function at(address addr) public view returns (bytes memory code) {
            assembly {
                // retrieve the size of the code, this needs assembly
                let size := extcodesize(addr)
                // allocate output byte array - this could also be done without assembly
                // by using code = new bytes(size)
                code := mload(0x40)
                // new "memory end" including padding
                mstore(0x40, add(code, and(add(add(size, 0x20), 0x1f), not(0x1f))))
                // store length in memory
                mstore(code, size)
                // actually retrieve the code, this needs assembly
                extcodecopy(addr, add(code, 0x20), 0, size)
            }
        }
    }

### References

<https://docs.soliditylang.org/en/latest/assembly.html>

<https://docs.soliditylang.org/en/latest/yul.html#yul>

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol>

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Create2.sol>

## ECDSA

* Review about ECDSA signature
* The danger of signing messages
* EIP191 and avoiding unintentionally signing a RLP
* ECDSA OpenZeppelin Utility
  * Usage of string manipulation in function _toEthSignedMessageHash_
  * Usage for inline assembly in function _tryRecover_

### References

<https://eips.ethereum.org/EIPS/eip-191>

<https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/>

<https://docs.ethers.io/v5/api/signer/#Signer-signMessage>

<https://docs.openzeppelin.com/contracts/4.x/utilities#checking_signatures_on_chain>

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol>

---

## Homework

* Create Github Issues with your questions about this lesson
* Read the references
