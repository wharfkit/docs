
# UserInterface


A prebuilt user interface is provided by the [WebRenderer](#). 

Applications can choose to build their own UserInterface for custom styling and functionality.

Each implementation extends the interface named UserInterface from the [SessionKit](#).

Each implementation may also extend the `AbstractUserInterface` to gain some base functionality. 

A UserInterface instance must be established by the developer in their app, passing any configuration options, which then must be passed into the [SessionKit](#) during construction.

## Login

The UserInterface is utilized first when the `login` method is called. 

When the app requests a user to login through the `kit.login()` call, the [SessionKit](#) will first trigger the `onLogin` method of the defined UserInterface. This notifies it to prepare any UI elements needed to perform the login.

The [SessionKit](#) will determine if:

- The UserInterface needs to prompt the user to select a [blockchain](#).
- The UserInterface needs to prompt the user to select a [permission](#).
- The UserInterface needs to prompt the user to manually enter a [permission](#).
- The UserInterface needs to prompt the user to select a [wallet](#).

The [SessionKit](#) will then call the [login](#) method defined on the UserInterface and pass in the [LoginContext](#).

LoginContext: https://github.com/wharfkit/session/blob/afe8281de2d4a52c8f7a3bb645c373ff9436f914/src/login.ts#L54-L101

It is now the responsibility of the UserInterface to interpret the context and prompt the user accordingly. The [SessionKit](#) will now await the response from the UserInterface, which must contain:

- If required, the [ChainID](#) of the blockchain the user is logging into.
- If required, the [PermissionLevel](#) of the account for the user to use.
- The index of the [WalletPlugin](#) the user has selected from the [walletPlugins](#) array on the LoginContext.

The UserInterface will know if the [ChainID](#) or [PermissionLevel](#) are required, based on a combination of the [uiRequirements](#) from the LoginContext and the [config](#) of the WalletPlugin that the user has selected.

Once this information is returned by the [login](#) method of the UserInterface back to the SessionKit, the SessionKit will interpret the response and begin the login process.

The SessionKit will process any [beforeLogin](#) hooks registered by any registered [LoginPlugins](#).

The SessionKit will then trigger the `login` method of the selected [WalletPlugin](#) and await its response.

Once the [WalletPlugin](#) has responded, any [afterLogin](#) hooks registered by the [LoginPlugins](#) will be processed.

A [Session](#) is then created and persisted through the [SessionKit](#) and finally the [onLoginComplete](#) method of the UserInterface is called to perform any cleanup required by the UI.

If at any point during this process an Error is thrown, a special method called [onError](#) will be called against the UserInterface. 

This is where the UserInterface has the opportunity to display a message to the user that an error has occurred. 





Links

Source Code: https://github.com/wharfkit/session/blob/dev/src/ui.ts
Base Implementation: https://github.com/wharfkit/web-renderer/blob/master/src/index.ts


https://wharfkit.github.io/session/interfaces/UserInterface.html
https://wharfkit.github.io/session/classes/AbstractUserInterface.html


UserInterface

/** Interact with the user to collect the data needed for a [[UserInterfaceLoginResponse]]. */
login(context: LoginContext): Promise<UserInterfaceLoginResponse>

/** Inform the UI that an error has occurred */
onError: (error: Error) => Promise<void>

/** Inform the UI that a login call has started **/
onLogin: () => Promise<void>

/** Inform the UI that a login call has completed **/
onLoginComplete: () => Promise<void>

/** Inform the UI that a transact call has started **/
onTransact: () => Promise<void>

/** Inform the UI that a transact call has completed **/
onTransactComplete: () => Promise<void>

/** Inform the UI that a transact call has started signing the transaction **/
onSign: () => Promise<void>

/** Inform the UI that a transact call has completed signing the transaction **/
onSignComplete: () => Promise<void>

/** Inform the UI that a transact call has started broadcasting the transaction **/
onBroadcast: () => Promise<void>

/** Inform the UI that a transact call has completed broadcasting the transaction **/
onBroadcastComplete: () => Promise<void>

/** Prompt the user with a custom UI element **/
prompt: (args: PromptArgs) => Cancelable<PromptResponse>

/** Update the displayed modal status from a TransactPlugin **/
status: (message: string) => void

/** Translate a string using the UI's language **/
translate: UserInterfaceTranslateFunction

/** Returns a translator for a specific namespace */
getTranslate: (namespace?: string) => UserInterfaceTranslateFunction

/** Programmatically add new localization strings to the  user interface */
addTranslations: (translations: LocaleDefinitions) => void




AbstractUserInterface


PromptArgs
PromptElement
UserInterfaceLoginResponse
UserInterfaceTranslateOptions
UserInterfaceTranslateFunction



export type LocaleDefinitions = Record<string, any>


Cancelable
https://github.com/wharfkit/session/blob/afe8281de2d4a52c8f7a3bb645c373ff9436f914/src/types.ts#L181-L222