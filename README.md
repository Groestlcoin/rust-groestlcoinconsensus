# Groestlcoin's libgroestlcoinconsensus with Rust binding

This project builds libgroestlcoinconsensus library from Groestlcoin's C++ sources using cargo and offers Rust binding to its API.

Libgroestlcoinconsenus allows transaction verification using Groestlcoins unique script engine.
Groestlcoin enabled applications SHOULD use libgroestlcoinconsensus library to avoid accepting transactions that the Groestlcoin network nodes would not.

This project simplifies Rust developer's life by creating the libgroestlcoinconsensus library with cargo.
No need to deal with the archaic C++ toolchain directly.  
This also simplifies cross-compiling the consenus library e.g. for a mobile application.

Libgroestlcoinconsenus refers to code from another library [secp256k1](https://github.com/bitcoin-core/secp256k1).
A snapshot of that library is also included into Groestlcoin sources, therefore it could be backed into libgroestlcoinconsenus.
A typical Groestlcoin enabled application will however want to access further secp256k1 functions.
The project [rustc-secp256k1](https://github.com/rust-bitcoin/rust-secp256k1) offers a cargo build and Rust bindings,
therefore decided to depend on that instead of compiling the Groestlcoin embedded sources into libgroestlcoinconsensus.
This introduces a risk, since a difference between the two secp256k1 sources could break consensus with Groestlcoin.

## Build

This project has a submodule (the Groestlcoin Core sources), you have to clone it using:

`
git clone --recurse-submodules git@github.com:Groestlcoin/rust-groestlcoinconsensus.git
`

then build it simple with:

`
cargo build
`

I verified the build for Linux and OSX. Aleksey Sidorov contributed the windows build. PRs are welcome to extend support for other platforms.

## API
The API is very basic, exposing Groestlcoin's as is. This is intentional to keep this project minimal footprint and no further runtime dependencies. You will need another Rust library to serialize Groestlcoin transactions and scripts.

Verify a single spend (input) of a Groestlcoin transaction:

`
verify (spent_output_script: &[u8], amount: u64, spending_transaction: &[u8], input_index: usize) -> Result<(), Error>
`

### Arguments
 * spend_output_script: a Groestlcoin transaction output script to be spent
 * amount: The spent output amount in satoshis
 * spending_transaction: spending Groestlcoin transaction, serialized in Groestlcoin's on wire format
 * input_index: index of the input within spending_transaction

**Note** that spent amount will only be checked for Segwit transactions. Above example is not segwit therefore verify will succeed with any amount.
