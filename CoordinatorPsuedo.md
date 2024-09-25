**Verifiable offchain inference response**

A verifiable offchain inference response is a mechanism similar to the verifiable randomness function (VRF). However, instead of generating a random value, it provides a response from AI agents. This response is generated offchain and then verified onchain to ensure its integrity and authenticity. The process involves:

1. **Offchain Inference**: AI agents perform computations or inferences offchain based on the provided input data.
2. **Response Generation**: The AI agents generate a response, which is then signed cryptographically to ensure it has not been tampered with.
3. **Onchain Verification**: The signed response is submitted to the blockchain, where it is verified using the cryptographic signature. This ensures that the response is genuine and has not been altered.

This mechanism allows for complex AI computations to be performed offchain, reducing the computational load on the blockchain while still ensuring the trustworthiness of the results.

**Coordinator Contract**
The Coordinator contract provides the basic methods to achieve verifiable offchain inference responses. It acts as an intermediary between the onchain smart contract and the offchain AI agents. The primary responsibilities of the Coordinator contract include:

**Some core function of a Coordinator contract**

__Request Inferences__

This function allows users to request an inference by providing the necessary input data. It increments the request counter, stores the request details, and emits an event to notify offchain AI agents of the new inference request.

```
function requestInference(bytes calldata schema, uint64 accId, uint32 callbackGasLimit) external returns (uint256) {

       // check validity of a request
       bool isValidReq = sPrepayment.isValidReq(accId);
        if (!isValidReq) {
            revert InvalidAccRequest();
        }

        requestCounter++;
        uint256 requestId = requestCounter;

        requests[requestId] = InferenceRequest({
            requester: msg.sender,
            inputData: inputData,
            callbackGasLimit,
            accId,
            fulfilled: false
        });

        emit InferenceRequested(requestId, msg.sender, schema);
        return requestId;
    }
```

__Inferense response__

This function is used by AI agents to submit their inference proof. The responseData can only be extracted from a valid proof.

```
    function submitInferenceResponse(uint256 requestId, bytes calldata responseData, IFERENCE.Proof memory proof, bytes signature) external {

        // Verify the signature here, we can use any library
        require(verifySignature(responseData, signature), "Invalid signature");

        InferenceRequest storage request = requests[requestId];
        require(!request.fulfilled, "Request already fulfilled");
        

        // Get the response from submitted proof
        (uint256 requestId, uint256 responseData) = getResponseFromProof(proof, rc);

        request.fulfilled = true;
        // Further processing of the responseData will be implemented in Consumer Contract 
        emit InferenceResponseReceived(requestId, responseData);

        // Further processing of the response 
    }
```

__Internal function to verify signature using Zksync__

```
function verifySignature(bytes memory data, bytes memory signature) internal view returns (bool) {
    // Implement zkSync signature verification logic here
    // Example using zkSync's ECDSA verification
    address signer = zkSyncLibrary.recoverSigner(data, signature);
    return signer != address(0);
}
```
These core functions enable the Coordinator contract to handle requests for offchain inferences, receive and verify responses, and ensure the integrity and authenticity of the data provided by AI agents.