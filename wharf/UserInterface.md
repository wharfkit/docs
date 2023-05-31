# UserInterface

The [SessionKit](#) exports an interface named `UserInterface` which defines how a graphical interface behaves during the [login](#) and [transact](#) method calls. 

## Usage

Typically the `UserInterface` is not something developers interact with directly, but are required to include in their projects and define during the instantiation of the [SessionKit](#). 

```ts
import { SessionKit } from '@wharfkit/session'
import { WebRenderer } from '@wharfkit/web-renderer'
import { WalletPluginAnchor } from '@wharfkit/wallet-plugin-anchor'

const sessionKit = new SessionKit({
    appName: 'myapp',
    chains: [{
        id: '73e4385a2708e6d7048834fbc1079f2fabb17b3c125b146af438971e90716c4d',
        url: 'http://jungle4.greymass.com'
    }],
    ui: new WebRenderer(), // <-- An instance of a `UserInterface`
    walletPlugins: [new WalletPluginAnchor()],
})
```

Once an instance is provided to the [SessionKit](#), it will programmatically make calls to the `UserInterface` where appropriate. Some examples of processes which will utilize the `UserInteface` include:

- During calls to the [login](#) method.
- While processing the included [LoginPlugin](#) hooks.
- During calls to the [transact](#) method.
- While processing the included [TransactPlugin](#) hooks.
- During interactions with the active [WalletPlugin](#).

## Responsibilities

The `UserInterface` is primarily responsible for two major aspects of the [SessionKit](#):

- Communication of information to the user.
- Prompting and handling user interactions.

### Communication

During typical [SessionKit](#) operations there is often benefit to providing the end user with status updates and information about their activity. The `UserInterface` defines multiple event-based methods in which external operations are able to call out to the user interface to provide relevant information to users.

### Interactions

The interaction between the end user and the [SessionKit](#) are facilitated by the `UserInterface` through a number of predefined events and method calls. During these interactions, the [SessionKit](#) will trigger and asyncronous operations against the `UserInterface` while it awaits the users response before proceeding.


## Implementation

The `UserInterface` components of the [SessionKit](#) are designed with layering in mind, optionally allowing developers to extends and inherit from in order to build custom instances.

```
DefinedUserInterface (Class Implementation)
↳ AbstractUserInterface (Abstract Class)
  ↳ UserInterface (Interface)
```

The [UserInterface](#) core component is defined as an interface with the data structures that the [SessionKit](#) expects to exist. One layer above this, an [AbstractUserInterface](#) is declared as an abstract class from which usable implementations can extend in order to gain base functionality. 

### Default Implementation

Wharf maintains and distributes a core package called the [WebRenderer](#) that acts as a default implementation of a `UserInterface`. It follows the recommendations to extend the `AbstractUserInterface` abstract class and also implement the `UserInterface` interface. 

More details about the [WebRenderer](#) can be found throughout the Wharf documentation or on [Github](https://github.com/wharfkit/web-renderer).

### Structure

To better understand the structure behind a `UserInterface` implementation from a technical perspective, the following categories of operations are part of the predefined interface.

#### Lifecycle Methods

Wharf currently has two major processes that each have a finite lifecycle: 

1. The [login](#) call on an instance of the [SessionKit](#) 
2. the [transact](#) call on each individual [Session](#) .

 When developers make calls to these methods, a lifecycle [context](#) object is created for the duration of the call. Through these processes the [context](#) also provides any [plugins](#) included the ability to interact with the user interface in a number of ways.

##### Login Lifecycle

Listed below is the sequence of events within the [login](#) method which will call the `UserInterface`.

###### `onLogin`

```ts
onLogin: () => Promise<void>
```

Immediately upon the developer's call to [login](#) against the [SessionKit](#), the `onLogin` call is made against the `UserInterface`. This call passes no data to the interface and expects no response, but gives the opportunity to the `UserInterface` to prepare itself for the incoming `login` call. 

This time can be used to prepare UI elements (in DOM or other mediums) before any actual user interaction.

###### `login`

```ts
login(context: LoginContext): Promise<UserInterfaceLoginResponse>
```

After initial processing has been completed by the [SessionKit](#), the `login` method of the given `UserInterface` is called. The [SessionKit](#) will pass in an instance of a [LoginContext](#) to the `UserInterface` to provide information that may be required to process the request. The [SessionKit](#) will then await a response from the `UserInterface` which must either return a value matching the [UserInterfaceLoginResponse](#) or throw an `Error`.

###### `onLoginComplete`
```ts
onLoginComplete: () => Promise<void>
```

After the [WalletPlugin](#) successfully completes it's login operations, the [SessionKit](#) will issue the `onLoginComplete` call against the `UserInterface`, which gives it the opportunity to reset itself and perform any clean up required.

##### Transact Lifecycle

Listed below is the sequence of events within the [transact](#) method which will call the `UserInterface`.

###### `onTransact`
```ts
onTransact: () => Promise<void>
```

Immediately upon the developer's call to [transact](#) against the [SessionKit](#), the `onTransact` call is made against the `UserInterface`. This call, similar to `onLogin`, passes no data and expects no specific response, but gives the implemented user interface time to prepare for the incoming request.

###### `onSign`
```ts
onSign: () => Promise<void>
```

After any included [TransactPlugin](#) hooks have had their opportunity to processes, the `onSign` call is made to the user interface to indicate that the tranasction is about to be signed by the [WalletPlugin](#). 

###### `onSignComplete`
```ts
onSignComplete: () => Promise<void>
```

Following a successful call to the [WalletPlugin](#) to sign the transaction, the `onSignComplete` call is made to indicate a signature has been retrieved.

###### `onBroadcast`
```ts
onBroadcast: () => Promise<void>
```

If the [transact](#) call has the default flag of `broadcast: true`, the `onBroadcast` call is made to the user interface as it prepares to broadcast the transaction to the network. 

**Note**: If the `broadcast: false` flag is set, this event will not be called.

###### `onBroadcastComplete`
```ts
onBroadcastComplete: () => Promise<void>
```

Following a successful call to the broadcast the transaction, the `onBroadcastComplete` call is made to indicate that the transaction has been successfully broadcast to the designed blockchain. 

**Note**: If the `broadcast: false` flag is set, this event will not be called.

###### `onTransactComplete`
```ts
onTransactComplete: () => Promise<void>
```

Upon success of the entire [transact](#) lifecycle flow, the `onTransactComplete` call is made to indicate to the `UserInterface` that this transaction has now completed. Any cleanup operations required by the user interface itself can now be called.

#### General Methods

Outside of the event-driven lifecycle methods above, a number of more generic methods are defined within a `UserInterface` instance for use in all of the other components that can interact with it.

These methods are made available in:

- The [SessionKit](#) during the [login](#) method call.
- A [Session](#) during the [transact](#) method call.
- A [WalletPlugin](#) during the [login](#) and [transact](#) calls.
- A [LoginPlugin](#) during the [login](#) call.
- A [TransactPlugin](#) during the [transact](#) call.

Wharf through both the [SessionKit](#) and [Session](#) classes will have direct access to these calls, while all of the various plugin types will have access to them through their specific [context](#) objects. The `UserInterface` instance is accessible to them through the `context.ui` field.

##### `prompt`
```
prompt: (args: PromptArgs) => Cancelable<PromptResponse>
```

The `prompt` call is available to any [plugin](#) that wishes to either display information or await some form of user interaction. 


The [plugin](#) that makes the call needs to provide arguments that match the `PromptArgs` interface and await a response, which will come in the form of a [CancelablePromise](https://github.com/wharfkit/session/blob/20d64d6410effda124265cd94fabf0da8a08e0c8/src/types.ts#L181-L222). This special type of promise allows the prompt to either be accepted, rejected, or canceled from within the plugin itself.

The arguments accepted by the prompt call must match:

```ts
interface PromptArgs {
    title: string
    body?: string
    elements: PromptElement[]
}
```

- The `title` is provided as a header for the display that will be rendered to the user.
- A `body` may optionally be defined with a text-based description of what you are prompting for.
- The `elements` array consists of one or more `PromptElement` instances, which instruct the `UserInterface` how to present this prompt to the user.


The `PromptElement` array is an object defined as follows:

```ts
interface PromptElement {
    type: 'accept' | 'asset' | 'button' | 'close' | 'countdown' | 'link' | 'qr' | 'textarea'
    label?: string
    data?: unknown
}
```

- The `type` field must match on of the strings included. Each string represents a different type of prompt 
- A `label` for the element, typically providing context to the element.
- A `data` object for the element, which is specific to the element itself and how its renderered.

The individual element types are further outlined in the [PromptElement](#) document.

Once the promise resolves, one of three things will happen:

- Upon `resolve`, the plugin can treat this as an "accept" or "OK" message and proceed.
- Upon `reject`, the plugin can treat this as an "decline" or "Cancel" message and proceed.
- Upon `cancel`, the plugin and current lifecycle method will be aborted.

##### `status`
```
status: (message: string) => void
```

The `status` method is a generic string-based system on the user interface which allows any process or plugin to provide basic messages directly to the user interface. These can be included in the UI to update the user about details of what process is occurring or for the developer to output as logs for debugging.

##### `onError`
```
onError: (error: Error) => Promise<void>
```

A `UserInterface` must define an `onError` method to define how the user interface handles errors thrown by the [SessionKit](#). These can be displayed directly to users, interpretted and handled based on their content, and logged externally to help troubleshoot issues.

##### `translate`
```
translate: UserInterfaceTranslateFunction
```

Part of the **Communication** responsibility of the `UserInterface` is also handling the translation of the content being passed to the user. This method must follow the [UserInterfaceTranslateFunction](#) interface and utilize whichever technologies are best suited to the task to facilitate translating content. 

The [WebRenderer](#) serves as [an example of how this can be done](https://github.com/wharfkit/web-renderer/blob/06cddd54ec78d8110747d4e5d67989a8cd1dce8f/src/index.ts#L244-L250), as it translates Wharf's expectation of a translation string into the i18n library its using.

##### `getTranslate`
```
getTranslate: (namespace?: string) => UserInterfaceTranslateFunction
```

Continuing with the **Communication** responsibility, this method defines how Wharf or a plugin should retrieve an instance of the `UserInterfaceTranslateFunction` by a given namespace. Plugins specifically will make use of this call to access and provide translations for their content within a given user interface. 

By default the [AbstractUserInterface](#) class will [define this method](https://github.com/wharfkit/session/blob/20d64d6410effda124265cd94fabf0da8a08e0c8/src/ui.ts#L118-L120) for use in plugins.

##### `addTranslations`
```
addTranslations: (translations: LocaleDefinitions) => void
```

The final responsibility of the `UserInterface` in regards to translations is the ability for Wharf or a plugin to be able to dynamically add translation strings into the user interface.

The [WebRenderer](#) again serves as [an example of how this can be done](https://github.com/wharfkit/web-renderer/blob/06cddd54ec78d8110747d4e5d67989a8cd1dce8f/src/index.ts#L252-L254). 

