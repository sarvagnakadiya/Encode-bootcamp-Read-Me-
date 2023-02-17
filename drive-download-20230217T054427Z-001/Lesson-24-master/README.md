# Lesson 24 - Part 1: IPFS

## File storage for the Web3.0

* Files on the server
* Scalable centralized solutions
* Databases vs File servers
* Decentralized file storage principles
* Introducing IPFS
* IPFS as a File Server

### References

<https://ipfs.io/>

## Using The IPFS

* Hosting and Co-Hosting files with IPFS
* Running a node
* Connecting to a local node
* Uploading files
* Downloading files
* File hashes

### References

<https://docs.ipfs.io/>

<https://docs.ipfs.io/how-to/command-line-quick-start/>

# Lesson 24 - Part 2: Smart Contract Security

## The Security Toolbox

* Docker
* Running images
* Tools
  * Slither
  * Echidna
  * Manticore
  * Mythx
  * Securfy2
* Fakerdao sample project

### References

<https://github.com/crytic/slither>

<https://github.com/crytic/echidna>

<https://github.com/trailofbits/manticore>

<https://mythx.io/>

<https://github.com/eth-sri/securify2>

<https://medium.com/coinmonks/ethereum-security-analysis-tools-an-introduction-and-comparison-1096194e64d5>

<https://github.com/scopelift/fakerdao>

### Instructions

Setup:

    git clone https://github.com/scopelift/fakerdao
    cd fakerdao
    python3 -m venv venv
    venv\Scripts\activate
    pip install slither-analyzer
    npm i
    npm install -g truffle
    slither .
    slither . --print contract-summary --truffle-ignore-compile

---

## Homework

* Create Github Issues with your questions about this lesson
* Read the references
