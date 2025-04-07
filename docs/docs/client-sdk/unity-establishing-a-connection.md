---
title: "Establishing a connection"
slug: "unity-establishing-a-connection"
category: "633d1d37bc7103008654c123"
---

A connection to a Wallet through MWP can be initiated by calling the `EthRequestAccounts` function from `MWPClient`.

```cs ClientController.cs
using UnityEngine;
using MobileWalletProtocol;

public class ClientController : MonoBehaviour
{
    [SerializeField]
    MWPClientOptions m_Options = new MWPClientOptions()
    {
        Metadata = new AppMetadata()
        {
            Name = "Smart Wallet",
            CustomScheme = "exp://",
            ChainIds = new string[] { "0xaa36a7" }
        },
        Wallet = Wallet.CreateWebWallet(
            name: "Rapid fire wallet",
            // The scheme should be the target wallet's URL
            scheme: "https://id.sample.openfort.xyz#policy=pol_a909d815-9b6c-40b2-9f6c-e93505281137",
            iconUrl: "https://purple-magnificent-bat-958.mypinata.cloud/ipfs/QmfQrh2BiCzugFauYF9Weu9SFddsVh9qV82uw43cxH8UDV"
        )
    };

    MWPClient m_Client;
    string m_Account;

    void Awake()
    {
        m_Client = new MWPClient(m_Options);
    }

    public async void RequestAccounts()
    {
        var result = await m_Client.EthRequestAccounts();

        if (result.IsSuccess)
        {
            var accounts = result.Value;

            m_Account = accounts[0];

            foreach (var account in accounts)
            {
                Debug.Log("Account: " + account);
            }
        }
        else
        {
            Debug.LogError("Error: " + result.Error);
        }
    }

    public void Disconnect()
    {
        m_Client.Reset();
    }
}
```
An example handshake request is provided in the [sample application](https://github.com/openfort-xyz/mobile-wallet-protocol-unity-client/blob/aef744eaecc9c41c1dc0473c4a2ac2cff2981433/Project/Assets/ClientController.cs#L32).