**Schema**

The schema is a critical parameter for offchain inference. It defines the structure and content of the inference request, specifying the following:

1. **Request Instructions**: Detailed instructions on the nature of the request, including the discription
2. **Reference Data**: The data that the inference should reference or utilize, including any necessary context or historical data.
3. **Response Format**: The expected format of the inference response, ensuring that the output is structured in a way that can be easily processed and integrated into subsequent workflows.

The schema ensures that the offchain inference process is well-defined and that the resulting data is usable and actionable.
The schema struct can be define in the consumer contract

```
    struct Schema {
        string categoryId;
        string requestInstructions;
        string referenceData;
        string responseFormat;
    }
``
You can convert into bytes and use it as a parameter for `requestInference` function

```
    function encodeSchema(Schema memory schema) public pure returns (bytes memory) {
        return abi.encode(schema);
    }

    function decodeSchema(bytes memory data) public pure returns (Schema memory) {
        return abi.decode(data, (Schema));
    }
```
    
