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
node_modules/.bin/solcjs --bin --abi Voting.sol
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
> web3 = new Web3("http://localhost:8545")

> web3.eth.getAccounts(console.log) // query all accounts and display them
```

#### Load ABI and bytecode in to string:

```
> bytecode = fs.readFileSync('Voting_sol_Voting.bin').toString()
> abi = JSON.parse(fs.readFileSync('Voting_sol_Voting.abi').toString())
```

#### Deploy contract to the blockchain:

```
> deployedContract = new web3.eth.Contract(abi) // create contract object
> listOfCandidates = ['Tim', 'Max', 'Rosa'] // set list of candidates
> deployedContract.deploy({
    data: bytecode,
    arguments: [listOfCandidates.map(name => web3.utils.asciiToHex(name))]
  }).send({
    from: 'Account address of one participant',
    gas: 1500000,
    gasPrice: web3.utils.toWei('0.00003', 'ether')
  }).then((newContractInstance) => {
    deployedContract.options.address = newContractInstance.options.address
    console.log(newContractInstance.options.address)
  });
```

#### Interact with the Contract using deployedVoteContracts Methods

```
// display votes for Tim
> deployedContract.methods.totalVotesFor(web3.utils.asciiToHex('Tim')).call(console.log)

// increment vote for Tim
> deployedContract.methods.voteForCandidate(web3.utils.asciiToHex('Tim')).send({from: 'set account address'}).then((f) => console.log(f))

// display new vote status for Tim
> deployedContract.methods.totalVotesFor(web3.utils.asciiToHex('Tim')).call(console.log)
```