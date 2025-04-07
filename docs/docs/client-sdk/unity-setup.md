---
title: "Setup"
slug: "unity-setup"
category: "633d1d37bc7103008654c123"
---

In order for your app to interact with a wallet that supports MWP, it needs to be configured with an App Link to your application. This callback URL will be used by the wallet application to navigate back to your application.

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
            scheme: "https://id.sample.openfort.xyz",
            iconUrl: "https://purple-magnificent-bat-958.mypinata.cloud/ipfs/QmfQrh2BiCzugFauYF9Weu9SFddsVh9qV82uw43cxH8UDV"
        )
    };

    MWPClient m_Client;
    string m_Account;

    void Awake()
    {
        m_Client = new MWPClient(m_Options);
    }
}
```

An example is provided in our [sample application](https://github.com/openfort-xyz/mobile-wallet-protocol-unity-client/tree/main/Project).