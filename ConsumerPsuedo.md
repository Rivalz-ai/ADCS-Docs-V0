**Consumer Contract**

The Consumer contract inherits from the Coordinator contract and interfaces with the Prepayment contract. It is designed to request AI-based inferences to decide whether to buy, sell, or do nothing with its Bitcoin holdings. The contract handles the request and response process, ensuring that the decisions are based on verifiable offchain inference responses.

**Key Responsibilities**

1. **Request Inference**: The Consumer contract requests an inference from the Coordinator contract, providing the necessary input data.
2. **Handle Response**: The Consumer contract receives and processes the inference response to make a decision on whether to buy, sell, or do nothing with its Bitcoin.

**Core Functions**

1. **requestTradeDecision**

This function allows the Consumer contract to request an inference from the Coordinator contract. It provides the necessary input data, including the schema, account ID, and callback gas limit.

```solidity

function requestTradeDecision(bytes calldata schema, uint64 accId, uint32 callbackGasLimit) external returns (uint256) {
    uint256 requestId = requestInference(schema, accId, callbackGasLimit);
    emit TradeDecisionRequested(requestId, msg.sender);
    return requestId;
}
```
2. __handleInferenceResponse__

This function processes the inference response received from the Coordinator contract. Based on the response, it decides whether to buy, sell, or do nothing with its Bitcoin.

```
function handleInferenceResponse(uint256 requestId, bytes calldata responseData) external {
    
    require(msg.sender == address(coordinator), "Only coordinator can call this function");
    // Process the response data to make a trade decision
    string memory decision = abi.decode(responseData, (string));

    if (keccak256(abi.encodePacked(decision)) == keccak256(abi.encodePacked("buy"))) {
        // Logic to buy Bitcoin
        emit TradeDecisionMade(requestId, "buy");
    } else if (keccak256(abi.encodePacked(decision)) == keccak256(abi.encodePacked("sell"))) {
        // Logic to sell Bitcoin
        emit TradeDecisionMade(requestId, "sell");
    } else {
        // Do nothing
        emit TradeDecisionMade(requestId, "do nothing");
    }
}
```