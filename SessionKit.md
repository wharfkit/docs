---
title: SessionKit
description: The SessionKit is responsible for creating and managing end user Session instances for web applications through actions such as login and restore.
category: SessionKit
slug: index
---

# SessionKit

**Page will have documentation overview of this Kit and links to other documents.**

The `SessionKit` is responsible for creating and managing end user [Session](#) instances for web applications through actions such as [login](#) and [restore](#).

## Usage

When using the `SessionKit` within a web application the developer will first establish an instance of the class with the required [SessionKitArgs](#). A simple example for a web application to do this with the [WebRenderer](#) and [WalletPluginAnchor](#) would be as follows:

```ts
import { SessionKit } from "@wharfkit/session"
import { WebRenderer } from "@wharfkit/web-renderer"
import { WalletPluginAnchor } from "@wharfkit/wallet-plugin-anchor"

const sessionKit = new SessionKit({
  appName: "myapp",
  chains: [
    {
      id: "73e4385a2708e6d7048834fbc1079f2fabb17b3c125b146af438971e90716c4d",
      url: "http://jungle4.greymass.com",
    },
  ],
  ui: new WebRenderer(),
  walletPlugins: [new WalletPluginAnchor()],
})
```

The `SessionKit` arguments which are all required are (see [SessionKitArgs](#)):

- `appName`: The name of the application
- `chains`: An array of blockchains that the app supports
- `ui`: An instance of a [UserInterface](#), like the [WebRenderer](#)
- `walletPlugins`: An array containing at least one [WalletPlugin](#)

Additional optional parameters may also be passed as the 2nd argument during creation of the `SessionKit` (see [SessionKitOptions](#)):

- `allowModify`: A boolean flag indicating whether [plugins](#) are allowed to modify transaction data. This parameter is passed down to each [Session](#) this `SessionKit` generates.
- `expireSeconds`: The default number of seconds before a transaction expires. This parameter is passed down to each [Session](#) this `SessionKit` generates.
- `fetch`: Primarily for development purposes in nodejs v16 or lower, an instance of fetch which can be used by all components.
- `loginPlugins`: An array of [LoginPlugin](#) for use in all [Session](#) instances generated by this `SessionKit`.
- `storage`: An option to override the default instance of `SessionStorage`, which is used to persist data in the `SessionKit`, the [Sessions](#) it creates, and all [plugins](#).
- `transactPlugins`: An array of [TransactPlugin](#) for use in all [Session](#) instances generated by this `SessionKit`.
- `transactPluginsOptions`: A key/value pair object from which plugins can read global configuration options. This will only need to be used if the plugin instructions require it.

In the example above, the instance is being set as the `sessionKit` variable, which can then be used to perform the following actions:

- [login](#): Initiate the login process for a user.
- [logout](#): Perform a logout of a given session, or logout all existing sessions.
- [restore](#): Restore and return the most recently used session, or a specific session based on the args provided.
- [restoreAll](#): Restore all sessions and return them in an array.
- [getSessions](#): Return an array of [SerializedSessions](#)

## Reference

- [GetSessions](#)
- [Login](#)
- [LoginContext](#)
- [LoginPlugin](#)
- [Logout](#)
- [Plugins](#)
- [Restore](#)
- [UserInterface](#)
