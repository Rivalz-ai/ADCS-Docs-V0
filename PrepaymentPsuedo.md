**Provide basic function for ADCS onchain payment**

**Disclaimer: This is a work in progress. Everything can change.**

This contract includes the functions to manage accounts and payments. It allows users to create accounts, deposit tokens, and manage their funds securely on the blockchain. The following sections outline the primary functionalities provided by the contract.

1. __Prequisite__
Before interacting with the contract, ensure you have a basic understanding of Solidity and smart contract development. This contract assumes familiarity with Ethereum's native token (ETH) and basic blockchain concepts.

__Create an Account__

The `createAccount` function allows users to create a new account within the contract. Each account is uniquely identified by an account ID.

```
function createAccount() external returns (uint64) {
    uint64 currentAccId = sCurrentAccId + 1;
    sCurrentAccId = currentAccId;

    Account acc = new Account(currentAccId, msg.sender);
    sAccIdToAccount[currentAccId] = acc;

    emit AccountCreated(currentAccId, address(acc), msg.sender);
    return currentAccId;
}
```

__deposit payment token to an account__

Let's assume we'll deposit a native token to an account. We can also expand to other ERC20 tokens
The deposit function allows users to deposit native tokens (ETH) into a specified account. The function ensures that the account exists and handles the transfer of funds securely.


```
function deposit(uint64 accId) external payable {
    Account account = sAccIdToAccount[accId];
    if (address(account) == address(0)) {
        revert InvalidAccount();
    }
    uint256 amount = msg.value;
    uint256 balance = account.getBalance();

    (bool sent, ) = payable(account).call{value: msg.value}("");
    if (!sent) {
        revert FailedToDeposit();
    }

    emit AccountBalanceIncreased(accId, balance, balance + amount);
}
```

__Add a consumer to an account__

```
function addConsumer(uint64 accId, address consumer) external onlyAccountOwner(accId) {
    sAccIdToAccount[accId].addConsumer(consumer);
    emit AccountConsumerAdded(accId, consumer);
}
```

__Other Functions__

+ Transfer account ownership
+ Accept account ownership
+ Remove consumer
+ Cancel account
+ Withdraw funds from account
