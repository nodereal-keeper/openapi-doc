# Sui JSON-RPC (V 0.17.0)

Sui JSON-RPC API for interaction with Sui Full node.

## sui_batchTransaction

**Transaction Builder API**

Create an unsigned batched transaction.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `single_transaction_params` : `<[RPCTransactionRequestParams]>` - list of transaction request parameters
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

**Example**
Create unsigned batch transaction data.

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_batchTransaction",
  "params": [
    "0x9b07815f04497e2e05d22cac3aa061410b20868c",
    [
      {
        "moveCallRequestParams": {
          "packageObjectId": "0x0000000000000000000000000000000000000002",
          "module": "devnet_nft",
          "function": "mint",
          "typeArguments": [],
          "arguments": [
            "Example NFT",
            "An NFT created by the Sui Command Line Tool",
            "ipfs://bafkreibngqhl3gaa7daob4i2vccziay2jjlp435cf66vhono7nrvww53ty"
          ]
        }
      },
      {
        "transferObjectRequestParams": {
          "recipient": "0xc619154c42a1c61be9902717b24b8ec168fe4896",
          "objectId": "0xefef92cbf44b581f23222c10916b17a369b4da03"
        }
      }
    ],
    "0xf5b70ccb10f1a705e061d0fdfa189618d28b0d44",
    1000
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "txBytes": "VHJhbnNhY3Rpb25EYXRhOjoBAgIAAAAAAAAAAAAAAAAAAAAAAAAAAgEAAAAAAAAAIJ0HWVK1gDbyp7VhRGWSQDxnLw2+Ep8eORE/n2Fk6ihnCmRldm5ldF9uZnQEbWludAADAAtFeGFtcGxlIE5GVAArQW4gTkZUIGNyZWF0ZWQgYnkgdGhlIFN1aSBDb21tYW5kIExpbmUgVG9vbABCaXBmczovL2JhZmtyZWlibmdxaGwzZ2FhN2Rhb2I0aTJ2Y2N6aWF5MmpqbHA0MzVjZjY2dmhvbm83bnJ2d3c1M3R5AMYZFUxCocYb6ZAnF7JLjsFo/kiW7++Sy/RLWB8jIiwQkWsXo2m02gMBAAAAAAAAACD43szzw8UY8UbelVTwPyeoPVsRhfOece81fivZ4TliUZsHgV8ESX4uBdIsrDqgYUELIIaM9bcMyxDxpwXgYdD9+hiWGNKLDUQBAAAAAAAAACBrCkFAMnZDeyXw/76JfPR2wHd+nSRv85dKxfGzUKoWxAEAAAAAAAAA6AMAAAAAAAA=",
    "gas": {
      "objectId": "0xf5b70ccb10f1a705e061d0fdfa189618d28b0d44",
      "version": 1,
      "digest": "awpBQDJ2Q3sl8P++iXz0dsB3fp0kb/OXSsXxs1CqFsQ="
    },
    "inputObjects": [
      {
        "MovePackage": "0x0000000000000000000000000000000000000002"
      },
      {
        "ImmOrOwnedMoveObject": {
          "objectId": "0xefef92cbf44b581f23222c10916b17a369b4da03",
          "version": 1,
          "digest": "+N7M88PFGPFG3pVU8D8nqD1bEYXznnHvNX4r2eE5YlE="
        }
      },
      {
        "ImmOrOwnedMoveObject": {
          "objectId": "0xf5b70ccb10f1a705e061d0fdfa189618d28b0d44",
          "version": 1,
          "digest": "awpBQDJ2Q3sl8P++iXz0dsB3fp0kb/OXSsXxs1CqFsQ="
        }
      }
    ]
  }
}
```

## sui_dryRunTransaction[#](https://docs.sui.io/sui-jsonrpc#sui_dryRunTransaction)

**Full Node API**



**Params**

- `tx_bytes` : `<Base64>` -

**Result**

- ```
  SuiTransactionEffects  <TransactionEffects>
  ```

## sui_executeTransaction

**APIs to execute transactions.**

Execute the transaction and wait for results if desired. Request types: 1. ImmediateReturn: immediately returns a response to client without waiting for any execution results. Note the transaction may fail without being noticed by client in this mode. After getting the response, the client may poll the node to check the result of the transaction. 2. WaitForTxCert: waits for TransactionCertificate and then return to client. 3. WaitForEffectsCert: waits for TransactionEffectsCert and then return to client. This mode is a proxy for transaction finality. 4. WaitForLocalExecution: waits for TransactionEffectsCert and make sure the node executed the transaction locally before returning the client. The local execution makes sure this node is aware of this transaction when client fires subsequent queries. However if the node fails to execute the transaction locally in a timely manner, a bool type in the response is set to false to indicated the case.

**Params**

- `tx_bytes` : `<Base64>` - transaction data bytes, as base-64 encoded string
- `sig_scheme` : `<SignatureScheme>` - Flag of the signature scheme that is used.
- `signature` : `<Base64>` - transaction signature, as base-64 encoded string
- `pub_key` : `<Base64>` - signer's public key, as base-64 encoded string
- `request_type` : `<ExecuteTransactionRequestType>` - The request type

**Result**

- ```
  SuiExecuteTransactionResponse   <SuiExecuteTransactionResponse>
  ```

**Example**
Execute an object transfer transaction

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_executeTransaction",
  "params": [
    "VHJhbnNhY3Rpb25EYXRhOjoAAENs56klSzDSU+RWfMr6XzbOhMgKqLyb5k4NWueWiEJ0rvMAWuZzOAkCAAAAAAAAACAyE4rjTSLmOXrzqaceTvofutFssM+v3lsohIWOg/pBRhEgRALReoQVOKG0v7bv5ChQQhoKyOwdW4TdYonhk7n4jeSplDWMn4UCAAAAAAAAACBhNSNsPnWpJeHHesNBJ8i6776gk58VJHErTTegILKM3QEAAAAAAAAA6AMAAAAAAAA=",
    "ED25519",
    "pD7Ih0rV6bwO7x0T3+cK8ySQS26jc/YHwMrPwXHvVyaqYUaaZ8e4NtWqF07mawgnFgUGP/X0s3pbEpxZEhdeAA==",
    "cfqxAQMxQkfRKmbmcMuspsUBRwEdgv9Ug5F/Z6u8Jts=",
    "WaitForLocalExecution"
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "certificate": {
      "transactionDigest": "mWZpOe42hid/gGQ1t7lOY5ohuzIXEIuo1uemz/CT96s=",
      "data": {
        "transactions": [
          {
            "TransferObject": {
              "recipient": "0x436ce7a9254b30d253e4567ccafa5f36ce84c80a",
              "objectRef": {
                "objectId": "0xa8bc9be64e0d5ae796884274aef3005ae6733809",
                "version": 2,
                "digest": "MhOK400i5jl686mnHk76H7rRbLDPr95bKISFjoP6QUY="
              }
            }
          }
        ],
        "sender": "0x11204402d17a841538a1b4bfb6efe42850421a0a",
        "gasPayment": {
          "objectId": "0xc8ec1d5b84dd6289e193b9f88de4a994358c9f85",
          "version": 2,
          "digest": "YTUjbD51qSXhx3rDQSfIuu++oJOfFSRxK003oCCyjN0="
        },
        "gasBudget": 1000
      },
      "txSignature": "AKQ+yIdK1em8Du8dE9/nCvMkkEtuo3P2B8DKz8Fx71cmqmFGmmfHuDbVqhdO5msIJxYFBj/19LN6WxKcWRIXXgBx+rEBAzFCR9EqZuZwy6ymxQFHAR2C/1SDkX9nq7wm2w==",
      "authSignInfo": {
        "epoch": 0,
        "signature": "",
        "signers_map": [
          58,
          48,
          0,
          0,
          0,
          0,
          0,
          0
        ]
      }
    },
    "effects": {
      "status": {
        "status": "success"
      },
      "gasUsed": {
        "computationCost": 100,
        "storageCost": 100,
        "storageRebate": 10
      },
      "transactionDigest": "w05NXKj399+qCDscEa9LhE/5Sz++9uNrUY2jrUxYVvo=",
      "mutated": [
        {
          "owner": {
            "AddressOwner": "0x11204402d17a841538a1b4bfb6efe42850421a0a"
          },
          "reference": {
            "objectId": "0xc8ec1d5b84dd6289e193b9f88de4a994358c9f85",
            "version": 2,
            "digest": "YTUjbD51qSXhx3rDQSfIuu++oJOfFSRxK003oCCyjN0="
          }
        },
        {
          "owner": {
            "AddressOwner": "0x436ce7a9254b30d253e4567ccafa5f36ce84c80a"
          },
          "reference": {
            "objectId": "0xa8bc9be64e0d5ae796884274aef3005ae6733809",
            "version": 2,
            "digest": "MhOK400i5jl686mnHk76H7rRbLDPr95bKISFjoP6QUY="
          }
        }
      ],
      "gasObject": {
        "owner": {
          "ObjectOwner": "0x11204402d17a841538a1b4bfb6efe42850421a0a"
        },
        "reference": {
          "objectId": "0xc8ec1d5b84dd6289e193b9f88de4a994358c9f85",
          "version": 2,
          "digest": "YTUjbD51qSXhx3rDQSfIuu++oJOfFSRxK003oCCyjN0="
        }
      },
      "events": [
        {
          "transferObject": {
            "packageId": "0x0000000000000000000000000000000000000002",
            "transactionModule": "native",
            "sender": "0x11204402d17a841538a1b4bfb6efe42850421a0a",
            "recipient": {
              "AddressOwner": "0x436ce7a9254b30d253e4567ccafa5f36ce84c80a"
            },
            "objectType": "0x2::example::Object",
            "objectId": "0xa8bc9be64e0d5ae796884274aef3005ae6733809",
            "version": 2
          }
        }
      ]
    },
    "timestamp_ms": null,
    "parsed_data": null
  }
}
```

## sui_getCommitteeInfo
**Full Node API**

Return the committee information for the asked epoch

**Params**

- `epoch` : `<uint64>` - The epoch of interest. If None, default to the latest epoch

**Result**

- ```
  CommitteeInfoResponse   <CommitteeInfoResponse>
  ```

  -  committee_info : `<[AuthorityPublicKeyBytes]>`
  -  epoch : `<uint64>`

## sui_getEvents[#](https://docs.sui.io/sui-jsonrpc#sui_getEvents)

**Event Read API**

Return list of events for a specified query criteria.

**Params**

- `query` : `<EventQuery>` - the event query criteria.
- `cursor` : `<EventID>` - optional paging cursor
- `limit` : `<uint>` - maximum number of items per page
- `descending_order` : `<boolean>` - query result ordering, default to false (ascending order), oldest record first.

**Result**

- ```
  EventPage  <Page_for_EventEnvelope_and_EventID>
  ```
**Example**
Return the Events emitted by a transaction

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getEvents",
  "params": [
    {
      "Transaction": "Vi63z0O8cI/1smk/OYkxoECgOF4LxllfNwKQCDlHxmk="
    },
    {
      "txSeq": 500,
      "eventSeq": 10
    },
    1,
    false
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "data": [
      {
        "timestamp": 0,
        "txDigest": "Vi63z0O8cI/1smk/OYkxoECgOF4LxllfNwKQCDlHxmk=",
        "id": {
          "txSeq": 0,
          "eventSeq": 0
        },
        "event": {
          "transferObject": {
            "packageId": "0x0000000000000000000000000000000000000002",
            "transactionModule": "native",
            "sender": "0xc9f373e51a0a03bfd8b1a861fb7e220b6617e575",
            "recipient": {
              "AddressOwner": "0xc677ed804f82f1c8587b98d64c00bfb46c3843bd"
            },
            "objectType": "0x2::example::Object",
            "objectId": "0x8bf6ccfa7c65a86138698cd1fdcac3dce8d8c7ce",
            "version": 2
          }
        }
      }
    ],
    "nextCursor": {
      "txSeq": 1000,
      "eventSeq": 5
    }
  }
}
```

## sui_getMoveFunctionArgTypes

**Full Node API**

Return the argument types of a Move function, based on normalized Type.

**Params**

- `package` : `<ObjectID>` -
- `module` : `<string>` -
- `function` : `<string>` -

**Result**

- ```
  Vec<MoveFunctionArgType> <[MoveFunctionArgType]>
  ```

## sui_getNormalizedMoveFunction[#](https://docs.sui.io/sui-jsonrpc#sui_getNormalizedMoveFunction)

**Full Node API**

Return a structured representation of Move function

**Params**

- `package` : `<ObjectID>` -
- `module_name` : `<string>` -
- `function_name` : `<string>` -

**Result**

- ```
  SuiMoveNormalizedFunction  <SuiMoveNormalizedFunction>
  ```
  -  is_entry : `<boolean>`
  -  parameters : `<[SuiMoveNormalizedType]>`
  -  return_ : `<[SuiMoveNormalizedType]>`
  -  type_parameters : `<[SuiMoveAbilitySet]>`
  -  visibility : `<SuiMoveVisibility>`

## sui_getNormalizedMoveModule

**Full Node API**

Return a structured representation of Move module

**Params**

- `package` : `<ObjectID>` -
- `module_name` : `<string>` -

**Result**

- ```
  SuiMoveNormalizedModule  <SuiMoveNormalizedModule>
  ```
  -  address : `<string>`
  -  exposed_functions : `<object>`
  -  file_format_version : `<uint32>`
  -  friends : `<[SuiMoveModuleId]>`
  -  name : `<string>`
  -  structs : `<object>`

## sui_getNormalizedMoveModulesByPackage

**Full Node API**

Return structured representations of all modules in the given package

**Params**

- `package` : `<ObjectID>` -

**Result**

- ```
  BTreeMap<String,SuiMoveNormalizedModule> <object>
  ```


## sui_getNormalizedMoveStruct

**Full Node API**

Return a structured representation of Move struct

**Params**

- `package` : `<ObjectID>` -
- `module_name` : `<string>` -
- `struct_name` : `<string>` -

**Result**

- ```
  SuiMoveNormalizedStruct <SuiMoveNormalizedStruct>
  ```
  -  abilities : `<SuiMoveAbilitySet>`
  -  fields : `<[SuiMoveNormalizedField]>`
  -  type_parameters : `<[SuiMoveStructTypeParameter]>`

## sui_getObject

**Read API**

Return the object information for a specified object

**Params**

- `object_id` : `<ObjectID>` - the ID of the queried object

**Result**

- ```
  GetObjectDataResponse  <ObjectRead>
  ```


**Example**
Get Object data

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getObject",
  "params": [
    "0x686464524a876b463d1297603568c40e814d9d53"
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "status": "Exists",
    "details": {
      "data": {
        "dataType": "moveObject",
        "type": "0x2::coin::Coin<0x2::coin::Coin<0x2::sui::SUI>>",
        "has_public_transfer": true,
        "fields": {
          "balance": 10000,
          "id": {
            "id": "0x686464524a876b463d1297603568c40e814d9d53"
          }
        }
      },
      "owner": {
        "AddressOwner": "0x96d23087a0fd641e91e0e00df6c012cded9ef9ba"
      },
      "previousTransaction": "XlvwQkZLXKZIi4l8AFsuF/APwLyOftPyq1nWB7DjWGs=",
      "storageRebate": 100,
      "reference": {
        "objectId": "0x686464524a876b463d1297603568c40e814d9d53",
        "version": 1,
        "digest": "2hzaTkK0FFsm+jXqGdJYsRPY4+EryiqmErME7OWyWYg="
      }
    }
  }
}
```

## sui_getObjectsOwnedByAddress

**Read API**

Return the list of objects owned by an address.

**Params**

- `address` : `<SuiAddress>` - the owner's Sui address

**Result**

- ```
  Vec<SuiObjectInfo> <[ObjectInfo]>
  ```

**Example**
Get objects owned by an address

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getObjectsOwnedByAddress",
  "params": [
    "0xd885442b44972526c4e8ce25a0416c3955d818bb"
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": [
    {
      "objectId": "0x1ab0e832877d628233e3e1d64f76b71a80ec4f33",
      "version": 0,
      "digest": "L00aZ0LFN/K7Mkc7AbHcscqslCcipHh7sYXkeRkWkm4=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "AddressOwner": "0xd885442b44972526c4e8ce25a0416c3955d818bb"
      },
      "previousTransaction": "X9sl3YQvc2q5c+h/U7SFeEJ4mh3VkWdimzxcq+R9v0g="
    },
    {
      "objectId": "0x827ec0818e00de4b297f047c3beda43d6e9f9b1b",
      "version": 0,
      "digest": "G8RM3841dOf38LLeIyOgbrw1a61hRyazE9gRAS1L5yM=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "AddressOwner": "0xd885442b44972526c4e8ce25a0416c3955d818bb"
      },
      "previousTransaction": "dVaHEgp8bfI4nDc8j74GvaemESBlJmjdVof+5z+seiw="
    },
    {
      "objectId": "0x564da689e9da76e1430d191fbc937d42f74104aa",
      "version": 0,
      "digest": "KRhx9LnRIPdlg6HaXREj8rigrGBrlApm5C79eCr1IDE=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "AddressOwner": "0xd885442b44972526c4e8ce25a0416c3955d818bb"
      },
      "previousTransaction": "EjMa3C7W9ALco5a/5t+jrIpX6giGCHD9UVz3ymr7iH0="
    },
    {
      "objectId": "0xd1e09be8e451cd3c364076623c0231898be0722c",
      "version": 0,
      "digest": "COTrIpxEy5V9Kc9zcK+KG5xULUmzyDz7nGg/t9BSbik=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "AddressOwner": "0xd885442b44972526c4e8ce25a0416c3955d818bb"
      },
      "previousTransaction": "136J57/v1Pc62j0ZrIfUU1HwGWqIC5XD6iRN0GDStlM="
    }
  ]
}
```

## sui_getObjectsOwnedByObject

**Read API**

Return the list of objects owned by an object.

**Params**

- `object_id` : `<ObjectID>` - the ID of the owner object

**Result**

- ```
  Vec<SuiObjectInfo> <[ObjectInfo]>
  ```

**Example**
Get objects owned by an object

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getObjectsOwnedByObject",
  "params": [
    "0x8196d048b7a6d04c8edc89579d86fd3fc90c52f9"
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": [
    {
      "objectId": "0xa14c6b812b94fe613c5bcebb5eeb1d449e251616",
      "version": 0,
      "digest": "bVfXH96xVNDcns23swBX0KkyaEysNSzcGj6JgCnQJO4=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "ObjectOwner": "0x8196d048b7a6d04c8edc89579d86fd3fc90c52f9"
      },
      "previousTransaction": "wdRMavXi+s3thNA7U3NRTxbj1m4ACBBRZLtzoe1YssE="
    },
    {
      "objectId": "0x8b0cbf377792e206e6b80d4d5eea64e2e70563b1",
      "version": 0,
      "digest": "WJAmxON/lBpFHvggmg+/vxtUmyuya46u4IiVFG3ye8M=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "ObjectOwner": "0x8196d048b7a6d04c8edc89579d86fd3fc90c52f9"
      },
      "previousTransaction": "zyYQwUsuJ8Hu1WwztXkDQVDqRrzPdlPgC28ktjvQTaY="
    },
    {
      "objectId": "0x8bc160c74cd844f922623fa58cdd6ef2d0531ce3",
      "version": 0,
      "digest": "WfMui9DNX54cTteFYCsUTXmIN9uwFHTINWbFsqU1QQ0=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "ObjectOwner": "0x8196d048b7a6d04c8edc89579d86fd3fc90c52f9"
      },
      "previousTransaction": "2qHdP8SvMhjXbMeqa+GABOXjjLNcyvGLfxpOPcex5/8="
    },
    {
      "objectId": "0xd3dbd682f600d1f62dc0921f7f7dbfca95055adc",
      "version": 0,
      "digest": "XhR6Hu4u3SNxRHRERonJRvaaWFkkuRxLiubUzW4eAvY=",
      "type": "0x2::coin::Coin<0x2::sui::SUI>",
      "owner": {
        "ObjectOwner": "0x8196d048b7a6d04c8edc89579d86fd3fc90c52f9"
      },
      "previousTransaction": "ZY0hvOm4i9zu4SlBRO7EfPOTiLtoD2m3QmvFKSlpL9w="
    }
  ]
}
```

## sui_getRawObject

**BCS API**

Return the raw BCS serialized move object bytes for a specified object.

**Params**

- `object_id` : `<ObjectID>` - the id of the object

**Result**

- ```
  GetRawObjectDataResponse <ObjectRead>
  ```

**Example**
Get Raw Object data

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getRawObject",
  "params": [
    "0x7d2ff374c3f870f36fb728ab4187831206d15181"
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "status": "Exists",
    "details": {
      "data": {
        "dataType": "moveObject",
        "type": "0x2::coin::Coin<0x2::sui::SUI>",
        "has_public_transfer": true,
        "version": 1,
        "bcs_bytes": "fS/zdMP4cPNvtyirQYeDEgbRUYEQJwAAAAAAAA=="
      },
      "owner": {
        "AddressOwner": "0xff7c50772417586a93633829fcba6d6e0ccb13d3"
      },
      "previousTransaction": "2/52FLgep2slXl1DUDLNhZXzfrj8AP/NoAr8XqnjnLI=",
      "storageRebate": 100,
      "reference": {
        "objectId": "0x7d2ff374c3f870f36fb728ab4187831206d15181",
        "version": 1,
        "digest": "8jJSXNzuo1pDOnsPUOjfsuIbpJfFSrMKPZrcB8FCnE0="
      }
    }
  }
}
```

## sui_getTotalTransactionNumber

**Read API**

Return the total number of transactions known to the server.

**Params**

**Result**

- ```
  u64   <uint64>
  ```

**Example**
Get total number of transactions

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getTotalTransactionNumber",
  "params": []
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": 100
}
```

## sui_getTransaction

**Read API**

Return the transaction response object.

**Params**

- `digest` : `<TransactionDigest>` - the digest of the queried transaction

**Result**

- ```
  SuiTransactionResponse  <SuiTransactionResponse>
  ```

  -  certificate : `<CertifiedTransaction>`
  -  effects : `<TransactionEffects>`
  -  parsed_data : `<[SuiParsedTransactionResponse]>`
  -  timestamp_ms : `<uint64>`

**Example**
Return the transaction response object for specified transaction digest

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getTransaction",
  "params": [
    "6ufvFXow2DG/iI/8ApYbMxIWFKyDd+e8vpUf2gbfAAs="
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "certificate": {
      "transactionDigest": "6ufvFXow2DG/iI/8ApYbMxIWFKyDd+e8vpUf2gbfAAs=",
      "data": {
        "transactions": [
          {
            "TransferObject": {
              "recipient": "0x7a91f631760853934d383634bcf7c32655009a61",
              "objectRef": {
                "objectId": "0xf1de0eae420a2e4ae1bb772ab2dd5d5a7dfa949c",
                "version": 2,
                "digest": "LrMQvKkSTl8vOM5znqrLtmAGiIBOB4RIn5D3+GbMKd8="
              }
            }
          }
        ],
        "sender": "0x1d6911910920c621a975743e49382f8db9c1c05d",
        "gasPayment": {
          "objectId": "0x0ef06908e7a54900f279be4c6a101e9a3af5c807",
          "version": 2,
          "digest": "ARL4CGSLNu+/je6KGoLeRtlQTpahEIoXjvdvVsOZYzo="
        },
        "gasBudget": 1000
      },
      "txSignature": "AFQCA34QO71gy26f8WcfCWubJ/ght4OIucz3s/o9V1S6fk2pqdbdujEQzssUUG9vO40nAijUGJET4NOVSDpuHQ6k8MuV4Ujxl9xtOeYgzm8Jf2BS6d0xIUr4jebbcPvoqQ==",
      "authSignInfo": {
        "epoch": 0,
        "signature": "",
        "signers_map": [
          58,
          48,
          0,
          0,
          0,
          0,
          0,
          0
        ]
      }
    },
    "effects": {
      "status": {
        "status": "success"
      },
      "gasUsed": {
        "computationCost": 100,
        "storageCost": 100,
        "storageRebate": 10
      },
      "transactionDigest": "sdpjc86U+dldylpA9OF2mRjuv5+64AvTjleHL0UiRGg=",
      "mutated": [
        {
          "owner": {
            "AddressOwner": "0x1d6911910920c621a975743e49382f8db9c1c05d"
          },
          "reference": {
            "objectId": "0x0ef06908e7a54900f279be4c6a101e9a3af5c807",
            "version": 2,
            "digest": "ARL4CGSLNu+/je6KGoLeRtlQTpahEIoXjvdvVsOZYzo="
          }
        },
        {
          "owner": {
            "AddressOwner": "0x7a91f631760853934d383634bcf7c32655009a61"
          },
          "reference": {
            "objectId": "0xf1de0eae420a2e4ae1bb772ab2dd5d5a7dfa949c",
            "version": 2,
            "digest": "LrMQvKkSTl8vOM5znqrLtmAGiIBOB4RIn5D3+GbMKd8="
          }
        }
      ],
      "gasObject": {
        "owner": {
          "ObjectOwner": "0x1d6911910920c621a975743e49382f8db9c1c05d"
        },
        "reference": {
          "objectId": "0x0ef06908e7a54900f279be4c6a101e9a3af5c807",
          "version": 2,
          "digest": "ARL4CGSLNu+/je6KGoLeRtlQTpahEIoXjvdvVsOZYzo="
        }
      },
      "events": [
        {
          "transferObject": {
            "packageId": "0x0000000000000000000000000000000000000002",
            "transactionModule": "native",
            "sender": "0x1d6911910920c621a975743e49382f8db9c1c05d",
            "recipient": {
              "AddressOwner": "0x7a91f631760853934d383634bcf7c32655009a61"
            },
            "objectType": "0x2::example::Object",
            "objectId": "0xf1de0eae420a2e4ae1bb772ab2dd5d5a7dfa949c",
            "version": 2
          }
        }
      ]
    },
    "timestamp_ms": null,
    "parsed_data": null
  }
}
```

## sui_getTransactions

**Full Node API**

Return list of transactions for a specified query criteria.

**Params**

- `query` : `<TransactionQuery>` - the transaction query criteria.
- `cursor` : `<TransactionDigest>` - Optional paging cursor
- `limit` : `<uint>` - Maximum item returned per page
- `descending_order` : `<boolean>` - query result ordering, default to false (ascending order), oldest record first.

**Result**

- ```
  TransactionsPage <Page_for_TransactionDigest_and_TransactionDigest>
  ```

**Example**
Return the transaction digest for specified query criteria

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_getTransactions",
  "params": [
    {
      "InputObject": "0x557dc42aaefeeb8c154d7ddc456b8aeaa3132c74"
    },
    "A1MdyY6D9uGY0tfRe6kd3H5xfPcIyTcGDwQEhzbsM/s=",
    100,
    false
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "data": [
      "4fwXyBRwidxDGP3UANtnNk7zUMlSZ22FfcN/rn2TKYo=",
      "EkXDG0IGiMFobLTRNW0XGA3YLpOkg8WrXwGimIeh2V4=",
      "W1SLYW2mOwzgfYFuie97mjghd7RCK7qif35iXqMZqgo="
    ],
    "nextCursor": "GEhmb/7SKRriSj+BdzU6BSINdlypxCGjaAg/GP5HLzk="
  }
}
```

## sui_getTransactionsInRange

**Read API**

Return list of transaction digests within the queried range.

**Params**

- `start` : `<uint64>` - the matching transactions' sequence number will be greater than or equals to the starting sequence number
- `end` : `<uint64>` - the matching transactions' sequence number will be less than the ending sequence number

**Result**

- ```
  Vec<TransactionDigest> <[TransactionDigest]>
  ```

## sui_mergeCoins

**Transaction Builder API**

Create an unsigned transaction to merge multiple coins into one coin.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `primary_coin` : `<ObjectID>` - the coin object to merge into, this coin will remain after the transaction
- `coin_to_merge` : `<ObjectID>` - the coin object to be merged, this coin will be destroyed, the balance will be added to `primary_coin`
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes <TransactionBytes>
  ```


  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_moveCall

**Transaction Builder API**

Create an unsigned transaction to execute a Move call on the network, by calling the specified function in the module of a given package.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `package_object_id` : `<ObjectID>` - the Move package ID, e.g. `0x2`
- `module` : `<string>` - the Move module name, e.g. `devnet_nft`
- `function` : `<string>` - the move function name, e.g. `mint`
- `type_arguments` : `<[TypeTag]>` - the type arguments of the Move function
- `arguments` : `<[SuiJsonValue]>` - the arguments to be passed into the Move function, in <a href="https://docs.sui.io/build/sui-json">SuiJson</a> format
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_pay[#]

**Transaction Builder API**

Send Coin<T> to a list of addresses, where `T` can be any coin type, following a list of amounts, The object specified in the `gas` field will be used to pay the gas fee for the transaction. The gas object can not appear in `input_coins`. If the gas object is not specified, the RPC server will auto-select one.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `input_coins` : `<[ObjectID]>` - the Sui coins to be used in this transaction
- `recipients` : `<[SuiAddress]>` - the recipients' addresses, the length of this vector must be the same as amounts.
- `amounts` : `<[]>` - the amounts to be transferred to recipients, following the same order
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ``` 
  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_payAllSui

**Transaction Builder API**

Send all SUI coins to one recipient. This is for SUI coin only and does not require a separate gas coin object. Specifically, what pay_all_sui does are: 1. accumulate all SUI from input coins and deposit all SUI to the first input coin 2. transfer the updated first coin to the recipient and also use this first coin as gas coin object. 3. the balance of the first input coin after tx is sum(input_coins) - actual_gas_cost. 4. all other input coins other than the first are deleted.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `input_coins` : `<[ObjectID]>` - the Sui coins to be used in this transaction, including the coin for gas payment.
- `recipient` : `<SuiAddress>` - the recipient address,
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_paySui

**Transaction Builder API**

Send SUI coins to a list of addresses, following a list of amounts. This is for SUI coin only and does not require a separate gas coin object. Specifically, what pay_sui does are: 1. debit each input_coin to create new coin following the order of amounts and assign it to the corresponding recipient. 2. accumulate all residual SUI from input coins left and deposit all SUI to the first input coin, then use the first input coin as the gas coin object. 3. the balance of the first input coin after tx is sum(input_coins) - sum(amounts) - actual_gas_cost 4. all other input coints other than the first one are deleted.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `input_coins` : `<[ObjectID]>` - the Sui coins to be used in this transaction, including the coin for gas payment.
- `recipients` : `<[SuiAddress]>` - the recipients' addresses, the length of this vector must be the same as amounts.
- `amounts` : `<[]>` - the amounts to be transferred to recipients, following the same order
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes   <TransactionBytes>
  ```
  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_publish

**Transaction Builder API**

Create an unsigned transaction to publish Move module.

**Params**

- `sender` : `<SuiAddress>` - the transaction signer's Sui address
- `compiled_modules` : `<[Base64]>` - the compiled bytes of a move module, the
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_splitCoin

**Transaction Builder API**

Create an unsigned transaction to split a coin object into multiple coins.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `coin_object_id` : `<ObjectID>` - the coin object to be spilt
- `split_amounts` : `<[]>` - the amounts to split out from the coin
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_splitCoinEqual

**Transaction Builder API**

Create an unsigned transaction to split a coin object into multiple equal-size coins.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `coin_object_id` : `<ObjectID>` - the coin object to be spilt
- `split_count` : `<uint64>` - the number of coins to split into
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_subscribeEvent

**Event Subscription, Websocket, PubSub**

Subscribe to a stream of Sui event

**Params**

- `filter` : `<EventFilter>` - the filter criteria of the event stream, see the <a href="https://docs.sui.io/build/pubsub#event-filters">Sui docs</a> for detailed examples.

**Result**

- ```
  SuiEventEnvelope <EventEnvelope>
  ```

## sui_transferObject

**Transaction Builder API**

Create an unsigned transaction to transfer an object from one address to another. The object's type must allow public transfers

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `object_id` : `<ObjectID>` - the ID of the object to be transferred
- `gas` : `<ObjectID>` - gas object to be used in this transaction, the gateway will pick one from the signer's possession if not provided
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget
- `recipient` : `<SuiAddress>` - the recipient's Sui address

**Result**

- ```
  TransactionBytes <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_transferSui

**Transaction Builder API**

Create an unsigned transaction to send SUI coin object to a Sui address. The SUI object is also used as the gas object.

**Params**

- `signer` : `<SuiAddress>` - the transaction signer's Sui address
- `sui_object_id` : `<ObjectID>` - the Sui coin object to be used in this transaction
- `gas_budget` : `<uint64>` - the gas budget, the transaction will fail if the gas cost exceed the budget
- `recipient` : `<SuiAddress>` - the recipient's Sui address
- `amount` : `<uint64>` - the amount to be split out and transferred

**Result**

- ```
  TransactionBytes  <TransactionBytes>
  ```

  -  gas : `<[ObjectRef]>` - the gas object to be used
  -  inputObjects : `<[InputObjectKind]>` - objects to be used in this transaction
  -  txBytes : `<[Base64]>` - transaction data bytes, as base-64 encoded string

## sui_tryGetPastObject

**Full Node API**

Note there is no software-level guarantee/SLA that objects with past versions can be retrieved by this API, even if the object and version exists/existed. The result may vary across nodes depending on their pruning policies. Return the object information for a specified version

**Params**

- `object_id` : `<ObjectID>` - the ID of the queried object
- `version` : `<SequenceNumber>` - the version of the queried object. If None, default to the latest known version

**Result**

- ```
  GetPastObjectDataResponse <ObjectRead>
  ```

**Example**
Get Past Object data

Request

```js
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "sui_tryGetPastObject",
  "params": [
    "0x818dd7234e049913233eb918c11638af89d575be",
    4
  ]
}
```

Response

```js
{
  "jsonrpc": "2.0",
  "result": {
    "status": "VersionFound",
    "details": {
      "data": {
        "dataType": "moveObject",
        "type": "0x2::coin::Coin<0x2::coin::Coin<0x2::sui::SUI>>",
        "has_public_transfer": true,
        "fields": {
          "balance": 10000,
          "id": {
            "id": "0x818dd7234e049913233eb918c11638af89d575be"
          }
        }
      },
      "owner": {
        "AddressOwner": "0xb99003d30a245ac74a02e26e45cb80ee1b9c00a9"
      },
      "previousTransaction": "M0XOXxK+qf/gR0jWaWwwYxc1GTrqlbj5CCwQYgwNbj8=",
      "storageRebate": 100,
      "reference": {
        "objectId": "0x818dd7234e049913233eb918c11638af89d575be",
        "version": 4,
        "digest": "yVlrRTjQ3CS+w3G7PkY0Vh+IkuYEAhq5a2Mur6Ip7D0="
      }
    }
  }
}
```