# Contract

## Smart Contract Introduction

Smart contract is a computerized transaction protocol that automatically implements its terms. Smart contract is the same as common contract, they all define the terms and rules related to the participants. Once the contract is started, it can runs in the way it is designed.

STABILA smart contract support Solidity language in (Ethereum). Currently recommend Solidity language version is 0.4.24 ~ 0.4.25. Write a smart contract, then build the smart contract and deploy it to STABILA network. When the smart contract is triggered, the corresponding function will be executed automatically.

## Smart Contract Features
STABILA virtual machine is based on Ethereum solidity language, it also has STABILA's own features.

### 1. Smart Contract
STABILA VM is compatible with Ethereum's smart contract, using protobuf to define the content of the contract:
``` solidity
message SmartContract {
  message ABI {
    message Entry {
      enum EntryType {
        UnknownEntryType = 0;
        Constructor = 1;
        Function = 2;
        Event = 3;
        Fallback = 4;
        Receive = 5;
        Error = 6;
      }
      message Param {
        bool indexed = 1;
        string name = 2;
        string type = 3;
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
  string name = 7;
  int64 origin_ucr_limit = 8;
  bytes code_hash = 9;
  bytes stb_hash = 10;
}
```
origin_address: smart contract creator address
contract_address: smart contract address
abi: the api information of the all the function of the smart contract
bytecode: smart contract byte code
call_value: STB transferred into smart contract while call the contract
consume_user_resource_percent: resource consumption percentage set by the developer
name: smart contract name
origin_ucr_limit: ucr consumption of the developer limit in one call, must greater than 0. For the old contracts, if this parameter is not set, it will be set 0, developer can use updateUcrLimit api to update this parameter (must greater than 0)

Through other two grpc message types CreateSmartContract and TriggerSmartContract to create and use smart contract.

### 2. The Usage of the Function of Smart Contract

* **constant function and inconstant function**

There are two types of function according to whether any change will be made to the properties on the chain: constant function and inconstant function
Constant function uses view/pure/constant to decorate, will return the result on the node it is called and not be broadcasted in the form of a transaction
Inconstant function will be broadcasted in the form of a transaction while be called, the function will change the data on the chain, such as transfer, changing the value of the internal variables of contracts, etc.

Note: If you use create command inside a contract (CREATE instruction), even use view/pure/constant to decorate the dynamically created contract function, this function will still be treated as inconstant function, be dealt in the form of transaction.

* **message calls**

Message calls can call the functions of other contracts, also can transfer STB to the accounts of contract and none-contract. Like the common STABILA triggercontract, Message calls have initiator, recipient, data, transfer amount, fees and return attributes. Every message call can generate a new one recursively. Contract can define the distribution of the remaining ucr in the internal message call. If it comes with OutOfUcrException in the internal message call, it will return false, but not error. In the meanwhile, only the gas sent with the internal message call will be consumed, if ucr is not specified in call.value(ucr), all the remaining ucr will be used.

* **delegate call/call code/libary**

There is a special type of message call, delegate call. The difference with common message call is the code of the target address will be run in the context of the contract that initiates the call, msg.sender and msg.value remain unchanged. This means a contract can dynamically loadcode from another address while running. Storage, current address and balance all point to the contract that initiates the call, only the code is get from the address being called. This gives Solidity the ability to achieve the 'lib' function: the reusable code lib can be put in the storage of a contract to implement complex data structure library.

* **CREATE command**

This command will create a new contract with a new address. The only difference with Ethereum is the newly generated STABILA address used the smart contract creation transaction id and the hash of nonce called combined. Different from Ethereum, the defination of nonce is the comtract sequence number of the creation of the root call. Even there are many CREATE commands calls, contract number in sequence from 1. Refer to the source code for more detail.
Note: Different from creating a contract by grpc's deploycontract, contract created by CREATE command does not store contract abi.

* **built-in function and built-in function attribute (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)**

```
1)SVM is compatible with solidity language's transfer format, including:
- accompany with constructor to call transfer
- accompany with internal function to call transfer
- use transfer/send/call/callcode/delegatecall to call transfer

Note: STABILA's smart contract is different from STABILA's system contract, if the transfer to address does not exist it can not create an account by smart contract transfer.

2)Different accouts vote for SuperNode (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

3)SuperNode gets all the reward (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

4)SuperNode approves or disappoves the proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

5)SuperNode proposes a proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

6)SuperNode deletes  a proposal (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

7)STABILA byte address converts to solidity address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

8)STABILA string address converts to solidity address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

9)Send token to target address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

10)Query token amount of target address (Since Odyssey-v3.1.1, SVM built-in function is not supported temporarily)

11)Compatible with all the built-in functions of Ethereum
```
Note: Ethereum's RIPEMD160 function is not recommended, because the return of STABILA is a hash result based on STABILA's sha256, not an accurate Ethereum RIPEMD160.

### 3. Contract Address Using in Solidity Language

Ethereum VM address is 20 bytes, but STABILA's VM address is 21 bytes.

* **address conversion**

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

* **address judgement**

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

* **variable assignment**

Solidity has address constant assignment, if using 21 bytes address the compiler will throw out an error, so you should use 20 bytes address, like:
```text
function assignAddress() public view {
        // address newAddress = 0x41ca35b7d915458ef540ade6068dfe2f44e8fa733c; // compile error
        address newAddress = 0xca35b7d915458ef540ade6068dfe2f44e8fa733c;
        // do something
}
```
If you want to use STABILA address of string type (TLLM21wteSPs4hKjbxgmH1L6poyMjeTbHm) please refer to (2-4-7,2-4-8).

### 4. The Special Constants Differ from Ethereum

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


Each command of smart contract consume system resource while running, we use 'Ucr' as the unit of the consumption of the resource.
