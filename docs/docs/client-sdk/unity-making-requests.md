---
title: "Making requests"
slug: "unity-making-requests"
category: "633d1d37bc7103008654c123"
---

Requests to a MWP compatible Wallet can be made by calling the `EIP-1196` function provided by the `MWPClient` class.

```cs ClientController.cs
var result = await m_Client.PersonalSign(new PersonalSignParams()
{
    Challenge = "0x48656c6c6f2c20776f726c6421",
    Address = m_Account
});

if (result.IsSuccess)
{
    Debug.Log("Signature: " + result.Value);
}
else
{
    Debug.LogError("Error: " + result.Error);
}
```