<a href="https://margelo.io">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="./img/banner-dark.png" />
    <source media="(prefers-color-scheme: light)" srcset="./img/banner-light.png" />
    <img alt="VisionCamera" src="./img/banner-light.png" />
  </picture>
</a>

<div align="center">
  <h1 align="center">MMKV</h1>
  <h3 align="center">The fastest key/value storage for React Native.</h3>
</div>

<div align="center">
  <a align="center" href="https://github.com/mrousavy?tab=followers">
    <img src="https://img.shields.io/github/followers/mrousavy?label=Follow%20%40mrousavy&style=social" />
  </a>
  <br/>
  <a align="center" href="https://twitter.com/mrousavy">
    <img src="https://img.shields.io/twitter/follow/mrousavy?label=Follow%20%40mrousavy&style=social" />
  </a>
  <br />
  <a href="https://github.com/sponsors/mrousavy">
    <img align="right" width="160" alt="This library helped you? Consider sponsoring!" src=".github/funding-octocat.svg">
  </a>
</div>
<br/>


* **MMKV** is an efficient, small mobile key-value storage framework developed by WeChat. See [Tencent/MMKV](https://github.com/Tencent/MMKV) for more information
* **react-native-mmkv** is a library that allows you to easily use **MMKV** inside your React Native applications. It provides fast and direct bindings to the native C++ library which are accessible through a simple JS API.

## Features

* **Get** and **set** strings, booleans and numbers
* **Fully synchronous** calls, no async/await, no Promises, no Bridge.
* **Encryption** support (secure storage)
* **Multiple instances** support (separate user-data with global data)
* **Customize storage location**
* **High performance** because everything is **written in C++**
* **~30x faster than AsyncStorage**
* Uses [**JSI**](https://github.com/react-native-community/discussions-and-proposals/issues/91) instead of the "old" Bridge
* **iOS**, **Android** and **Web** support
* Easy to use **React Hooks** API

## Sponsors

<div align="right">
  <a href="https://getstream.io/chat/react-native-chat/tutorial/?utm_source=Github&utm_medium=Github_Repo_Content_Ad&utm_content=Developer&utm_campaign=Github_Jan2022_ReactNative&utm_term=react-native-mmkv">
    <img align="right" src="https://theme.zdassets.com/theme_assets/9442057/efc3820e436f9150bc8cf34267fff4df052a1f9c.png" height="40" />
  </a>
</div>

react-native-mmkv is sponsored by **getstream.io**. <br/>
[Try the React Native Chat tutorial 💬](https://getstream.io/chat/react-native-chat/tutorial/?utm_source=Github&utm_medium=Github_Repo_Content_Ad&utm_content=Developer&utm_campaign=Github_Jan2022_ReactNative&utm_term=react-native-mmkv)

## Benchmark

[StorageBenchmark](https://github.com/mrousavy/StorageBenchmark) compares popular storage libraries against each other by reading a value from storage for 1000 times:

<div align="center">
  <a href="https://github.com/mrousavy/StorageBenchmark">
    <img src="./img/benchmark_1000_get.png" />
  </a>
  <p>
    <b>MMKV vs other storage libraries</b>: Reading a value from Storage 1000 times. <br/>
    Measured in milliseconds on an iPhone 11 Pro, lower is better. <br/>
  </p>
</div>

## Installation

### React Native

```sh
yarn add react-native-mmkv
cd ios && pod install
```

### Expo

```sh
npx expo install react-native-mmkv
npx expo prebuild
```

## Usage

### Create a new instance

To create a new instance of the MMKV storage, use the `MMKV` constructor. It is recommended that you re-use this instance throughout your entire app instead of creating a new instance each time, so `export` the `storage` object.

#### Default

```js
import { MMKV } from 'react-native-mmkv'

export const storage = new MMKV()
```

This creates a new storage instance using the default MMKV storage ID (`mmkv.default`).

#### App Groups

If you want to share MMKV data between your app and other apps or app extensions in the same group, open `Info.plist` and create an `AppGroup` key with your app group's value. MMKV will then automatically store data inside the app group which can be read and written to from other apps or app extensions in the same group by making use of MMKV's multi processing mode.
See [Configuring App Groups](https://developer.apple.com/documentation/xcode/configuring-app-groups).

#### Customize

```js
import { MMKV, MMKVMode } from 'react-native-mmkv'

export const storage = new MMKV({
  id: `user-${userId}-storage`,
  path: `${USER_DIRECTORY}/storage`,
  encryptionKey: 'hunter2',
  mode: 'single-process'
})
```

This creates a new storage instance using a custom MMKV storage ID. By using a custom storage ID, your storage is separated from the default MMKV storage of your app.

The following values can be configured:

* `id`: The MMKV instance's ID. If you want to use multiple instances, use different IDs. For example, you can separate the global app's storage and a logged-in user's storage. (required if `path` or `encryptionKey` fields are specified, otherwise defaults to: `'mmkv.default'`)
* `path`: The MMKV instance's root path. By default, MMKV stores file inside `$(Documents)/mmkv/`. You can customize MMKV's root directory on MMKV initialization (documentation: [iOS](https://github.com/Tencent/MMKV/wiki/iOS_advance#customize-location) / [Android](https://github.com/Tencent/MMKV/wiki/android_advance#customize-location))
* `encryptionKey`: The MMKV instance's encryption/decryption key. By default, MMKV stores all key-values in plain text on file, relying on iOS's/Android's sandbox to make sure the file is encrypted. Should you worry about information leaking, you can choose to encrypt MMKV. (documentation: [iOS](https://github.com/Tencent/MMKV/wiki/iOS_advance#encryption) / [Android](https://github.com/Tencent/MMKV/wiki/android_advance#encryption))
* `mode`:  *Android Only*: The MMKV mode. It is set to `single-process` by default. You can set its value to `multi-process` to support simultaneous read-write access between processus at the cost of performance. This is useful when you want to share data between your react-native app and native extensions such as widgets. _Notice_: On iOS, this will automatically be set to `multi-process` if you set an AppGroup in the plist configuration (more information on AppGroups [here](https://github.com/mrousavy/react-native-mmkv/tree/master#app-groups))


### Set

```js
storage.set('user.name', 'Marc')
storage.set('user.age', 21)
storage.set('is-mmkv-fast-asf', true)
```

### Get

```js
const username = storage.getString('user.name') // 'Marc'
const age = storage.getNumber('user.age') // 21
const isMmkvFastAsf = storage.getBoolean('is-mmkv-fast-asf') // true
```

### Keys

```js
// checking if a specific key exists
const hasUsername = storage.contains('user.name')

// getting all keys
const keys = storage.getAllKeys() // ['user.name', 'user.age', 'is-mmkv-fast-asf']

// delete a specific key + value
storage.delete('user.name')

// delete all keys
storage.clearAll()
```

### Objects

```js
const user = {
  username: 'Marc',
  age: 21
}

// Serialize the object into a JSON string
storage.set('user', JSON.stringify(user))

// Deserialize the JSON string into an object
const jsonUser = storage.getString('user') // { 'username': 'Marc', 'age': 21 }
const userObject = JSON.parse(jsonUser)
```

### Encryption

```js
// encrypt all data with a private key
storage.recrypt('hunter2')

// remove encryption
storage.recrypt(undefined)
```

### Buffers

```js
storage.set('someToken', new Uint8Array([1, 100, 255]))
const buffer = storage.getBuffer('someToken')
console.log(buffer) // [1, 100, 255]
```

## Testing with Jest

A mocked MMKV instance is automatically used when testing with Jest, so you will be able to use `new MMKV()` as per normal in your tests. Refer to [example/test/MMKV.test.ts](example/test/MMKV.test.ts) for an example.

## Documentation

* [Hooks](./docs/HOOKS.md)
* [Value-change Listeners](./docs/LISTENERS.md)
* [Migrate from AsyncStorage](./docs/MIGRATE_FROM_ASYNC_STORAGE.md)
* [Using MMKV with redux-persist](./docs/WRAPPER_REDUX.md)
* [Using MMKV with recoil](./docs/WRAPPER_RECOIL.md)
* [Using MMKV with mobx-persist-storage](./docs/WRAPPER_MOBX.md)
* [Using MMKV with mobx-persist](./docs/WRAPPER_MOBXPERSIST.md)
* [Using MMKV with zustand persist-middleware](./docs/WRAPPER_ZUSTAND_PERSIST_MIDDLEWARE.md)
* [Using MMKV with jotai](./docs/WRAPPER_JOTAI.md)
* [Using MMKV with react-query](./docs/WRAPPER_REACT_QUERY.md)
* [How is this library different from **react-native-mmkv-storage**?](https://github.com/mrousavy/react-native-mmkv/issues/100#issuecomment-886477361)

## LocalStorage and In-Memory Storage (Web)

If a user chooses to disable LocalStorage in their browser, the library will automatically provide a limited in-memory storage as an alternative. However, this in-memory storage won't persist data, and users may experience data loss if they refresh the page or close their browser. To optimize user experience, consider implementing a suitable solution within your app to address this scenario.

## Limitations

As the library uses JSI for synchronous native methods access, remote debugging (e.g. with Chrome) is no longer possible. Instead, you should use [Flipper](https://fbflipper.com).

### Flipper

Use [flipper-plugin-react-native-mmkv](https://github.com/muchobien/flipper-plugin-react-native-mmkv) to debug your MMKV storage using Flipper. You can also simply `console.log` an MMKV instance.

### Reactotron

Use [reactotron-react-native-mmkv](https://www.npmjs.com/package/reactotron-react-native-mmkv) to automatically log writes to your MMKV storage using Reactotron. [See the docs for how to setup this plugin with Reactotron.](https://www.npmjs.com/package/reactotron-react-native-mmkv)

## Community Discord

[**Join the Margelo Community Discord**](https://discord.gg/6CSHz2qAvA) to chat about react-native-mmkv or other Margelo libraries.

## Adopting at scale

react-native-mmkv is provided _as is_, I work on it in my free time.

If you're integrating react-native-mmkv in a production app, consider [funding this project](https://github.com/sponsors/mrousavy) and <a href="mailto:me@mrousavy.com?subject=Adopting react-native-mmkv at scale">contact me</a> to receive premium enterprise support, help with issues, prioritize bugfixes, request features, help at integrating react-native-mmkv, and more.

## Contributing

See the [contributing guide](CONTRIBUTING.md) to learn how to contribute to the repository and the development workflow.

## License

MIT
