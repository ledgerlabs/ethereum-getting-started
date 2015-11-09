# Getting Started
Ethereum dApps have two key components:  the on-blockchain code that is used for high security guarantees on a public or private blockchain (server-like), and the local front ends that provide user interface and off-blockchain functionality to the user (client-like).

In this guide we provide basic instructions for getting started with dApp development by using the Geth blockchain client for interacting with the blockchain and MeteorJS to create an HTML5 front end that interacts with it via Javascript APIs. For further details on each component of this stack, or to explore some of the many other possibilities for developing dApps on Ethereum, feel free to consult the following links as a reference:

* [Frontier Guide](https://ethereum.gitbooks.io/frontier-guide/content/)
* [Ethereum Development Tutorial](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial)
* [Ethereum JavaScript API](https://github.com/ethereum/wiki/wiki/JavaScript-API) 
* [Building Ethereum (Go client)](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)
* [Ethereum Forums](https://forum.ethereum.org/)
* [List of dApps](http://dapps.ethercasts.com/); some are open-source. 

Let's begin by getting our stack set up.  Although developing dApps that use the public Ethereum blockchain is in some sense no different than using a private blockchain or a local test blockchain, a few practical factors make it easier to start with controlled environments.  This guide will show you how to set up your own local blockchain for testing so that you don't have to worry about things like obtaining ether to pay for blockchain space.  During the hackathon itself, we will also make available a shared private blockchain on a designated IP address so that different teams and machines can easily interoperate or take advantage of shared tools.

## Preparing for a local test chain

The first block in a new chain is referred to as a "genesis block".  By default, Ethereum clients will use the public Ethereum blockchain and its corresponding genesis block.  In order to cleanly differentiate your private test network from the public(and prevent clients from trying to merge them), utilising a separate genesis block is considered best practice.  Fortunately, the steps for doing so are simple:

1.  Download the custom genesis block from [this link](http://tech.lab.carl.pro/_media/kb/ethereum/customgenesis.json)
2.  Select a random 5 digit number to act as your unique network id, i.e. using [random.org](https://www.random.org/)


## Installing the Geth Ethereum client

Next, it's time to install the actual client which will manage your private blockchain.  A variety of easy installation methods are available, depending on your OS:

To install on linux (Ubuntu) run the follow commands: 

    sudo apt-get install software-properties-common
    sudo add-apt-repository -y ppa:ethereum/ethereum
    sudo apt-get update
    sudo apt-get install ethereum

**Mac**: You will need the "<a href="http://brew.sh/">Homebrew</a>" packages.
  
    brew tap ethereum/ethereum
    brew install ethereum

**Windows**: You will need to install the [chocolatey.org](https://chocolatey.org/) system, and then install Geth from its package manager. From an administrative command prompt run:

    @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

and then

    choco install geth-stable --version=1.2.2.0

By default choco installs geth in an obscure directory:  it can be found in C:\ProgramData\chocolatey\lib\geth-stable\tools if you want to run it or add it to your system path.
    
## Setting up a Geth account and Using the Console
To run Geth with your private test blockchain, we will pass it our genesis block with the --genesis parameter, and our unique network id with the --networkid parameter.  If you are interested in further customisation, such as using a different user or data directory so that you can interact with both your private test chain and the public network, refer to the documentation [here](http://tech.lab.carl.pro/kb/ethereum/testnet_setup).

Assuming you have Geth added to your system path or are in the folder where it is installed, you can now setup geth with this command:

    geth account new

When it launches, there will be a disclaimer to agree to, and you will be asked to enter a password for your local ethereum account.  It will print an address and exit.

Now we want to put it into action.  Run Geth again, this time using the following options (again replacing the networkid with your own of course):

    geth --genesis customgenesis.json --networkid 12345 --rpc --rpccorsdomain="*" --ipcapi "admin,eth,miner" --rpcapi "eth,web3" --mine --unlock=0 --verbosity=5 --maxpeers=0 --nodiscover --minerthreads="4" console

This time geth may take a while to start up, because we have passed options to begin mining on our test blockchain.  Geth will also prompt you for your password since we have asked it to unlock your account for RPC access.  For a full list of options that can be passed to Geth, feel free to refer [here](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options) or simply use the --help parameter.

After you enter your password and it finishes getting ready to mine (about 5 to 10 minutes), Geth will now be creating its own local test blockchain, and serving access to a wide variety of functions over RPC.  Note that a configuration like this would be quite insecure for anything other than testing!!  When you need to exit Geth you can just type "exit" into the Geth console and it will cleanly shut down.

If mining heats up your computer too much (by consuming 100% of your CPU, as it is designed to), you can stop geth after it has mined a few blocks (giving you test ether to use) and restart it without the --mine parameter.  Keep in mind that if you do this you may still need to mine more blocks later for testing or contract inclusion reasons.  Another common concern is being flooded with too many console messages, which can be dealt with by appending a log file, as in:

    geth console 2>> geth.log

  
## Deploying your first dApp
In order to deploy your first dApp, we need to get MeteorJS set up. If it isn't already installed on your system, go ahead and install it: 

**Linux & Mac**: From a terminal, run <code>curl https://install.meteor.com/ | sh</code>. 

**Windows**: Download and run the binary installer: <a href="https://install.meteor.com/windows">https://install.meteor.com/windows</a>

Now that we have MeteorJS installed, we need to grab a dApp from github. Let's try the example from [here](https://github.com/SilentCicero/meteor-dapp-boilerplate.git). 

On Linux and Mac you can install and run the example easily from the terminal:

    mkdir dapps
    cd dapps
    git clone https://github.com/SilentCicero/meteor-dapp-boilerplate.git
    cd meteor-dapp-boilerplate
    cd app
    meteor

On Windows you can simply download the zip file from the above link, and extract it into a folder (or use git).  Open a command prompt to the app subfolder and type <code>meteor</code> to start it.

Meteor may take a little while to download and install any necessary components, especially if you have Geth mining and taking up all your CPU.  After meteor finishes, make sure that you have your Geth rpc instance (described above) back up and running in the background.  Some juggling may be required here (i.e. restarting meteor again after you bring geth back up).  Once both are running you can now go to http://localhost:3000 to view this app in action. The app is very simple: it just shows the current ether balances of your local wallets. As you mine and refresh the page, you should see the numbers changing.

If the balance functionality is working properly, select the "View2" option from the top right to try deploying a simple "Multiplying" contract.  Congratulations!  You have deployed an Ethereum smart contract!
 
## Writing and Compiling a Contract of Your Own
To start writing your own on-blockchain "contract" code, you will need a compiler for the Ethereum virtual machine.  You could host your own, but using an online version is much easier.  For this guide we recommend using [Cosmo](http://cosmo.to/), which can both compile your contract and deploy it to the geth client running on your localhost.

To compile your first smart contract, you can select one of the many examples from [this tutorial](https://github.com/ethereum/go-ethereum/wiki/Contract-Tutorial) or any of the default examples that Cosmo loads with.  You can ignore the instructions on the page about installing your own compiler unless you're interested in doing so.

On the left of Cosmo you will see high level source code for the smart contract.  The language these contracts are written in is called "Solidity" but it is very similar to any other object-oriented language such as Java or C++.  On the middle right, you can see the contract's interface (which enables it to be easily called) and the opcodes of the compiled contract in the language of the ethereum virtual machine.  It is these opcodes which will actually be placed into the blockchain and run.

To connect Cosmo to your running node, change the port in the top right box ("rpc provider") to 8545 and press connect.  The console below should say "Web3 Connected" though it may give some other errors.

Now you can press "DEPLOY" above the source code window and the example contract will be automatically deployed to your testing blockchain.  You can now use the Methods box to interact with your live contract.

That's it!  To begin experimenting, feel free to modify either the MeteorJS boilerplate example, or just play around with smart contract design in Cosmo.  More detailed documentation of the <code>web3</code> javascript object used to connect your HTML5 apps with Geth and the on-blockchain Solidity smart contracts is available [here](https://github.com/ethereum/wiki/wiki/JavaScript-API).  During the deep dive we will be working a specific example in more detail and answering any question you might have (or just hit us up on the Slack in the #ethereum channel!

## Anatomy of an Ethereum Contract
In this case we'll look at a simple storage program. 

#### Solidity Code

```
contract SimpleStorage {
    uint storedData;
    function set(uint x) {
        storedData = x;
    }
    function get() constant returns (uint retVal) {
        return storedData;
    }
}
```

#### ABI
```
[
  {
    "constant": false,
    "inputs": [
      {
        "name": "x",
        "type": "uint256"
      }
    ],
    "name": "set",
    "outputs": [],
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "get",
    "outputs": [
      {
        "name": "retVal",
        "type": "uint256"
      }
    ],
    "type": "function"
  }
]
```


#### Byte Code
Here is the Ethereum Byte code for this contract: 
<code>6060604052603b8060116000396000f300606060405260e060020a600035046360fe47b1811460245780636d4ce63c14602e575b005b6004356000556022565b6000546060908152602090f3 </code>

#### Complete Javascript Code

```
var abi = [
        {
            "constant": false,
            "inputs": [
                {
                    "name": "x",
                    "type": "uint256"
                }
            ],
            "name": "set",
            "outputs": [],
            "type": "function"
        },
        {
            "constant": true,
            "inputs": [],
            "name": "get",
            "outputs": [
                {
                    "name": "retVal",
                    "type": "uint256"
                }
            ],
            "type": "function"
        }
    ],
    code = '6060604052603b8060116000396000f300606060405260e060020a600035046360fe47b1811460245780636d4ce63c14602e575b005b6004356000556022565b6000546060908152602090f3',
    deployTransactionObject = {
        from: web3.eth.accounts[0],
        gas: 300000,
        data: code,
    },
    setTransactionObject = {
        from: web3.eth.accounts[0],
        gas: 300000,
    };
```

#### Setup Contract in JS
Setup the SimpleStorage Contract Object in JS
```
var SimpleStorage = web3.eth.contract(abi);
```

#### Deploy
 Deploy a new SimpleStorage contract to the Ethereum blockchain
```
SimpleStorage.new(deployTransactionObject, function(err, contract){
    if(err)
        console.log('There was an error deploying your SimpleStorage contract on the Blockchain: ', err);

    if(!err) {
        if(!contract.address) {
            console.log('Your transaction was posted to the Ethereum blockchain with this hash identifier: ', contract.transactionHash);
        } else {
            console.log('Your contract was deployed to this address on the blockchain: ', contract.address);
        }
    }
});
```

#### Accessing Storage
Once you have deployed your SimpleStorage contract, use the address provided below as the .at address
to set up a SimpleStorage Contract Instance
```
var contractInstance = SimpleStorage.at('0x7de20ad122e172d75086abe4df51841851f2019c');
```

#### Set Stored Value
 Set Stored Value in your SimpleStorage contract on the Ethereum blockchain

```
contractInstance.set.sendTransaction(456, setTransactionObject, function(err, result){
    if(err)
        console.log('Error Saving Data on SimpleStorage: ', err);

    if(!err)
        console.log('Transaction Hash: ', result);
});
```


#### Retrieve Stored Value
Get the stored value from your SimpleStorage contract on the Ethereum blockchain
```
contractInstance.get.call(function(err, result){
    if(err)
        console.log('Error Calling Contract', err);
    
    if(!err)
        console.log('Stored Value: ', result);
});
```
