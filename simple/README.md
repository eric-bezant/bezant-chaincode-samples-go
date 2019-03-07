# `Go Chaincode` simple code
This guide will walk you through to write `Hyperledger Fabric` chaincode based on `Go`

# Environment
+ `Go`
+ `Hyperledger Fabric`


# `Chaincode` Development Example
Writing your own chain code requires an understanding of the `Fabric` platform and also`Go`. An application is a basic example chaincode that creates assets (key-value pairs) on a ledger.

## Download Code
```sh
$ git clone https://github.com/bezant-developers/bezant-chaincode-samples-go.git
```

## Basic code
```go
package main

import (
	"fmt"

	"github.com/hyperledger/fabric/core/chaincode/shim"
	pb "github.com/hyperledger/fabric/protos/peer"
)

type SimpleChaincode struct {
}

func (t *SimpleChaincode) Init(stub shim.ChaincodeStubInterface) pb.Response {
	return shim.Success(nil)
}

func (t *SimpleChaincode) Invoke(stub shim.ChaincodeStubInterface) pb.Response {
	return shim.Success(nil)
}

func main() {
	err := shim.Start(new(SimpleChaincode))
	if err != nil {
		fmt.Printf("Error starting Simple chaincode: %s", err)
	}
}
```

## Function
Init is called during chaincode instantiation to initialize and also chaincode upgrade calls this function as well.
```go
func (t *SimpleChaincode) Init(stub shim.ChaincodeStubInterface) pb.Response {
	fmt.Println("========= Init =========")
	return shim.Success(nil)
}
```

The Invoke method is called in response to receiving an invoke transaction to process transaction proposals.
```go
func (t *SimpleChaincode) Invoke(stub shim.ChaincodeStubInterface) pb.Response {
	fmt.Println("========= Invoke =========")
	function, args := stub.GetFunctionAndParameters()

	if function == "put" {
		return t.put(stub, args)
	} else if function == "get" {
		return t.get(stub, args)
	}

	return shim.Error("No function name : " + function + " found")
}
```

Save the keys and values to the ledger.
```go
func (t *SimpleChaincode) put(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	var err error
	var keyString, valString string

	if len(args) != 2 {
		return shim.Error("Incorrect number of arguments. Expecting 2")
	}

	keyString = args[0]
	valString = args[1]

	err = stub.PutState(keyString, []byte(valString))
	if err != nil {
		return shim.Error(err.Error())
	}

	return shim.Success(nil)
}
```

Get returns the value of the specified asset key
``` go
func (t *SimpleChaincode) get(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	var keyString string
	var err error

	if len(args) != 1 {
		return shim.Error("Incorrect number of arguments. Expecting 1")
	}

	keyString = args[0]

	// Get the state from the ledger
	resultValueBytes, err := stub.GetState(keyString)
	if err != nil {
		return shim.Error("Failed to get state for" + keyString)
	}

	if resultValueBytes == nil {
		return shim.Error("Failed to get state for" + keyString)
	}

	return shim.Success(resultValueBytes)
}
```
