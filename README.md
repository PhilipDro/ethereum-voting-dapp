# Voting dApp based on Ethereum Smart Contracts

#### Install modules:
```
npm install ganache-cli // blockchain simulator
npm install web3 // js library interacting with blockchain via RPC
npm install solc // compiles to bytecode
```

#### Start Ganache Blockchain Simulator:

```
node_modules/.bin/ganache-cli
```

#### Compile Solidity Code into Byte Code for the EVM to consume.
```
node_modules/.bin/solcjs --bin --abi contract.sol
```

Now you have:
* contract_sol_VotingContract.abi
    * ABI is the definition which tells the contract user which methods are available
* contract_sol_VotingContract.bin
    * bytecode that communicates with the blockchain

#### Interact with web3js via node console:


```
node
> Web3 = require('web3')
> Web3 = new Web3("http://127.0.0.1:8545")
> console.log(Web3) // output available methods

> Web3.eth.getAccounts().then(console.log) // query all accounts and display them
```

#### Compile the contract:

```
> bytecode = fs.readFileSync('contract_sol_VotingContract.bin').toString()
> abi = JSON.parse(fs.readFileSync('contract_sol_VotingContract.abi').toString())
```

#### Deploy contract to the blockchain:

```
> deployedVoteContract = new Web3.eth.Contract(abi) // create contract object
> listOfCandidates = ['Tim', 'Max', 'Rosa'] // set list of candidates
> deployedVoteContract.deploy({
  data: bytecode, // pass bytecode
  // convert candidates to bytes32
  arguments: [listOfCandidates.map(name => Web3.utils.asciiToHex(name))]
}).send({
  from: 'Input user hash', // declare who owns the contract
  gas: 1500000, // amount of costs the owner has to carry
  // each unit has a price associated with it
  gasPrice: Web3.utils.toWei('0.00003', 'ether') 
}).then((newContractInstance) => {
  deployedVoteContract.options.address = newContractInstance.options.address
  console.log(newContractInstance.options.address)
});
```

#### Interact with the Contract using deployedVoteContracts Methods

```
// display votes for Tim
> deployedVoteContract.methods.totalVotesFor(Web3.utils.asciiToHex('Tim')).call(console.log)

// increment vote for Tim
> deployedVoteContract.methods.voteForCandidate(Web3.utils.asciiToHex('Tim')).send({from: '0x9Eb07e3b616A15a10d6b0b3fb3d3656834e31659'}).then((f) => console.log(f))

// display new vote status for Tim
> deployedVoteContract.methods.totalVotesFor(Web3.utils.asciiToHex('Tim')).call(console.log)
```