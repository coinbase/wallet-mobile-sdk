---
title: "API Reference"
slug: "unity-api-reference"
category: "633d1d37bc7103008654c123"
---

# Mobile Wallet Protocol - Unity SDK

The Unity SDK provides methods to interact with mobile wallets using the Mobile Wallet Protocol. The main class `MWPClient` handles wallet connections and requests.

## MWPClient

A client for interacting with mobile wallets using the Mobile Wallet Protocol.

### Initialization

Create a new client with the necessary options:

```csharp
var options = new MWPClientOptions
{
    Metadata = new AppMetadata()
    {
        Name = "My Dapp",
        LogoUrl = "https://example.com/logo.png",
        ChainIds = new[] { "0x1" },
        CustomScheme = "mydapp://"
    },
   Wallet = Wallet.CreateWebWallet(
      name: "Rapid fire wallet",
      // The scheme should be the target wallet's URL
      scheme: "https://id.sample.openfort.xyz",
      iconUrl: "https://purple-magnificent-bat-958.mypinata.cloud/ipfs/QmfQrh2BiCzugFauYF9Weu9SFddsVh9qV82uw43cxH8UDV"
   )
};

var client = new MWPClient(options);
```

## Actions

The MWPClient provides methods for various wallet interactions. Below is a list of supported actions:

| Action                                                  | Method                       | Description                                 |
| :------------------------------------------------------ | :--------------------------- | :------------------------------------------ |
| [EthRequestAccounts](#ethrequestaccounts)               | `eth_requestAccounts`        | Request user accounts                       |
| [PersonalSign](#personalsign)                           | `personal_sign`              | Sign a message with a personal signature    |
| [EthSignTransaction](#ethsigntransaction)               | `eth_signTransaction`        | Sign a transaction without sending          |
| [EthSendTransaction](#ethsendtransaction)               | `eth_sendTransaction`        | Sign and send a transaction                 |
| [EthSignTypedDataV4](#ethsigntypeddatav4)               | `eth_signTypedData_v4`       | Sign typed structured data (EIP-712)        |
| [WalletSwitchEthereumChain](#walletswitchethereumchain) | `wallet_switchEthereumChain` | Switch to a different Ethereum chain        |
| [WalletAddEthereumChain](#walletaddethereumchain)       | `wallet_addEthereumChain`    | Add a new Ethereum chain to wallet          |
| [WalletWatchAsset](#walletwatchasset)                   | `wallet_watchAsset`          | Add and track a token asset                 |
| [WalletSendCalls](#walletsendcalls)                     | `wallet_sendCalls`           | Submit a batch of calls to the wallet       |
| [WalletShowCallsStatus](#walletshowcallsstatus)         | `wallet_showCallsStatus`     | Show information about a call bundle        |
| [WalletGrantPermissions](#walletgrantpermissions)       | `wallet_grantPermissions`    | Request permissions to execute transactions |

## EthRequestAccounts

Requests the accounts of the user from the wallet application.

### Method

```csharp
public async Task<Result<string[]>> EthRequestAccounts()
```

### Return Value

A `Result<string[]>` containing:
- On success: An array of Ethereum addresses
- On failure: An error message

### Example

```csharp
var result = await client.EthRequestAccounts();

if (result.IsSuccess)
{
    string[] accounts = result.Value;
    // Use the accounts
}
else
{
    Debug.LogError($"Failed to get accounts: {result.Error}");
}
```

## PersonalSign

Sign a message by calculating an Ethereum specific signature with: `sign(keccak256("\x19Ethereum Signed Message:\n" + len(message) + message))`.

### Method

```csharp
public async Task<Result<string>> PersonalSign(PersonalSignParams personalSignParams)
```

### Parameters

| Name               | Type                 | Description                              |
| :----------------- | :------------------- | :--------------------------------------- |
| personalSignParams | `PersonalSignParams` | Parameters for the personal sign request |

The `PersonalSignParams` class contains:

| Name      | Type     | Description               |
| :-------- | :------- | :------------------------ |
| Challenge | `string` | Message data to sign      |
| Address   | `string` | Address to sign data with |

### Return Value

A `Result<string>` containing:
- On success: The signature as a hex string
- On failure: An error message

### Example

```csharp
var params = new PersonalSignParams
{
    Address = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
    Challenge = "0xdeadbeaf"
};

var result = await client.PersonalSign(params);

if (result.IsSuccess)
{
    string signature = result.Value;
    // Use the signature
}
else
{
    Debug.LogError($"Failed to sign message: {result.Error}");
}
```

## EthSignTransaction

Signs a transaction that can be submitted to the network at a later time.

### Method

```csharp
public async Task<Result<EthSignTransactionResult>> EthSignTransaction(EthSignTransactionParams signTransactionParams)
```

### Parameters

| Name                  | Type                       | Description                           |
| :-------------------- | :------------------------- | :------------------------------------ |
| signTransactionParams | `EthSignTransactionParams` | Parameters of the transaction to sign |

The `EthSignTransactionParams` class contains:

| Name                 | Type       | Description                                              |
| :------------------- | :--------- | :------------------------------------------------------- |
| Type                 | `string`   | Transaction type                                         |
| Nonce                | `string`   | Transaction nonce                                        |
| To                   | `string`   | Recipient address                                        |
| From                 | `string`   | Sender address                                           |
| Value                | `string`   | Transaction value in wei                                 |
| Data                 | `string`   | Transaction data                                         |
| GasLimit             | `string`   | Gas limit for the transaction                            |
| GasPrice             | `string`   | Gas price in wei                                         |
| MaxPriorityFeePerGas | `string`   | Maximum priority fee per gas (for EIP-1559 transactions) |
| MaxFeePerGas         | `string`   | Maximum fee per gas (for EIP-1559 transactions)          |
| AccessList           | `object[]` | EIP-2930 access list                                     |
| ChainId              | `string`   | Chain ID for the transaction                             |

### Return Value

A `Result<EthSignTransactionResult>` containing:
- On success: An object with `V`, `R`, and `S` signature components
- On failure: An error message

The `EthSignTransactionResult` class contains:

| Name | Type     | Description               |
| :--- | :------- | :------------------------ |
| V    | `string` | ECDSA Recovery ID         |
| R    | `string` | ECDSA signature parameter |
| S    | `string` | ECDSA signature parameter |

### Example

```csharp
var params = new EthSignTransactionParams
{
    From = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
    To = "0x000000000000000000000000000000000000dEaD",
    Value = "10000000000000",
    Data = "0x",
    Nonce = "1",
    GasPrice = "30000000000",
    MaxFeePerGas = "60000000000",
    MaxPriorityFeePerGas = "2500000000",
    GasLimit = "21000",
    ChainId = "1"
};

var result = await client.EthSignTransaction(params);

if (result.IsSuccess)
{
    var signature = result.Value;
    // Use signature.V, signature.R, signature.S
}
else
{
    Debug.LogError($"Failed to sign transaction: {result.Error}");
}
```

## EthSendTransaction

Send a transaction, or create a contract if the `data` field contains code.

### Method

```csharp
public async Task<Result<string>> EthSendTransaction(EthSendTransactionParams sendTransactionParams)
```

### Parameters

| Name                  | Type                       | Description                           |
| :-------------------- | :------------------------- | :------------------------------------ |
| sendTransactionParams | `EthSendTransactionParams` | Parameters of the transaction to send |

The `EthSendTransactionParams` class contains:

| Name     | Type     | Description                   |
| :------- | :------- | :---------------------------- |
| To       | `string` | Recipient address             |
| From     | `string` | Sender address                |
| Gas      | `string` | Gas limit for the transaction |
| Value    | `string` | Transaction value in wei      |
| Data     | `string` | Transaction data              |
| GasPrice | `string` | Gas price in wei              |

### Return Value

A `Result<string>` containing:
- On success: The transaction hash
- On failure: An error message

### Example

```csharp
var params = new EthSendTransactionParams
{
    From = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
    To = "0x000000000000000000000000000000000000dEaD",
    Value = "10000000000000",
    Data = "0x",
    Gas = "21000",
    GasPrice = "30000000000"
};

var result = await client.EthSendTransaction(params);

if (result.IsSuccess)
{
    string txHash = result.Value;
    // Use the transaction hash
}
else
{
    Debug.LogError($"Failed to send transaction: {result.Error}");
}
```

## EthSignTypedDataV4

Signs typed structured data according to EIP-712.

### Method

```csharp
public async Task<Result<string>> EthSignTypedDataV4(string address, EthSignTypedDataV4Params typedData)
```

### Parameters

| Name      | Type                       | Description               |
| :-------- | :------------------------- | :------------------------ |
| address   | `string`                   | Address to sign data with |
| typedData | `EthSignTypedDataV4Params` | Typed data to sign        |

The `EthSignTypedDataV4Params` class contains:

| Name        | Type                                   | Description                   |
| :---------- | :------------------------------------- | :---------------------------- |
| Types       | `Dictionary<string, TypedDataField[]>` | Type definitions              |
| PrimaryType | `string`                               | The primary type being signed |
| Domain      | `EIP712Domain`                         | Domain separator values       |
| Message     | `object`                               | The message to sign           |

### Return Value

A `Result<string>` containing:
- On success: The signature as a hex string
- On failure: An error message

### Example

```csharp
var domain = new EIP712Domain
{
    Name = "My Dapp",
    Version = "1",
    ChainId = "1",
    VerifyingContract = "0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC"
};

var typedData = new EthSignTypedDataV4Params
{
    Types = new Dictionary<string, TypedDataField[]>
    {
        ["EIP712Domain"] = new TypedDataField[]
        {
            new TypedDataField { Name = "name", Type = "string" },
            new TypedDataField { Name = "version", Type = "string" },
            new TypedDataField { Name = "chainId", Type = "uint256" },
            new TypedDataField { Name = "verifyingContract", Type = "address" }
        },
        ["Person"] = new TypedDataField[]
        {
            new TypedDataField { Name = "name", Type = "string" },
            new TypedDataField { Name = "wallet", Type = "address" }
        }
    },
    PrimaryType = "Person",
    Domain = domain,
    Message = new 
    {
        name = "John Doe",
        wallet = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826"
    }
};

var result = await client.EthSignTypedDataV4("0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826", typedData);

if (result.IsSuccess)
{
    string signature = result.Value;
    // Use the signature
}
else
{
    Debug.LogError($"Failed to sign typed data: {result.Error}");
}
```

## WalletSwitchEthereumChain

Switches the active chain of the wallet application.

### Method

```csharp
public async Task<Result> WalletSwitchEthereumChain(string chainId)
```

### Parameters

| Name    | Type     | Description                  |
| :------ | :------- | :--------------------------- |
| chainId | `string` | ID of the chain to switch to |

### Return Value

A `Result` containing:
- On success: No value, just success status
- On failure: An error message

### Example

```csharp
var result = await client.WalletSwitchEthereumChain("0x89"); // Polygon Network

if (result.IsSuccess)
{
    // Chain switched successfully
}
else
{
    Debug.LogError($"Failed to switch chain: {result.Error}");
}
```

## WalletAddEthereumChain

Adds a new chain to the wallet application.

### Method

```csharp
public async Task<Result<AddEthereumChainResult>> WalletAddEthereumChain(WalletAddEthereumChainParams chainParams)
```

### Parameters

| Name        | Type                           | Description                    |
| :---------- | :----------------------------- | :----------------------------- |
| chainParams | `WalletAddEthereumChainParams` | Parameters of the chain to add |

The `WalletAddEthereumChainParams` class contains:

| Name              | Type             | Description                                   |
| :---------------- | :--------------- | :-------------------------------------------- |
| ChainId           | `string`         | ID of the chain to add                        |
| ChainName         | `string`         | Name of the chain                             |
| RpcUrls           | `string[]`       | List of RPC URLs                              |
| IconUrls          | `string[]`       | List of icon URLs                             |
| NativeCurrency    | `NativeCurrency` | Information about the chain's native currency |
| BlockExplorerUrls | `string[]`       | List of block explorer URLs                   |
| Currency          | `string`         | Currency symbol                               |

The `NativeCurrency` class contains:

| Name     | Type     | Description                   |
| :------- | :------- | :---------------------------- |
| Name     | `string` | Name of the native currency   |
| Symbol   | `string` | Symbol of the native currency |
| Decimals | `int`    | Decimals of precision         |

### Return Value

A `Result<AddEthereumChainResult>` containing:
- On success: An empty result object
- On failure: An error message

### Example

```csharp
var params = new WalletAddEthereumChainParams
{
    ChainId = "0x89",
    ChainName = "Polygon",
    RpcUrls = new[] { "https://polygon-rpc.com/" },
    IconUrls = new[] { "https://polygon.technology/favicon.ico" },
    NativeCurrency = new NativeCurrency
    {
        Name = "MATIC",
        Symbol = "MATIC",
        Decimals = 18
    },
    BlockExplorerUrls = new[] { "https://polygonscan.com/" }
};

var result = await client.WalletAddEthereumChain(params);

if (result.IsSuccess)
{
    // Chain added successfully
}
else
{
    Debug.LogError($"Failed to add chain: {result.Error}");
}
```

## WalletWatchAsset

Requests that the user track the specified token in their wallet.

### Method

```csharp
public async Task<Result<bool>> WalletWatchAsset(WalletWatchAssetParams watchAssetParams)
```

### Parameters

| Name             | Type                     | Description                      |
| :--------------- | :----------------------- | :------------------------------- |
| watchAssetParams | `WalletWatchAssetParams` | Parameters of the token to track |

The `WalletWatchAssetParams` class contains:

| Name    | Type                      | Description                                   |
| :------ | :------------------------ | :-------------------------------------------- |
| Type    | `string`                  | Type of token asset (e.g., "ERC20", "ERC721") |
| Options | `WalletWatchAssetOptions` | Data of the asset to watch                    |

The `WalletWatchAssetOptions` class contains:

| Name     | Type     | Description                    |
| :------- | :------- | :----------------------------- |
| Address  | `string` | Contract address for the token |
| Symbol   | `string` | Symbol for the token           |
| Decimals | `int`    | Decimals of precision          |
| Image    | `string` | Logo image URL for the token   |
| TokenId  | `string` | Token ID (for ERC-721 tokens)  |

### Return Value

A `Result<bool>` containing:
- On success: `true` if the token was successfully added
- On failure: An error message

### Example

```csharp
var params = new WalletWatchAssetParams
{
    Type = "ERC20",
    Options = new WalletWatchAssetOptions
    {
        Address = "0xcf664087a5bb0237a0bad6742852ec6c8d69a27a",
        Symbol = "WONE",
        Decimals = 18,
        Image = "https://s2.coinmarketcap.com/static/img/coins/64x64/11696.png"
    }
};

var result = await client.WalletWatchAsset(params);

if (result.IsSuccess && result.Value)
{
    // Token added successfully
}
else
{
    Debug.LogError($"Failed to add token: {result.Error}");
}
```

## WalletSendCalls

Requests that a wallet submits a batch of calls.

### Method

```csharp
public async Task<Result<string>> WalletSendCalls(WalletSendCallsParams walletSendCallsParams)
```

### Parameters

| Name                  | Type                    | Description                       |
| :-------------------- | :---------------------- | :-------------------------------- |
| walletSendCallsParams | `WalletSendCallsParams` | Parameters of the calls to submit |

The `WalletSendCallsParams` class contains:

| Name         | Type     | Description               |
| :----------- | :------- | :------------------------ |
| Version      | `string` | Version of the protocol   |
| ChainId      | `string` | Chain ID                  |
| From         | `string` | Sender address            |
| Calls        | `Call[]` | Array of calls to execute |
| Capabilities | `object` | Additional capabilities   |

The `Call` class contains:

| Name  | Type     | Description       |
| :---- | :------- | :---------------- |
| To    | `string` | Recipient address |
| Value | `string` | Call value in wei |
| Data  | `string` | Call data         |

### Return Value

A `Result<string>` containing:
- On success: A call bundle identifier
- On failure: An error message

### Example

```csharp
var params = new WalletSendCallsParams
{
    Version = "1.0",
    ChainId = "0x1",
    From = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
    Calls = new[]
    {
        new Call
        {
            To = "0x000000000000000000000000000000000000dEaD",
            Value = "10000000000000",
            Data = "0x"
        }
    }
};

var result = await client.WalletSendCalls(params);

if (result.IsSuccess)
{
    string callBundleId = result.Value;
    // Use the call bundle ID
}
else
{
    Debug.LogError($"Failed to send calls: {result.Error}");
}
```

## WalletShowCallsStatus

Requests that a wallet shows information about a given call bundle.

### Method

```csharp
public async Task<Result> WalletShowCallsStatus(string callBundleId)
```

### Parameters

| Name         | Type     | Description                       |
| :----------- | :------- | :-------------------------------- |
| callBundleId | `string` | The identifier of the call bundle |

### Return Value

A `Result` containing:
- On success: No value, just success status
- On failure: An error message

### Example

```csharp
var result = await client.WalletShowCallsStatus("bundle-id-123");

if (result.IsSuccess)
{
    // Call status shown successfully
}
else
{
    Debug.LogError($"Failed to show call status: {result.Error}");
}
```

## WalletGrantPermissions

Request a Wallet to grant permissions in order to execute transactions on the user's behalf.

### Method

```csharp
public async Task<Result<WalletGrantPermissionsResult>> WalletGrantPermissions(WalletGrantPermissionsParams walletGrantPermissionsParams)
```

### Parameters

| Name                         | Type                           | Description                            |
| :--------------------------- | :----------------------------- | :------------------------------------- |
| walletGrantPermissionsParams | `WalletGrantPermissionsParams` | Parameters of the permissions to grant |

The `WalletGrantPermissionsParams` class contains:

| Name        | Type           | Description                     |
| :---------- | :------------- | :------------------------------ |
| ChainId     | `string`       | Chain ID                        |
| Address     | `string`       | User's address                  |
| Expiry      | `long`         | Expiration timestamp            |
| Signer      | `Signer`       | Signer information              |
| Permissions | `Permission[]` | Array of permissions to request |

The `Signer` class contains:

| Name | Type                         | Description          |
| :--- | :--------------------------- | :------------------- |
| Type | `string`                     | Type of signer       |
| Data | `Dictionary<string, object>` | Signer-specific data |

The `Permission` class contains:

| Name | Type                         | Description              |
| :--- | :--------------------------- | :----------------------- |
| Type | `string`                     | Type of permission       |
| Data | `Dictionary<string, object>` | Permission-specific data |

### Return Value

A `Result<WalletGrantPermissionsResult>` containing:
- On success: A result object with the granted permissions
- On failure: An error message

The `WalletGrantPermissionsResult` class extends `WalletGrantPermissionsParams` with:

| Name        | Type          | Description        |
| :---------- | :------------ | :----------------- |
| Context     | `string`      | Permission context |
| AccountMeta | `AccountMeta` | Account metadata   |
| SignerMeta  | `SignerMeta`  | Signer metadata    |

### Example

```csharp
var params = new WalletGrantPermissionsParams
{
    ChainId = "0x1",
    Address = "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
    Expiry = DateTimeOffset.Now.AddDays(7).ToUnixTimeSeconds(),
    Signer = new Signer
    {
        Type = "eoa",
        Data = new Dictionary<string, object>()
    },
    Permissions = new[]
    {
        new Permission
        {
            Type = "transact",
            Data = new Dictionary<string, object>
            {
                ["maxValuePerTx"] = "1000000000000000000"
            }
        }
    }
};

var result = await client.WalletGrantPermissions(params);

if (result.IsSuccess)
{
    var permissionsResult = result.Value;
    // Use the permissions result
}
else
{
    Debug.LogError($"Failed to grant permissions: {result.Error}");
}
```

# Utility Types

## AppMetadata

Defines metadata about the application.

### Properties

| Name         | Type       | Description                     |
| :----------- | :--------- | :------------------------------ |
| Name         | `string`   | Name of the application         |
| LogoUrl      | `string`   | URL to the application's logo   |
| ChainIds     | `string[]` | Chain IDs supported by the app  |
| CustomScheme | `string`   | Custom URL scheme for callbacks |

## Chain

Defines information about an Ethereum chain.

### Properties

| Name   | Type     | Description           |
| :----- | :------- | :-------------------- |
| Id     | `string` | Chain ID              |
| RpcUrl | `string` | RPC URL for the chain |

## Capability

Defines capabilities supported by the wallet.

### Properties

| Name             | Type               | Description                    |
| :--------------- | :----------------- | :----------------------------- |
| Permissions      | `Permissions`      | Permission capabilities        |
| PaymasterService | `PaymasterService` | Paymaster service capabilities |
| AtomicBatch      | `AtomicBatch`      | Atomic batch capabilities      |

## Result and Result&lt;T&gt;

Utility classes to handle operation results.

### Properties

| Name      | Type     | Description                                |
| :-------- | :------- | :----------------------------------------- |
| Error     | `string` | Error message, null if operation succeeded |
| IsSuccess | `bool`   | Indicates whether operation was successful |
| Value     | `T`      | Result value (only for `Result<T>`)        |

### Methods

| Name                  | Return Type | Description                            |
| :-------------------- | :---------- | :------------------------------------- |
| Success()             | `Result`    | Creates a successful result            |
| Success(T value)      | `Result<T>` | Creates a successful result with value |
| Failure(string error) | `Result`    | Creates a failed result                |
| Failure(string error) | `Result<T>` | Creates a failed result                |