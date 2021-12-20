# Overview

# 1. Project Repository

Github Url: [https://github.com/stabilaprotocol](https://github.com/stabilaprotocol)
[java-stabila](https://github.com/stabilaprotocol/java-stabila) is the source code of the MainNet.
[protocol](https://github.com/stabilaprotocol/protocol) is the definition of the api and data structure.
[wallet-cli](https://github.com/stabilaprotocol/wallet-cli) is the official command line wallet.

MainNet Configuration:
[https://github.com/stabilaprotocol/StabilaDeployment/blob/master/main_net_config.conf](https://github.com/stabilaprotocol/StabilaDeployment/blob/master/main_net_config.conf)
TestNet Configuration:
[https://github.com/stabilaprotocol/StabilaDeployment/blob/master/test_net_config.conf](https://github.com/stabilaprotocol/StabilaDeployment/blob/master/test_net_config.conf)

# 2. SRs and Committee

## 2.1 How to Become a Super Representative

 In STABILA network, any account can apply to become a super representative candidate. Every account can vote for super representative candidates. The top 27 candidates with the most votes are the super representatives. Super representatives can produce blocks. The votes will be counted every 6 hours, so super representatives may also change every 6 hours.

 To prevent vicious attack, STABILA network burns 9999 STB from the account that applies to become a super representative candidate.

## 2.2 Super Representatives Election

 To vote, you need to have STABILA Power(TP). To get STABILA Power, you need to stake STB. Every 1 staked STB accounts for one STABILA Power(TP). Every account in STABILA network has the right to vote for a super representative candidate. After you unstake your staked STB, you will lose the responding STABILA Power(TP), so your previous vote will be invalid.

 Note: Only your latest vote will be counted in STABILA network which means your previous vote will be over written by your latest vote.

Example (Using wallet-cli):

```text
cdbalance 10,000,000 3 // stake 10 STB to get 10 STABILA Power(TP)
votewitness witness1 4 witness2 6 // Vote 4 votes for witness1, 6 votes for witness2
votewitness witness1 3 witness2 7 // Vote 3 votes for witness1, 7 votes for witness2
```

The final output above is: Vote 3 votes for witness1, 7 votes for witness2

## 2.3 Reward for Super Representatives

**Votes Reward:**
Every 6 hours, the top 127 super representative candidates with the most votes will share a total amount of 115,200 STB according to their votes percentage. The annual votes reward is 168,192,000 STB in total.

**Block Producing Reward:**
Every time after a super representative produces a block, it will be reward 32 STB. The 27 super representatives take turns to produce blocks every 3 seconds. The annual block producing reward is 336,384,000 STB in total.

Every time after a super representative produces a block, the 32 STB block producing reward will be sent to it's sub-account. The sub-account is a read-only account, it allows a withdraw action from sub-account to super representative account every 24 hours.

## 2.4 Committee

### 2.4.1 What is Committee

Committee can modify the STABILA network parameters, like transaction fees, block producing reward amount, etc. Committee is composed of the current 27 super representatives. Every super representative has the right to start a proposal. The proposal will be passed after it gets more than 19 approves from the super representatives and will become valid in the next maintenance period.

### 2.4.2 Create a Proposal

Only the account of a super representative can create a proposal.
The network parameters can be modified([min,max]):

- 0: MAINTENANCE_TIME_INTERVAL, [3 * 27* 1000, 24 * 3600 * 1000] //super representative votes count time interval, currently 6 * 3600 * 1000 ms
- 1: ACCOUNT_UPGRADE_COST, [0, 100 000 000 000 000 000]  //the fee to apply to become a super representative candidate, currently 9999_000_000 UNIT
- 2: CREATE_ACCOUNT_FEE, [0, 100 000 000 000  000 000] //the fee to create an account, currently 100_000 UNIT
- 3: TRANSACTION_FEE, [0, 100 000 000 000 000 000] //the fee for bandwidth, currently 1_000 UNIT/byte
- 4: ASSET_ISSUE_FEE, [0, 100 000 000 000 000 000] //the fee to issue an asset, currently 1024_000_000 UNIT
- 5: WITNESS_PAY_PER_BLOCK, [0, 100 000 000 000 000 000] //the block producing reward, currently 32_000_000 UNIT
- 6: WITNESS_STANDBY_ALLOWANCE, [0, 100 000 000 000 000 000] //the votes reward for top 127 super representative candidates, currently 115_200_000_000 UNIT
- 7: CREATE_NEW_ACCOUNT_FEE_IN_SYSTEM_CONTRACT, //the fee to create an account in system, currently 1_000_000 UNIT
- 8: CREATE_NEW_ACCOUNT_BANDWIDTH_RATE, //the consumption of bandwidth or STB while creating an account, using together with #7
- 9: ALLOW_CREATION_OF_CONTRACTS, //to enable the VM
- 10: REMOVE_THE_POWER_OF_THE_GR, //to clear the votes of GR
- 11: UCR_FEE, [0,100 000 000 000 000 000] //UNIT
- 12: EXCHANGE_CREATE_FEE, [0, 100 000 000 000 000 000] //UNIT
- 13: MAX_CPU_TIME_OF_ONE_TX, [0, 1000] //ms
- 14: ALLOW_UPDATE_ACCOUNT_NAME, //to allow users to change account name and allow account duplicate name, currently 0, means false
- 15: ALLOW_SAME_TOKEN_NAME, //to allow create a token with duplicate name, currently 1, means true
- 16: ALLOW_DELEGATE_RESOURCE, //to enable the resource delegation
- 17: TOTAL_UCR_LIMIT, //to modify the ucr limit
- 18: ALLOW_SVM_TRANSFER_TRC10, //to allow smart contract to transfer TRC-10 token, currently 0, means false

Example (Using wallet-cli):
```text
createproposal id value
id: the serial number (0 ~ 18)
value: the parameter value
```

Note: In STABILA network, 1 STB = 1000_000 UNIT

### 2.4.3 Vote for a Proposal

Proposal only support YES vote. Since the creation time of the proposal, the proposal is valid within 3 days. If the proposal does not receive enough YES votes within the period of validity, the proposal will be invalid beyond the period of validity. Yes vote can be cancelled.

Example (Using wallet-cli):
```text
approveProposal id is_or_not_add_approval
id: proposal id
is_or_not_add_approval: YES vote or cancel YES vote
```

### 2.4.4 Cancel Proposal

Proposal creator can cancel the proposal before it is passed.

Example (Using wallet-cli):
```text
deleteProposal id
id: proposal id
```

### 2.4.5 Query Proposal

- Query all the proposals list (ListProposals)
- Query all the proposals list by pagination (GetPaginatedProposalList)
- Query a proposal by proposal id (GetProposalById)

For more api detail, please refer to [Stabila HTTP API](../api/http.md)

# 3. Account Model

## 3.1 Introduction

STABILA uses account model. An account's identity is address, it needs private key signature to operate an account. An account has many attributes, like STB balance, tokens balance, bandwidth, etc. STB and tokens can be transferred from account to account and it costs bandwidth. An account can also issue a smart contract, apply to become a super representative candidate, vote, etc. All STABILA's activities are based on account.

## 3.2 How to Create an Account

1.&nbsp;Use a wallet to generate the address and private key. To activate the account, you need to transfer STB or transfer token to the new created account.

2.&nbsp;Use an account already existed in STABILA network to create an account

## 3.3 Key-pair Generation Algorithm
Stabila signature algorithm is ECDSA, curve used is SECP256K1. Private key is a random bumber, public key is a point in the elliptic curve. The process is: first generate a random number d to be the private key, then calculate P = d * G as the public key, G is the elliptic curve base point.

## 3.4 Address Format
Use the public key P as the input, by SHA3 get the result H. The length of the public key is 64 bytes, SHA3 uses Keccak256. Use the last 20 bytes of H, and add a byte of 0x41 in front of it, then the address comes out. Do basecheck to address, here is the final address. All addresses start with 'T'.

basecheck process: first do sha256 calculation to address to get h1, then do sha256 to h1 to get h2, use the first 4 bytes as check to add it to the end of the address to get address||check, do base58 encode to address||check to get the final result.

Character map:
ALPHABET = "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz"

## 3.5 Signature
[Signature introduction](https://github.com/stabilaprotocol/documentation/blob/master/English_Documentation/Procedures_of_transaction_signature_generation.md)

# 4. Network Node
## 4.1 SuperNode
### 4.1.1 SuperNode Introduction
Super Representative(abbr: SR) is the block producer in STABILA network, there are 27 SR. They verify the transactions and write the transactions into the blocks, they take turns to produce blocks. The super Representatives' information is public to everyone in STABILA network. The best way to browse is using [stabilascan](https://stabilascan.org/#/sr/representatives).
### 4.1.2 SuperNode Deployment
[SuperNode Deployment](https://github.com/stabilaprotocol/java-stabila/blob/develop/run.md#running-a-super-representative-node-for-mainnet)

### 4.1.3 Recommended Hardware Configuration

minimum requirement:
CPU: 16 cores, RAM: 32G, Bandwidth: 100M, Disk: 1T

Recommended requirement:
CPU: > 64 cores RAM: > 64G, Bandwidth: > 500M, Disk: > 20T

## 4.2 FullNode
### 4.2.1 FullNode Introduction
FullNode has the complete block chain data, can update data in real time. It can broadcast the transactions and provide api service.
### 4.2.2 FullNode Deployment
please refer to [STABILA-Deployment](https://github.com/stabilaprotocol/stabila-deployment)
### 4.2.3 Recommended Hardware Configuration
Minimum requirement:
CPU: 16 cores, RAM: 32G, Bandwidth: 100M, Disk: 1T
Recommended requirement:
CPU: > 64 cores RAM: > 64G, Bandwidth: > 500M, Disk: > 20T

## 4.3 SolidityNode
### 4.3.1 SolidityNode Introduction
SolidityNode only synchronize solidified blocks data from the fullNode it specifies, It also provie api service.
### 4.3.2 SolidityNode Deployment
Please refer to [STABILA-Deployment](https://github.com/stabilaprotocol/stabila-deployment)
### 4.3.3 Recommended Hardware Configuration
Minimum requirement:
CPU: 16 cores, RAM: 32G, Bandwidth: 100M, Disk: 1T
Recommended requirement:
CPU: > 64 cores RAM: > 64G, Bandwidth: > 500M, Disk: > 20T

## 4.4 STABILA Network Instructure
STABILA network uses Peer-to-Peer(P2P) network instructure, all nodes status equal. There are three types of node: SuperNode, FullNode, SolidityNode. SuperNode produces blocks, FullNode synchronizes blocks and broadcasts transactions, SolidityNode synchronizes solidified blocks. Any device that deploy the java-stabila code can join STABILA network as a node.
![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/network.png)

## 4.5 FullNode and SolidityNode Fast Deployment
Download fast deployment script, run the script according to different types of node.
please refer to [Node Fast Deployment](https://github.com/stabilaprotocol/stabila-deployment#deployment-of-soliditynode-on-the-one-host)

## 4.6 MainNet, TestNet, PrivateNet

MainNet, TestNet, PrivateNet all use the same code, only the node start configuration varies.

### 4.6.1 MainNet

[MainNet configuration](https://github.com/stabilaprotocol/stabila-deployment/blob/master/main_net_config.conf)

### 4.6.2 TestNet

[TestNet configuration](https://github.com/stabilaprotocol/stabila-deployment/blob/master/test_net_config.conf)

### 4.6.3 PrivateNet

**4.6.3.1 Preconditions**

- at least two accounts
- at least deploy one SuperNode to produce blocks
- deploy serval FullNodes to synchronize blocks and broadcast transactions
- SuperNode and FullNode comprise the private network

**4.6.3.2 Deployment**

**4.6.3.2.1 Step 1: SuperNode Deployment**

 1.&nbsp;download private_net_config.conf

```text
wget https://raw.githubusercontent.com/stabilaprotocol/stabila-deployment/master/private_net_config.conf
```
 2.&nbsp;add your private key in localwitness
 3.&nbsp;set genesis.block.witnesses as the private key's corresponding address
 4.&nbsp;set p2p.version, any positive integer but 11111
 5.&nbsp;set the first SR needSyncCheck = false, others can be set true
 6.&nbsp;set node.discovery.enable = true
 7.&nbsp;run the script

```shell
nohup java -Xmx6g -XX:+HeapDumpOnOutOfMemoryError -jar FullNode.jar  --witness  -c private_net_config.conf

command line parameters introduction:
--witness: start witness function. For SuperNode, this parameter is required, i.e.: --witness
--log-config: specify the log configuration file path, i.e.: --log-config logback.xml
-c: specify the configuration file path, i.e.: -c config.conf
```

 The usage of the log file:
 You can change the level of the module to control the log output. The default level of each module is INFO, for example: only print the message with the level higher than warn:
 <logger name="net" level="WARN"/>
 The parameters in configuration file that need to modify:
 localwitness:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/localwitness.jpg)
 witnesses:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/witness.png)
 version:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/p2p_version.png)
 enable:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/discovery_enable.png)


**4.6.3.2.2  Step 2: FullNode Deployment**

 1.&nbsp;Download private_net_config.conf

```shell
wget https://raw.githubusercontent.com/stabilaprotocol/stabila-deployment/master/private_net_config.conf
```

 2.&nbsp;set seed.node ip.list with SR's ip and port

 3.&nbsp;set p2p.version the same as SuperNode's p2p.version

 4.&nbsp;set genesis.block the same as genesis.block

 5.&nbsp;set needSyncCheck true

 6.&nbsp;set node.discovery.enable true

 7.&nbsp;run the script


```shell
 nohup java -Xmx6g -XX:+HeapDumpOnOutOfMemoryError -jar FullNode.jar  -c private_net_config.conf

 command lines parameters
 --log-config: specify the log configuration file path, i.e.: --log-config logback.xml
 -c: specify the configuration file path, i.e.: -c config.conf
```

 The usage of the log file:
 You can change the level of the module to control the log output. The default level of each module is INFO, for example: only print the message with the level higher than warn:
 <logger name="net" level="WARN"/>
 The parameters in configuration file that need to modify:
 ip.list:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/ip_list.png)
 p2p.version:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/p2p_version.png)
 genesis.block:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/genesis_block.png)
 needSyncCheck:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/need_sync_check.png)
 node.discovery.enable:

 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/discovery_enable.png)

## 4.7 DB Engine
### 4.7.1 Rocksdb
**4.7.1.1 Configuration**

 Use rocksdb as the data storage engine, need to set db.engine to "ROCKSDB"
 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/db_engine.png)
 Note: rocksdb only support db.version=2, do not support db.version=1

 The optimization parameters rocksdb support:
 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/rocksdb_tuning_parameters.png)

**4.7.1.2 Use rocksdb's data backup function**

 Choose rocksdb to be the data storage engine, you can use it's data backup function while running
 ![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/db_backup.png)

 Note: FullNode can use data backup function. In order not to affect SuperNode's block producing performance, SuperNode does not support backup service, but SuperNode's backup service node can use this function.

**4.7.1.3 Convert leveldb data to rocksdb data**

 The data storage structure of leveldb and rocksdb is not compatible, please make sure the node use the same type of data engine all the time. We provide data conversion script which can convert leveldb data to rocksdb data.

 Usage:
```text
 cd to the source code root directory
 ./gradlew build   #build the source code
 java -jar build/libs/DBConvert.jar  #run data conversion command
```
 Note: If the node's data storage directory is self-defined, before run DBConvert.jar, you need to add the following parameters:

 **src_db_path**: specify LevelDB source directory, default output-directory/database
 **dst_db_path**: specify RocksDb source directory, default output-directory-dst/database

Example, if you run the script like this:
```text
 nohup java -jar FullNode.jar -d your_database_dir &
```
Then, you should run DBConvert.jar this way:
```text
 java -jar build/libs/DBConvert.jar  your_database_dir/database  output-directory-dst/database
```
 Note: You have to stop the running of the node, and then to run the data conversion script.

 If you do not want to stop the running of the node for too long, after node is shut down, you can copy leveldb's output-directory to the new directory, and then restart the node. Run DBConvert.jar in the previous directory of the new directory, and specify the parameters: `src_db_path`和`dst_db_path`.

Example:
```text
 cp -rf output-directory /tmp/output-directory
 cd /tmp
 java -jar DBConvert.jar output-directory/database  output-directory-dst/database
```
 All the whole data conversion process may take 10 hours.

**4.7.1.4 rocksdb vs leveldb**

You can refer to:
[ROCKSDB vs LEVELDB](https://github.com/stabilaprotocol/documentation/blob/master/STB/Rocksdb_vs_Leveldb.md)

# 5. Smart Contract
## 5.1 STABILA Smart Contract Introduction

Smart contract is a computerized transaction protocol that automatically implements its terms. Smart contract is the same as common contract, they all define the terms and rules related to the participants. Once the contract is started, it can run in the way it is designed.

STABILA smart contract support Solidity language in (Ethereum). Currently recommend Solidity language version is 0.4.24 ~ 0.4.25. Write a smart contract, then build the smart contract and deploy it to STABILA network. When the smart contract is triggered, the corresponding function will be executed automatically.

## 5.2 STABILA Smart Contract Features
STABILA virtual machine is based on Ethereum solidity language, it also has STABILA's own features.

### 5.2.1 Smart Contract
STABILA VM is compatible with Ethereum's smart contract, using protobuf to define the content of the contract:
```
message SmartContract {
  message ABI {
    message Entry {
      enum EntryType {
        UnknownEntryType = 0;
        Constructor = 1;
        Function = 2;
        Event = 3;
        Fallback = 4;
      }
      message Param {
        bool indexed = 1;
        string name = 2;
        string type = 3;
        // SolidityType type = 3;
      }
      enum StateMutabilityType {
        UnknownMutabilityType = 0;
        Pure = 1;
        View = 2;
        Nonpayable = 3;
        Payable = 4;
      }

      bool anonymous = 1;
      bool constant = 2;
      string name = 3;
      repeated Param inputs = 4;
      repeated Param outputs = 5;
      EntryType type = 6;
      bool payable = 7;
      StateMutabilityType stateMutability = 8;
    }
    repeated Entry entrys = 1;
  }
  bytes origin_address = 1;
  bytes contract_address = 2;
  ABI abi = 3;
  bytes bytecode = 4;
  int64 call_value = 5;
  int64 consume_user_resource_percent = 6;
  string name = 7；
  int64 origin_ucr_limit = 8;
}
```
origin_address: smart contract creator address

contract_address: smart contract address

abi: the api information of the all the function of the smart contract
bytecode: smart contract byte code

call_value: STB transferred into smart contract while call the contract
consume_user_resource_percent: resource consumption percentage set by the developer

name: smart contract name

origin_ucr_limit: ucr consumption of the developer limit in one call, must greater than 0. For the old contracts, if this parameter is not set, it will be set 0, developer can use
 updateUcrLimit api to update this parameter (must greater than 0)

Through other two grpc message types CreateSmartContract and TriggerSmartContract to create and use smart contracts.

### 5.2.2 The Usage of the Function of Smart Contract

1.&nbsp;constant function and inconstant function

There are two types of function according to whether any change will be made to the properties on the chain: constant function and inconstant function
Constant function uses view/pure/constant to decorate, will return the result on the node it is called and not be broadcasted in the form of a transaction
Inconstant function will be broadcasted in the form of a transaction while being called, the function will change the data on the chain, such as transfer, changing the value of the internal variables of contracts, etc.

Note: If you use create command inside a contract (CREATE instruction), even use view/pure/constant to decorate the dynamically created contract function, this function will still be treated as inconstant function, be dealt in the form of transaction.

2.&nbsp;message calls

Message calls can call the functions of other contracts, also can transfer STB to the accounts of contract and none-contract. Like the common STABILA triggercontract, Message calls have initiator, recipient, data, transfer amount, fees and return attributes. Every message call can generate a new one recursively. Contract can define the distribution of the remaining ucr in the internal message call. If it comes with OutOfUcrException in the internal message call, it will return false, but not error. In the meanwhile, only the gas sent with the internal message call will be consumed, if ucr is not specified in call.value(ucr), all the remaining ucr will be used.

3.&nbsp;delegate call/call code/libary

There is a special type of message call, delegate call. The difference with common message call is the code of the target address will be run in the context of the contract that initiates the call, msg.sender and msg.value remain unchanged. This means a contract can dynamically load code from another address while running. Storage, current address and balance all point to the contract that initiates the call, only the code is get from the address being called. This gives Solidity the ability to achieve the 'lib' function: the reusable code lib can be put in the storage of a contract to implement complex data structure library.

4.&nbsp;CREATE command

This command will create a new contract with a new address. The only difference with Ethereum is the newly generated STABILA address used the smart contract creation transaction id and the hash of nonce called combined. Different from Ethereum, the definition of nonce is the comtract sequence number of the creation of the root call. Even there are many CREATE commands calls, contract number in sequence from 1. Refer to the source code for more detail.
Note: Different from creating a contract by grpc's deploycontract, contract created by CREATE command does not store contract abi.

5.&nbsp;built-in function and built-in function attribute (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

1)SVM is compatible with solidity language's transfer format, including:
- accompany with constructor to call transfer
- accompany with internal function to call transfer
- use transfer/send/call/callcode/delegatecall to call transfer

Note: STABILA's smart contract is different from STABILA's system contract, if the transfer to address does not exist it can not create an account by smart contract transfer.

2)Different accounts vote for SuperNode (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
3)SuperNode gets all the reward (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
4)SuperNode approves or disapproves the proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
5)SuperNode proposes a proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
6)SuperNode deletes  a proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
7)STABILA byte address converts to solidity address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
8)STABILA string address converts to solidity address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
9)Send token to target address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
10)Query token amount of target address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)
11)Compatible with all the built-in functions of Ethereum

Note: Ethereum's RIPEMD160 function is not recommended, because the return of STABILA is a hash result based on STABILA's sha256, not an accurate Ethereum RIPEMD160.

### 5.2.3 Contract Address Using in Solidity Language

Ethereum VM address is 20 bytes, but STABILA's VM address is 21 bytes.

1.&nbsp;address conversion

Need to convert STABILA's address while using in solidity (recommended):
```text
/**
     *  @dev    convert uint256 (HexString add 0x at beginning) stabila address to solidity address type
     *  @param  stabilaAddress uint256 stabilaAddress, begin with 0x, followed by HexString
     *  @return Solidity address type
*/

function convertFromStabilaInt(uint256 stabilaAddress) public view returns(address){
        return address(stabilaAddress);
}
```
This is similar with the grammar of the conversion from other types converted to address type in Ethereum.

2.&nbsp;address judgement

Solidity has address constant judgement, if using 21 bytes address the compiler will throw out an error, so you should use 20 bytes address, like:
```text
function compareAddress(address stabilaAddress) public view returns (uint256){
        // if (stabilaAddress == 0x41ca35b7d915458ef540ade6068dfe2f44e8fa733c) { // compile error
        if (stabilaAddress == 0xca35b7d915458ef540ade6068dfe2f44e8fa733c) { // right
            return 1;
        } else {
            return 0;
        }
}
```
But if you are using wallet-cli, you can use 21 bytes address, like 0000000000000000000041ca35b7d915458ef540ade6068dfe2f44e8fa733c

3.&nbsp;variable assignment

Solidity has address constant assignment, if using 21 bytes address the compiler will throw out an error, so you should use 20 bytes address, like:
```text
function assignAddress() public view {
        // address newAddress = 0x41ca35b7d915458ef540ade6068dfe2f44e8fa733c; // compile error
        address newAddress = 0xca35b7d915458ef540ade6068dfe2f44e8fa733c;
        // do something
}
```
If you want to use STABILA address of string type (TLLM21wteSPs4hKjbxgmH1L6poyMjeTbHm) please refer to (2-4-7,2-4-8).

### 5.2.4 The Special Constants Differ from Ethereum

**Currency**

Like solidity supports ETH, STABILA VM supports stb and unit, 1 stb = 1000000 unit, case sensitive, only support lower case. stabila-studio supports stb and unit, remix does not support stb and unit.
We recommend to use stabila-studio instead of remix to build STABILA smart contract.

**Block**

- block.blockhash (uint blockNumber) returns (bytes32): specified block hash, can only apply to the latest 256 blocks and current block excluded
- block.coinbase (address): SuperNode address that produced the current block
- block.difficulty (uint): current block difficulty, not recommended, set 0
- block.gaslimit (uint): current block gas limit, not supported, set 0
- block.number (uint): current block number
- block.timestamp (uint): current block timestamp
- gasleft() returns (uint256): remaining gas
- msg.data (bytes): complete call data
- msg.gas (uint): remaining gas - since 0.4.21, not recommended, replaced by gesleft()
- msg.sender (address): message sender (current call)
- msg.sig (bytes4): first 4 bytes of call data (function identifier)
- msg.value (uint): the amount of UNIT send with message
- now (uint): current block timestamp (block.timestamp)
- tx.gasprice (uint): the gas price of transaction, not recommended, set 0
- tx.origin (address): transaction initiator

## 5.3 Ucr Introduction
Each command of smart contract consume system resource while running, we use 'Ucr' as the unit of the consumption of the resource.

### 5.3.1 How to Get Ucr

Stake STB to get ucr.

Example (Using wallet-cli):

```text
cdBalance cded_balance cded_duration [ResourceCode:0 BANDWIDTH,1 UCR]
```

Stake STB to get ucr, ucr obtained = user's STB staked amount / total amount of staked STB in STABILA * 50_000_000_000.

Example:

```text
If there are only two users, A stakes 2 STB, B stakes 2 STB
the ucr they can get is:
A: 25_000_000_000 and ucr_limit is 25_000_000_000
B: 25_000_000_000 and ucr_limit is 25_000_000_000

when C stakes 1 STB:
the ucr they can get is:
A: 20_000_000_000 and ucr_limit is 20_000_000_000
B: 20_000_000_000 and ucr_limit is 20_000_000_000
B: 10_000_000_000 and ucr_limit is 10_000_000_000
```

** Ucr Recovery **

The ucr consumed will reduce to 0 smoothly within 24 hours.

Example:

```text
at one moment, A has used 72_000_000 Ucr
if there is no continuous consumption or STB stake
one hour later, the ucr consumption amount will be 72_000_000 - (72_000_000 * (60*60/60*60*24)) Ucr = 69_000_000 Ucr
24 hours later, the ucr consumption amount will be 0 Ucr
```

### 5.3.2 How to Set Fee Limit (Caller Must Read)
***

*Within the scope of this section, the smart contract developer will be called "developer", the users or other contracts which call the smart contract will be called "caller"*

*The amount of ucr consumed while call the contract can be converted to STB or UNIT, so within the scope of this section, when refer to the consumption of the resource, there's no strict difference between Ucr, STB and UNIT, unless they are used as a number unit.*

***

Set a rational fee limit can guarantee the smart contract execution. And if the execution of the contract cost great ucr, it will not consume too much ucr from the caller. Before you set fee limit, you need to know several conception:

1.&nbsp;The legal fee limit is a integer between 0 - 10^9, unit is UNIT.

2.&nbsp;Different smart contracts consume different amount of ucr due to their complexity. The same trigger in the same contract almost consumes the same amount fo ucr[1]. When the contract is triggered, the commands will be executed one by one and consume ucr. If it reaches the fee limit, commands will fail to be executed, and ucr is not refundable.

3.&nbsp;Currently fee limit only refers to the ucr converted to UNIT that will be consumed from the caller[2]. The ucr consumed by triggering contract also includes developer's share.

4.&nbsp;For a vicious contract, if it encounters execution timeout or bug crash, all it's ucr will be consumed.

5.&nbsp;Developer may undertake a proportion of ucr consumption(like 90%). But if the developer's ucr is not enough for consumption, the rest of the ucr consumption will be undertaken by caller completely. Within the fee limit range, if the caller does not have enough ucr, then it will burn equivalent amount of STB [2].

To encourage caller to trigger the contract, usually developer has enough ucr.

** Example **

How to estimate the fee limit:

Assume contract C's last execution consumes 18000 Ucr, so estimate the ucr consumption limit to be 20000 Ucr[3]

According to the staked STB amount and ucr conversion, assume 1 STB = 400 ucr.

When to burn STB, 1 STB = 10000 ucr[4]

Assume developer undertake 90% ucr consumption, and developer has enough ucr.

Then the way to estimate the fee limit is:
```
1). A = 20000 ucr * (1 STB / 400 ucr) = 50 STB = 50_000_000 UNIT,

2). B = 20000 ucr * (1 STB / 10000 ucr) = 2 STB = 2_000_000 UNIT,

3). Take the greater number of A and B, which is 50_000_000 UNIT,

4). Developer undertakes 90% ucr consumption, caller undertakes 10% ucr consumption,
```

So, the caller is suggested to set fee limit to 50_000_000 UNIT * 10% = 5_000_000 UNIT

Note:
```
[1] The ucr consumption of each execution may fluctuate slightly due to the situation of all the nodes.
[2] STABILA may change this policy.
[3] The estimated ucr consumption limit for the next execution should be greater than the last one.
[4] 1 STB = 10^4 ucr is a fixed number for burning STB to get ucr, STABILA may change it in future.
```

### 5.3.3 Ucr Calculation (Developer Must Read)

1.&nbsp;In order to punish the vicious developer, for the abnormal contract, if the execution times out (more than 50ms) or quits due to bug (revert not included), the maximum available ucr will be deducted. If the contract runs normally or revert, only the ucr needed for the execution of the commands will be deducted.

2.&nbsp;Developer can set the proportion of the ucr consumption it undertakes during the execution, this proportion cna be changed later. If the developer's ucr is not enough, it will consume the caller's ucr.

3.&nbsp;Currently, the total ucr available when trigger a contract is composed of caller fee limit and developer's share

Note:
- If the developer is not sure about whether the contract is normal, do not set caller's ucr consumption proportion to 0%, in case all developer's ucr will be deducted due to vicious execution[1].
- We recommend to set caller's ucr consumption proportion to 10% ~ 100%[2].

**Example 1**

A has an account with a balance of 90 STB(90000000 UNIT) and 10 STB staked for 100000 ucr.

Smart contract C set the caller ucr consumption proportion to 100% which means the caller will pay for the ucr consumption completely.

A triggers C, the fee limit set is 30000000 (unit UNIT, 30 STB)

So during this trigger the ucr A can use is from two parts:
- A's ucr by staking STB;
- The ucr converted from the amount of STB burning according to a fixed rate;

If fee limit is greater than the ucr obtained from staking STB, then it will burn STB to get ucr. The fixed rate is: 1 Ucr = 100 UNIT, fee limit still has (30 - 10) STB = 20 STB available, so the ucr it can keep consuming is 20 STB / 100 UNIT = 200000 ucr.

Finally, in this call, the ucr A can use is (100000 + 200000) = 300000 ucr.

If contract executes successfully without any exception, the ucr needed for the execution will be deducted. Generally, it is far more less than the amount of ucr this trigger can use.

If Assert-style error come out, it will consume the whole number of ucr set for fee limit.

Assert-style error introduction, refer to [https://developers.stabila.network/docs/vm-exception](https://developers.stabila.network/docs/vm-exception)

**Example 2**

A has an account with a balance of 90 STB(90000000 UNIT) and 10 STB staked for 100000 ucr.

Smart contract C set the caller ucr consumption proportion to 40% which means the developer will pay for the rest 60% ucr consumption.

Developer D stakes 50 STB to get 500000 ucr.

A triggers C, the fee limit set is 200000000 (unit UNIT, 200 STB).

So during this trigger the ucr A can use is from three parts:
- A's ucr by staking STB -- X;
- The ucr converted from the amount of STB bruning according to a fixed rate -- Y;
If fee limit is greater than the ucr obtained from staking STB, then it will burn STB to get ucr. The fixed rate is: 1 Ucr = 100 UNIT, fee limit still has (200 - 10) STB = 190 STB available, but A only has 90 STB left, so the ucr it can keep consuming is 90 STB / 100 UNIT = 900000 ucr;
- D's ucr by staking STB -- Z;

There are two situation:
if (X + Y) / 40% >= Z / 60%, the ucr A can use is X + Y + Z
if (X + Y) / 40% < Z / 60%, the ucr A can use is (X + Y) / 40%

If contract executes successfully without any exception, the ucr needed for the execution will be deducted. Generally, it is far more less than the amount of ucr this trigger can use.


Assert-style error introduction, refer to [https://developers.stabila.network/docs/vm-exception](https://developers.stabila.network/docs/vm-exception)

Note: when developer create a contract, do not set consume_user_resource_percent to 0, which means developer will undertake all the ucr consumption. If Assert-style error comes out, it will consume all ucr from the developer itsef.

To avoid unnecessary lost, 10 - 100 is recommended for consume_user_resource_percent.

## 5.4 Smart Contract Development Tool

### 5.4.1 StabilaBox

Support the build, deploy, transplant, etc. for solidity language written smart contract.
[https://developers.stabila.network/docs/stabila-box-user-guide](https://developers.stabila.network/docs/stabila-box-user-guide)

### 5.4.2 StabilaWeb
Provide http api service for the usage of smart contract.
[https://developers.stabila.network/docs/stabila-web-intro](https://developers.stabila.network/docs/stabila-web-intro)

### 5.4.3 StabilaGrid
Provide smart contract event query service.
[https://developers.stabila.network/docs/stabila-grid-intro](https://developers.stabila.network/docs/stabila-grid-intro)

## 5.5 Using Command Lines Tool to Develop Smart Contract

First you can use StabilaStudio to write, build and debug the smart contract. After you finish the development of the contract, you can copy it to [SimpleWebCompiler](https://github.com/stabilaprotocol/stabila-demo/tree/master/SmartContractTools/SimpleWebCompiler) to compile to get ABI and ByteCode. We provide a simple data read/write smart contract code example to demonstrate:

```text
pragma solidity ^0.4.0;
contract DataStore {

    mapping(uint256 => uint256) data;

    function set(uint256 key, uint256 value) public {
        data[key] = value;
    }

    function get(uint256 key) view public returns (uint256 value) {
        value = data[key];
    }
}
```

**Start a Private Net**

Make sure the fullnode code has been deployed locally, you can check if 'Produce block successfully' log appears in FullNode/logs/stabila.log

**Develop a Smart Contract**

Copy the code example above to remix to debug.

**Compile in SimpleWebCompiler for ABI and ByteCode**

Copy the code example above to SimpleWebCompiler to get ABI and ByteCode.
Because STABILA's compiler is a little different from Ethereum, so you can not get ABI and ByteCode by using Remix. But it will soon be supported.

**Using Wallet-cli to Deploy**

Download Wallet-Cli and build

```shell
# download cource code
git clone https://github.com/stabilaprotocol/wallet-cli
cd  wallet-cli
# build
./gradlew build
cd  build/libs
```

Note: You need to change the node ip and port in config.conf

start wallet-cli

```text
java -jar wallet-cli.jar
```

after started, you can use command lines to operate:

```text
importwallet
<input your password twice for your account>
<input your private key>
login
<input your password you set>
getbalance
```

deploy contract

```text
Shell
# contract deployment command
DeployContract contractName ABI byteCode constructor params isHex fee_limit consume_user_resource_percent <value> <library:address,library:address,...>

# parameters
contract_name: Contract name
ABI: ABI from SimpleWebCompiler
bytecode: ByteCode from SimpleWebCompiler
constructor: When deploy contract, this will be called. If is needed, write as constructor(uint256,string). If not, just write #
params: The parameters of the constructor, use ',' to split, like  1, "test", if no constructor, just write #
fee_limit: The STB consumption limit for the deployment, unit is UNIT(1 UNIT = 10^-6 STB)
consume_user_resource_percent: Consume user's resource percentage. It should be an integer between [0, 100]. if 0, means it does not consume user's resource until the developer's resource has been used up
value: The amount of STB transfer to the contract when deploy
library: If the contract contains library, you need to specify the library address

# example
deploycontract DataStore [{"constant":false,"inputs":[{"name":"key","type":"uint256"},{"name":"value","type":"uint256"}],"name":"set","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"key","type":"uint256"}],"name":"get","outputs":[{"name":"value","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"}] 608060405234801561001057600080fd5b5060de8061001f6000396000f30060806040526004361060485763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416631ab06ee58114604d5780639507d39a146067575b600080fd5b348015605857600080fd5b506065600435602435608e565b005b348015607257600080fd5b50607c60043560a0565b60408051918252519081900360200190f35b60009182526020829052604090912055565b600090815260208190526040902054905600a165627a7a72305820fdfe832221d60dd582b4526afa20518b98c2e1cb0054653053a844cf265b25040029 # # false 1000000 30 0
If it is deployed successfully, it will return 'Deploy the contract successfully'
```

get the contract address

```text
Your smart contract address will be: <contract address>

# in this example
Your smart contract address will be: TTWq4vMEYB2yibAbPV7gQ4mrqTyX92fha6
```

call the contract to store data, query data

```text
Shell
# call contract command
triggercontract <contract_address> <method> <args> <is_hex> <fee_limit> <value>

# parameters
contract_address: Contract address, like TTWq4vMEYB2yibAbPV7gQ4mrqTyX92fha6
method: The method called, like set(uint256,uint256) or fool(), use ',' to split the parameters. Do not leave space between parameters
args: The parameters passed to the method called, use ',' to split the parameters. Do not leave space between parameters
is_hex: whether the input parameters is Hex, false or true
fee_limit: The STB consumption limit for the trigger, unit is UNIT(1 UNIT = 10^-6 STB)
value: The amount of STB transfer to the contract when trigger

# trigger example
## set mapping 1->1
triggercontract TTWq4vMEYB2yibAbPV7gQ4mrqTyX92fha6 set(uint256,uint256) 1,1 false 1000000  0000000000000000000000000000000000000000000000000000000000000000

## get mapping key = 1
triggercontract TTWq4vMEYB2yibAbPV7gQ4mrqTyX92fha6 get(uint256) 1 false 1000000  0000000000000000000000000000000000000000000000000000000000000000
```

If the function called is constant or view, wallet-cli will return the result directly.
If it contains library, before deploy the contract you need to deploy the library first. After you deploy library, you can get the library address, then fill the address in library:address,library:address,...

```text
# for instance, using remix to get the bytecode of the contract, like:
608060405234801561001057600080fd5b5061013f806100206000396000f300608060405260043610610041576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063f75dac5a14610046575b600080fd5b34801561005257600080fd5b5061005b610071565b6040518082815260200191505060405180910390f35b600073<b>__browser/oneLibrary.sol.Math3__________<\b>634f2be91f6040518163ffffffff167c010000000000000000000000000000000000000000000000000000000002815260040160206040518083038186803b1580156100d357600080fd5b505af41580156100e7573d6000803e3d6000fd5b505050506040513d60208110156100fd57600080fd5b81019080805190602001909291905050509050905600a165627a7a7230582052333e136f236d95e9d0b59c4490a39e25dd3a3dcdc16285820ee0a7508eb8690029
```

The address of the library deployed before is: TSEJ29gnBkxQZR3oDdLdeQtQQykpVLSk54
When you deploy, you need to use browser/oneLibrary.sol.Math3:TSEJ29gnBkxQZR3oDdLdeQtQQykpVLSk54 as the parameter of deploycontract.

# 6. Built-in Contracts and API
## 6.1 Built-in Contracts
Please refer to:
[https://github.com/stabilaprotocol/documentation/blob/master/English_Documentation/STABILA_Protocol/TX_ContractTypes_Update.MD](https://github.com/stabilaprotocol/documentation/blob/master/English_Documentation/STABILA_Protocol/TX_ContractTypes_Update.MD)

## 6.2 GRPC API Introduction
Please refer to:
[https://github.com/stabilaprotocol/documentation/blob/master/English_Documentation/STABILA_Protocol/STABILA_Wallet_RPC-API.md](https://github.com/stabilaprotocol/documentation/blob/master/English_Documentation/STABILA_Protocol/STABILA_Wallet_RPC-API.md)

## 6.3 Http API Introduction
Please refer to:
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

# 7. TRC-10 Token Introduction
STABILA network support two types of token, one is TRC-20 token issued by smart contract, the other one is TRC-10 token issued by system contract.

## 7.1 How to Issue a TRC-10 Token
HTTP API:

```text
wallet/createassetissue
Description: Issue a token
demo: curl -X POST  http://127.0.0.1:8090/wallet/createassetissue -d '{
"owner_address":"41e552f6487585c2b58bc2c9bb4492bc1f17132cd0",
"name":"0x6173736574497373756531353330383934333132313538",
"abbr": "0x6162627231353330383934333132313538",
"total_supply" :4321,
"stb_num":1,
"num":1,
"start_time" : 1530894315158,
"end_time":1533894312158,
"description":"007570646174654e616d6531353330363038383733343633",
"url":"007570646174654e616d6531353330363038383733343633",
"free_asset_net_limit":10000,
"public_free_asset_net_limit":10000,
"cded_supply":{"cded_amount":1, "cded_days":2}
}'
Parameter owner_address: Owner address, default hexString
Parameter name: Token name, default hexString
Parameter abbr: Token name abbreviation, default hexString
Parameter total_supply: Token total supply
Parameter stb_num: Define the price by the ratio of stb_num/num,
Parameter num: Define the price by the ratio of stb_num/num
Parameter start_time: ICO start time
Parameter end_time: ICO end time
Parameter description: Token description, default hexString
Parameter url: Token official website url, default hexString
Parameter free_asset_net_limit: Token free asset net limit
Parameter public_free_asset_net_limit: Token public free asset net limit
Parameter cded_supply: Token staked supply
Parameter permission_id: Optional, for multi-signature use
Return: Transaction object
Note: The unit of 'stb_num' is UNIT
```

## 7.2 Participate TRC-10 Token
HTTP API:

```text
wallet/participateassetissue
Description: Participate a token
demo: curl -X POST http://127.0.0.1:8090/wallet/participateassetissue -d '{
"to_address": "41e552f6487585c2b58bc2c9bb4492bc1f17132cd0",
"owner_address":"41e472f387585c2b58bc2c9bb4492bc1f17342cd1",
"amount":100,
"asset_name":"3230313271756265696a696e67"
}'
Parameter to_address: The issuer address of the token, default hexString
Parameter owner_address: The participant address, default hexString
Parameter amount: Participate token amount
Parameter asset_name: Token id, default hexString
Parameter permission_id: Optional, for multi-signature use
Return: Transaction object
Note: The unit of 'amount' is the smallest unit of the token
```

## 7.3 TRC-10 Token Transfer
HTTP API:

```text
wallet/transferasset
Description: Transfer token
demo: curl -X POST  http://127.0.0.1:8090/wallet/transferasset -d '{"owner_address":"41d1e7a6bc354106cb410e65ff8b181c600ff14292", "to_address": "41e552f6487585c2b58bc2c9bb4492bc1f17132cd0", "asset_name": "31303030303031", "amount": 100}'
Parameter owner_address: Owner address, default hexString
Parameter to_address: To address, default hexString
Parameter asset_name: Token id, default hexString
Parameter amount: Token transfer amount
Parameter permission_id: Optional, for multi-signature use
Return: Transaction object
Note: The unit of 'amount' is the smallest unit of the token
```

# 8. Resource Model
## 8.1 Resource Model Introduction

STABILA network has 4 types of resources: Bandwidth, CPU, Storage and RAM. Benefit by STABILA's exclusive RAM model, STABILA's RAM resource is almost infinite.

STABILA network imports two resource conceptions: Bandwidth points and Ucr. Bandwidth Point represents Bandwidth, Ucr represents CPU and Storage.

Note:
- Ordinary transaction only consumes Bandwidth points
- Smart contract related transaction not only consumes Bandwidth points, but also Ucr

## 8.2 Bandwidth Points

The transaction information is stored and transmitted in the form of byte array, Bandwidth Points consumed = the number of bytes of the transaction * Bandwidth Points rate. Currently Bandwidth Points rate = 1

Such as if the number of bytes of a transaction is 200, so this transaction consumes 200 Bandwidth Points.

Note: Due to the change of the total amount of the staked STB in the network and the self-staked STB amount, the Bandwidth Points an account possesses is not fixed.

## 8.2.1 How to Get Bandwidth Points

1.&nbsp;By staking STB to get Bandwidth Points, Bandwidth Points = the amount of STB self-staked / the total amount of STB staked for Bandwidth Points in the network * 43_200_000_000

2.&nbsp;Every account has a fixed amount of free Bandwidth Points every day, it is defined in #61 network parameter, user can check the value on stabilascan(https://stabilascan.org/#/sr/committee).

### 8.2.2 Bandwidth Points Consumption

Transactions other than queries consume Bandwidth points.

A special scenario: When transferring STB or TRC-10 tokens to an account that does not yet exist, this procedure creates the account prior to the transfer.

To create an account, a flat charge of 1 STB is required. If there are insufficient Bandwidth points obtained by STB staking, an additional 0.1 STB will be spent.

Bandwidth points consumption sequence for TRC-10 transfer:

1. Free Bandwidth points.

2. TRC-10 issuer's Bandwidth points(if possible.)

3. Bandwidth points STB staking.

4. Bandwidth points obtained by STB burning, the rate = the number of bytes of the transaction * 1_000 UNIT;

Bandwidth points consumption sequence for other transactions:

1. Free Bandwidth points.

2. Bandwidth points STB staking.

3. Bandwidth points obtained by STB burning, the rate = the number of bytes of the transaction * 1_000 UNIT;

### 8.2.3 Bandwidth Points Recovery
Every 24 hours, the amount of the usage of Bandwidth points of an account will be reset to 0. For the specific formula:
![image](https://raw.githubusercontent.com/stabilaprotocol/documentation-en/master/images/bandwidthRestoreEqn.gif)

Every 24 hours, the amount of the usage of Bandwidth points of an account will be reset to 0.

## 8.3 Ucr
[5.3 Ucr Introduction](#53-ucr-introduction)

## 8.4 Resource Delegation
In STABILA network, an account can stake STB for Bandwidth or Ucr for other accounts. The primary account owns the staked STB and STABILA power, the recipient account owns the Bandwidth or Ucr. Like ordinary staking, resource delegation staking is also at least 3 days.

+ Example(Using wallet-cli)
```text
cdBalance cded_balance cded_duration [ResourceCode:0 BANDWIDTH,1 UCR] [receiverAddress]

cded_balance: the amount of STB to stake (unit UNIT)
cded_duration: the staking period (currently a fixed 3 days)
ResourceCode: 0 for Bandwidth, 1 for Ucr
receiverAddress: recipient account address
```

## 8.5 Other Fees

|Type|Fee|
| :------|:------:|
|Create a witness|9999 STB|
|Issue a TRC-10 token|1024 STB|
|Create an account|1 STB|
|Create an exchange|1024 STB|

# 9. DEX Introduction

STABILA network supports decentralized exchange(DEX) using Bancor protocol. DEX is composed of many exchange pairs.

## 9.1 What is an Exchange Pair
The term of 'Exchange Pair' describes a trade between one token with another, like A/B, A/STB.

## 9.2 Exchange Pair Creation
Any account can create an exchange pair, it burns 1024 STB.

Please refer to 'wallet/exchangecreate':
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

## 9.3 Exchange Pair Transaction
Any account can trade in the DEX. The trade follows Bancor protocol.

Please refer to 'wallet/exchangetransaction':
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

## 9.4 Exchange Pair Injection
The exchange pair creator can inject more tokens into the exchange pair. Injection can decrease the range of ratio fluctuation. If one token is injected, the other one will be injected automatically to keep the current ratio of the two tokens unchanged.

Please refer to 'wallet/exchangeinject':
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

## 9.5 Exchange Pair Withdrawal
The exchange pair creator can withdraw tokens from the exchange pair. Withdrawal can increase the range of ratio fluctuation. If one token is withdrawn, the other one will be withdrawn automatically to keep the current ratio of the two tokens unchanged.

Please refer to 'wallet/exchangewithdraw':
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

## 9.6 Query

### 9.6.1 Transaction Query
ListExchanges: Query the list of all the exchange pairs
GetPaginatedExchangeList: Query the list of all the exchange pairs by pagination
GetExchangeById: Query an exchange pair by exchange pair id

Please refer to:
[https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md](https://github.com/stabilaprotocol/documentation/blob/master/STB/Stabila-http.md)

### 9.6.2 Price Calculation
The token price is determined by the ratio of the balance of the two tokens.

### 9.6.3 Calculate the Amount of Token You Can Get
sellTokenQuant is the amount of the first_token you want to sell;
buyTokenQuant is the amount of second_token you can get;
supply = 1_000_000_000_000_000_000L;
supplyQuant = -supply * (1.0 - Math.pow(1.0 + (double) sellTokenQuant/(firstTokenBalance + sellTokenQuant, 0.0005));
buyTokenQuant = (long)balance * (Math.pow(1.0 + (double) supplyQuant / supply, 2000.0) - 1.0);


# 10. Multi-Signatures
Please refer to:
[Multi-signatures](../mechanism-algorithm/multi-signatures.md)

# 11. Shielded Transaction
Please refer to:
[Shielded Transaction](../mechanism-algorithm/shielded-transaction.md)

# 12. Wallet Introduction
## 12.1 wallet-cli Introduction
Please refer to:
[https://github.com/stabilaprotocol/wallet-cli/blob/master/README.md](https://github.com/stabilaprotocol/wallet-cli/blob/master/README.md)

## 12.2 Get Transaction ID

```text
Hash.sha256(transaction.getRawData().toByteArray())
```
## 12.3 Get Block ID

```text
private byte[] generateBlockId(long blockNum, byte[] blockHash) {
  byte[] numBytes = Longs.toByteArray(blockNum);
  byte[] hash = blockHash;
  System.arraycopy(numBytes, 0, hash, 0, 8);
  return hash;
 }
```
## 12.4 How to Build a Transaction Locally
According to the defination of the transaction, you need to fill up all the fields of the transaction.

You need to set reference block and expiration time information, so you need to connect to the Mainnet. We recommend to use the latest block on fullnode as the value of reference block, use the latest block time plus N minutes as the value of expiration time.

The network judgment condition is if (expiration > latest block time and expiration < latest block time + 24 hours) means the transaction is in period of validity. Otherwise, it will be an overdue transaction, will not be accepted by the Mainnet.

Way to set reference block: set RefBlockHash the bytes from the 8 to 16(not included) of the hash of the latest block, set BlockBytes the bytes from 6 to 8(not included) of the height of the latest block.
```text
public static Transaction setReference(Transaction transaction, Block newestBlock) {
     long blockHeight = newestBlock.getBlockHeader().getRawData().getNumber();
     byte[] blockHash = getBlockHash(newestBlock).getBytes();
     byte[] refBlockNum = ByteArray.fromLong(blockHeight);
     Transaction.raw rawData = transaction.getRawData().toBuilder()
         .setRefBlockHash(ByteString.copyFrom(ByteArray.subArray(blockHash, 8, 16)))
         .setRefBlockBytes(ByteString.copyFrom(ByteArray.subArray(refBlockNum, 6, 8)))
         .build();
     return transaction.toBuilder().setRawData(rawData).build();
   }
```
Way to set expiration time and transaction timestamp:
```text
public static Transaction createTransaction(byte[] from, byte[] to, long amount) {
     Transaction.Builder transactionBuilder = Transaction.newBuilder();
     Block newestBlock = WalletClient.getBlock(-1);

     Transaction.Contract.Builder contractBuilder = Transaction.Contract.newBuilder();
     Contract.TransferContract.Builder transferContractBuilder = Contract.TransferContract
         .newBuilder();
     transferContractBuilder.setAmount(amount);
     ByteString bsTo = ByteString.copyFrom(to);
     ByteString bsOwner = ByteString.copyFrom(from);
     transferContractBuilder.setToAddress(bsTo);
     transferContractBuilder.setOwnerAddress(bsOwner);
     try {
       Any any = Any.pack(transferContractBuilder.build());
       contractBuilder.setParameter(any);
     } catch (Exception e) {
       return null;
     }
     contractBuilder.setType(Transaction.Contract.ContractType.TransferContract);
     transactionBuilder.getRawDataBuilder().addContract(contractBuilder)
         .setTimestamp(System.currentTimeMillis()) //in the form of millisecond
         .setExpiration(newestBlock.getBlockHeader().getRawData().getTimestamp() + 10 * 60 * 60 * 1000);
     Transaction transaction = transactionBuilder.build();
     Transaction refTransaction = setReference(transaction, newestBlock);
     return refTransaction;
   }
```
## 12.5 Related Demo

Build transaction locally, signature demo, please refer to:
[https://github.com/stabilaprotocol/wallet-cli/blob/master/src/main/java/org/stabila/demo/TransactionSignDemo.java](https://github.com/stabilaprotocol/wallet-cli/blob/master/src/main/java/org/stabila/demo/TransactionSignDemo.java)
nodejs demo, please refer to:
[https://github.com/stabilaprotocol/stabila-demo/tree/master/demo/nodejs](https://github.com/stabilaprotocol/stabila-demo/tree/master/demo/nodejs)
