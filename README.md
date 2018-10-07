# `secretstream-stream`

> Abstract encoding API of `libsodium` `crypto_secretstream`

Someday there will be a stream interface here too

## Usage

```js
var secretstream = require('secretstream-stream')
var sodium = require('sodium-native')

// Parameters
var header = Buffer.alloc(secretstream.HEADERBYTES)
var key = Buffer.alloc(secretstream.KEYBYTES)

// Initialise
sodium.randombytes_buf(key)

// Init encryption side, writing into header Buffer, which needs to be shared
// with decryption side
var tx = secretstream.encrypt(header, key)
var ciphertext = tx.encrypt(secretstream.TAG_MESSAGE, Buffer.from('Hello world!'))

// Setup the decrypt side
var rx = secretstream.decrypt(header, key)
var plaintext = rx.decrypt(ciphertext)

console.log(plaintext.equals(Buffer.from('Hello world!')), rx.decrypt.tag.equals(secretstream.TAG_MESSAGE))
```

## API

### Constants

#### Buffer sizes

- `secretstream.KEYBYTES` - Key size
- `secretstream.HEADERBYTES` - Header size
- `secretstream.ABYTES` - MAC size added to every message

#### Tags

- `secretstream.TAG_MESSAGE`
- `secretstream.TAG_PUSH`
- `secretstream.TAG_FINAL`
- `secretstream.TAG_REKEY`

### `var tx = secretstream.encrypt(header, key)`

Create an encrypt instance with `key`, writing into `header`. `header` needs to
be passed the the decryption side somehow.

### `var ciphertext = tx.encrypt(tag, plaintext, [ad], [ciphertext], [offset])`

Encrypt Buffer `plaintext` with added `tag` using optional Buffer `ad`, and
write into Buffer `ciphertext` at `offset`. `ad` can be `null` if unused, while
`ciphertext` will be allocated if not given. `offset` defalts to `0`.

### `var bytes = tx.encryptionLength(plaintext)`

Calculate the required length for a `ciphertext` from `plaintext` Buffer.

### `tx.encrypt.bytes`

Number of bytes written into `ciphertext` at last call to `tx.encrypt`

### `var rx = secretstream.decrypt(header, key)`

Create an decrypt instance with `key`, using `header` from `encrypt`.

### `var plaintext = rx.decrypt(ciphertext, [ad], [plaintext], [offset])`

Decrypt Buffer `ciphertext` using optional Buffer `ad`, and
write into Buffer `plaintext` at `offset`. `ad` can be `null` if unused, while
`plaintext` will be allocated if not given. `offset` defalts to `0`.

### `var bytes = tx.decryptionLength(ciphertext)`

Calculate the required length for a `plaintext` from `ciphertext` Buffer.

### `rx.decrypt.bytes`

Number of bytes written into `plaintext` at last call to `rx.decrypt`

### `rx.decrypt.tag`

A tag Buffer for the tag from the last decrypted `ciphertext`. Should be
compared against one of the exported tags. Please review the [libsodium
documentation](https://download.libsodium.org/doc/secret-key_cryptography/secretstream#usage)
for how tags should be interpreted.

## Install

```sh
npm install secretstream-stream
```

## License

[ISC](LICENSE)