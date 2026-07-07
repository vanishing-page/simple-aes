# simple AES
[![tests](https://img.shields.io/github/actions/workflow/status/vanishing-page/simple-aes/nodejs.yml?style=flat-square)](https://github.com/vanishing-page/simple-aes/actions/workflows/nodejs.yml)
[![module](https://img.shields.io/badge/module-ESM%2FCJS-blue?style=flat-square)](README.md)
[![types](https://img.shields.io/npm/types/@vanishing.page/simple-aes?style=flat-square)](README.md)
[![semantic versioning](https://img.shields.io/badge/semver-2.0.0-blue?logo=semver&style=flat-square)](https://semver.org/)
[![install size](https://flat.badgen.net/packagephobia/install/@vanishing.page/simple-aes?cache-control=no-cache)](https://packagephobia.com/result?p=@vanishing.page/simple-aes)
[![GZip size](https://flat.badgen.net/bundlephobia/minzip/@vanishing.page/simple-aes)](https://bundlephobia.com/package/@vanishing.page/simple-aes)
[![license](https://img.shields.io/badge/license-Big_Time-blue?style=flat-square)](LICENSE)


Cryptography used by [vanishing.page](https://vanishing.page/). Works in
browsers and node.

This is generally useful as a dead simple way of working with symmetric keys
in a browser or node.

Thanks to [Fission](https://github.com/fission-codes/), the original author
for much of this code.

<!-- toc -->

- [Install](#install)
- [Use](#use)
  * [Bundler](#bundler)
  * [pre-bundled](#pre-bundled)
- [API](#api)
  * [`@vanishing.page/simple-aes`](#vanishingpagesimple-aes)
  * [`@vanishing.page/simple-aes/compat`](#vanishingpagesimple-aescompat)
  * [`encryptMessage`](#encryptmessage)
  * [`decryptMessage`](#decryptmessage)

<!-- tocstop -->

## Install

```sh
npm i -S @vanishing.page/simple-aes
```

## Use

### Bundler
Just import

```js
import {
    decryptMessage,
    encryptMessage,
    type Message
} from '@vanishing.page/simple-aes'
// } from '@vanishing.page/simple-aes/compat'  // for older browsers
```

### pre-bundled
This exposes pre-bundled & minified JS files.

#### copy

```sh
cp ./node_modules/@vanishing.page/simple-aes/dist/compat.min.js ./public
cp ./node_modules/@vanishing.page/simple-aes/dist/index.min.js ./public/simple-aes.min.js
```

#### link

```html
<body>
    <!-- ... -->

    <script type="module" src="./compat.min.js"></script>
    <!-- or webcrypto version -->
    <script type="module" src="./simple-aes.min.js"></script>
</body>
```

## API

### `@vanishing.page/simple-aes`

Use the `webcrypto` API.

```js
import {
    decryptMessage,
    encryptMessage,
    type Message
} from '@vanishing.page/simple-aes'
```

### `@vanishing.page/simple-aes/compat`

Use a user-land module,
[@noble/ciphers](https://github.com/paulmillr/noble-ciphers). This will work
in browsers of all ages.

```js
import {
    encryptMessage,
    decryptMessage
} from '@vanishing.page/simple-aes/compat'
```

### `encryptMessage`
Generate a new AES key and encrypt the given message object.
Return an array of `[ encryptedMessage, { key }]`,
where `key` is a new AES key, encoded as `base64url`.

Can pass in a size for the key. By default it uses 256 bits.

```ts
async function encryptMessage (
    msg:{ content:string },
    opts:{ length:SymmKeyLength } = { length: DEFAULT_SYMM_LEN }
):Promise<[{ content:string }, { key:string }]>
```

#### encrypt example
```ts
import { SymmKeyLength, encryptMessage } from '@vanishing.page/simple-aes'

const [encryptedMsg, { key }] = await encryptMessage({
    content: 'hello world'
})

// or pass in a key size
await encryptMessage({
    content: 'hello'
}, {
    length: SymmKeyLength.B128
})

console.log(encryptedMessage)
// =>  { content: '5eAcA6+jnBfbuCx8L...' }
```

### `decryptMessage`
Decrypt the given message with the given key. Suitable for decrypting a message that was encrypted by this library. Key is an AES key, `base64url` encoded.

```ts
async function decryptMessage (
    msg:{ content:string },
    keyString:string  // <-- base64url
):Promise<{ content:string }>
```

#### decrypt example
```js
import { test } from '@vanishing.page/tapzero'
import { decryptMessage } from '@vanishing.page/simple-aes'

test('decrypt the message', async t => {
    const decrypted = await decryptMessage(message, key)
    t.equal(decrypted.content, 'hello world',
        'should decrypt to the right text')
})
```
