# Smart Contracts Development in Ralph

Smart contract development in Ralph is designed with two key principles in mind: simplicity and security. Ralph is a straightforward yet powerful programming language that makes it easier for developers to write secure smart contracts while avoiding common pitfalls found in other blockchain platforms.

Ralph language's design philosophy emphasizes explicit asset management and clear control flow, helping developers reason about their code more effectively. By making asset flows and state changes more visible and predictable, Ralph reduces the likelihood of subtle bugs that could lead to security vulnerabilities. Ralph also makes common smart contract vulnerabilities like reentrancy attacks impossible by design.

The UTXO-based asset management model provides additional security benefits by default. It prevents unlimited authorization of assets by restricting access to only the UTXOs specified within a transaction. It also disables flashloan because assets cannot be borrowed and returned in the same transaction.

These features work together to create a development environment where security isn't an afterthought but is woven into the fabric of the language itself. As we explore Ralph's features and capabilities in more detail throughout this chapter, you'll see how this focus on simplicity and security translates into practical benefits for smart contract development.


## Alephium's Stateful UTXO Model

Before delving into the specifics of smart contract development in Ralph, it's important to understand Alephium's unique stateful UTXO (sUTXO) model, which forms the foundation for its smart contract capabilities. This section starts by comparing the classical UTXO model and account-based model, then examines how Alephium's sUTXO model combines their strengths to create a powerful new paradigm.

### UTXO vs Account models

The UTXO (Unspent Transaction Output) model and Account model represent two fundamentally different paradigms for managing blockchain state and transactions, each with distinct advantages and trade-offs.

The UTXO model, pioneered by Bitcoin, treats transactions like a chain of digital cash transfers. Each transaction consumes previous unspent outputs and creates new ones, similar to how physical cash is exchanged. When you spend money, you use up existing bills (inputs) and may receive change back (outputs). This model offers several compelling benefits. Since UTXOs can be processed inexplorer-backend-readonly-6d7b9bc6fd-gvvs4dependently, the system enables natural parallelization of transaction validation. The model also provides enhanced privacy as users can use different addresses for each transaction. Additionally, it allows for efficient batching of multiple payments into single transactions. Perhaps most importantly, the UTXO model has proven its security through Bitcoin's decade-plus history of securing billions in value.

However, the UTXO model faces important limitations when it comes to smart contract development. The lack of persistent state between transactions complicates the implementation of complex business logic. For instance, tracking aggregated data such as a counter across transactions is challenging, despite being a common scenario in morden smart contracts. Additionally, the UTXO scripting language's expressiveness is often intentionally limited to mitigate potential security issues, prioritizing security over flexibility.

The Account model, popularized by Ethereum, maintains a global state of accounts with balances and other data. Each account has an address and associated state that can be modified by transactions. This approach brings several benefits to smart contract development. It simplifies the implementation of complex smart contracts and provides straightforward state management. The programming approach it enables mirrors object-oriented programming, a widely adopted and well-understood paradigm with significant traction among developers.

The Account model, however, brings its own set of challenges. The shared global state makes parallel execution inherently difficult, the system is more susceptible to MEV (Miner Extractable Value) exploits and it often falls short in providing robust security measures at the smart contract level. A prime example is the need for token approvals, users must approve token contracts to act on their behalf before spending, which in practice often leads to unlimited authorization. If such a contract is malicious or compromised, it could drain a user's entire token balance unchecked. This approval model adds complexity and potential security risks that are not present in the UTXO model.

Each model has its trade-offs, which is why Alephium developed stateful UTXO (sUTXO) model which thoughtfully combines the advantages of both approaches while mitigating their respective drawbacks. This hybrid model represents a significant step forward in blockchain architecture, offering developers the best of both worlds.

### Stateful UTXO

In the sUTXO model, assets are managed using UTXOs, preserving the security and parallelization advantages inherent in the traditional UTXO model. While users can own an arbitrary number of UTXOs to manage their assets flexibly, every smart contract in Alephium is assigned exactly one UTXO to manage its assets. This design seamlessly unifies asset management for both users and contracts, simple and consistent.

Contract states, on the other hand, are managed using account-based model, where each contract maintains its own persistent state that can be modified across transactions. This enables developers to express sophisticated business logic more easily.

Transactions in the sUTXO model support both basic asset transfers and complex smart contract interactions. For basic transfers, they follow the classical UTXO pattern: consuming existing UTXOs as inputs and creating new ones as outputs. For example, when Alice sends 5 ALPHs to Bob, the transaction consumes Alice's UTXOs containing 5+ ALPHs in total and creates two new UTXOs: one with 5 ALPHs for Bob and another with the remaining balance (minus fees) returned to Alice.

The sUTXO model extends this by enabling transactions to also interact with smart contracts in powerful ways. A single transaction can transfer assets between users and contracts while executing contract logic and modifying contract states. For instance, when swapping tokens on a DEX, the transaction would:
1. Consume the user's UTXO containing the input tokens
2. Execute the DEX contract's swap logic
3. Update the contract's internal state (e.g. liquidity pools)
4. Create new UTXOs containing the output tokens for the user

Through this innovative model, Alephium achieves the best of both worlds: the scalability and security benefits inherent to UTXOs, combined with the flexibility and expressiveness typically associated with account-based smart contract platforms.

### Hybrid Programming Model

Alephium's sUTXO model necessitates a specialized programming language (Ralph) and virtual machine (Alphred). They work in concert to provide an expressive yet secure programming environment. The UTXO-based asset management treats tokens as first-class citizens, just like native ALPH tokens, and eliminates a class of common UX and security issues found in account-based models such as unlimited token approvals. Together with Ralph's carefully designed programming constructs, the Alphred VM prevents reentrancy attacks, unintended function calls, and accidental state updates. It also ensures that assets move strictly as specified by the contract code, making it extremely difficult for malicious actors to manipulate asset transfers in unintended ways. These built-in security features free developers to focus more on building their dApps.

Alephium also supports Bitcoin-like stateless programming where logic happens entirely within the scope of UTXOs. For example, it supports Pay-to-Script-Hash (P2SH) using the Ralph programming language, which is more expressive than Bitcoin script, to create sophisticated spending conditions. In fact, the support of Schnorr signatures in Alephium is implemented using this approach.

The separation of asset and contract states, combined with its support for both stateful and stateless programming, opens up new possibilities for dApps development. This hybrid approach draws interesting parallels to the evolution of programming languages. The UTXO model's immutability and absence of shared state aligns with functional programming (FP), which emphasizes on pure functions and immutable data, while the account-based model mirrors object-oriented programming (OOP), where objects manage mutable state that evolves over time. Notably, both classical UTXO model and pure functional languages such as Haskell are harder to program and thus never gained traction by the wider developer community. By contrast, despite of the frequent criticisms about Java's state management, it still dominates the industry because of its developer friendliness, much like Account-based model's widespread adoption in the blockchain space.

Alephium’s stateful UTXO (sUTXO) model mirrors the evolution of modern programming languages. Just as Scala and Rust harness the combined strength of functional and object-oriented paradigms, sUTXO model represents a similar evolutionary step in blockchain programming model that preserves the robust security and clarity of immutable UTXO model, while incorporating the expressiveness and flexibility of account-based model. This hybrid design empowers developers toexplorer-backend-readonly-6d7b9bc6fd-gvvs4 build sophisticated dApps with both the security benefits of UTXO and the flexibility of account-based models.

## Ralph Basics

Ralph is the smart contract programming language specifically designed for the Alephium blockchain, with a focus on security, simplicity, and efficiency. The design of Ralph follows the following principles:

- Keep the smart contract DSL as simple as possible
- Ensure there is one, and preferably only one, obvious way to accomplish tasks
- Incorporate good practices, especially security practices, as built-in features rather than optional patterns

This design philosophy is reflected in Ralph's key features. The language makes variables immutable by default to prevent reduce mutation-related bugs and unintended state changes. It implements automatic safety checks for public contract calls, protecting against unexpected external interactions that could compromise security. Asset movements between functions must be explicitly approved and verified, eliminating unauthorized transfers. Ralph's specialized data structures like sub-contracts and maps are optimized to minimize state bloat, improving the sustainability of the blockchain. These carefully designed features create a language that balances robust security with developer productivity, making Ralph both powerful and approachable for developers of all experience levels.

### Hello Web3

Let's kick things off with a simple example before we dive into all the nitty-gritty details of syntax and features. Here's a basic `HelloWeb3` contract that just prints a message, great for getting our feet wet with Ralph.

```rust
// This is a comment
// This is a simple Hello Web3 program

Contract HelloWeb3 {
    pub fn main() -> () {
        emit Debug(`Hello Web3!`)
    }
}
```

To test this contract, we'll start by creating a new project and adding the `HelloWeb3` contract to it. The [Alephium CLI](https://docs.alephium.org/sdk/cli) allows us to quickly scaffold a new project.

```bash
$ npx @alephium/cli init hello-web3
...
✅ Done.
✨ Project is initialized!
```

Next, we'll create a file named `hello-web3.ral` inside the `contracts` directory and add the `HelloWeb3` contract into it:

```bash
> cd hello-web3
> cat contracts/hello-web3.ral
Contract HelloWeb3() {
    pub fn main() -> () {
        emit Debug(`Hello Web3!`)
    }
}
```

To compile and test the contract, we also need to start the local Alephium devnet:

```bash
> git clone git@github.com:alephium/alephium-stack.git
> cd alephium-stack/devnet
> docker-compose up -d
[+] Running 5/5
 ✔ Container devnet-alephium-1           Healthy
 ✔ Container devnet-postgres-1           Healthy
 ✔ Container devnet-explorer-backend-1   Running
 ✔ Container devnet-pgadmin-1            Running
 ✔ Container devnet-explorer-frontend-1  Running
```

Once the local devnet is running, compile the `HelloWeb3` contract like this:

```bash
$ npm install
$ npx @alephium/cli compile
Loading alephium config file: .../hello-web3/alephium.config.ts
Full node version: v3.11.2
✅ Compilation completed!
Generating code for contract HelloWeb3
Generating code for contract TokenFaucet
Generating code for script Withdraw
✅ Codegen completed!
```

The console output shows that the `HelloWeb3` contract was successfully compiled, with its companion TypeScript class generated in the `artifacts/ts` directory. The `TokenFaucet` contract and `Withdraw` script are part of the default project template and will be covered later, so please ignore them for now.

To test the `HelloWeb3` contract, let's create a file called `src/hello-web3.ts` and add the following code to it:

```ts
import { web3 } from '@alephium/web3'
import { getSigner } from '@alephium/web3-test'
import { HelloWeb3 } from '../artifacts/ts'

async function run() {
  web3.setCurrentNodeProvider('http://127.0.0.1:22973')
  const signer = await getSigner()
  const helloWeb3 = await HelloWeb3.deploy(signer, { initialFields: {} })
  await helloWeb3.contractInstance.view.main()
}

run()
```

In the example above, we deployed the `HelloWeb3` contract and invoked its `main` function using the `view` method using its TypeScript companion class. The `view` method is a read-only operation, meaning that it does not modify the contract's state.

The line `web3.setCurrentNodeProvider('http://127.0.0.1:22973')` sets the current node provider to the local devnet. To deploy a contract, we also need a signer to cover the transaction fee. The Alephium Web3 SDK simplifies this by providing a convenient `getSigner()` function, which returns a fresh pre-funded devnet signer ready for use.

To execute the test, run:

```bash
$ npx ts-node src/hello-web3.ts
Testing HelloWeb3.main:
> Contract @ 2BucyFRRMPLEPyhnvZBNV5W1Luei5gkt6dGFQCCM9fhYj - Hello Web3!
```

`2BucyFRRMPLEPyhnvZBNV5W1Luei5gkt6dGFQCCM9fhYj` is the address of the deployed `HelloWeb3` contract. Your address will likely be different. The "Hello Web3!" message confirms that the contract's `main` function executed successfully.

### Types

Ralph is a statically typed language with type inference, eliminating the need for explicit type declarations for local variables and constants. All types in Ralph are value types, which are always copied when passed as function arguments or assigned to variables.

#### Primitive Types

Ralph supports the following primitive types: `Bool`, `I256`, `U256`, `ByteVec` and `Address`. Let's create some contracts to explore these types.

##### Bool

```rust
Contract Bool () {
    pub fn test() -> () {
      let a = true
      let b = false

      // Logical operators
      assert!(a && b == false, 0)
      assert!(a || b == true, 1)
      assert!(!a == false, 2)

      // Combining logical operators
      assert!((a || b) && !b == true, 3)
      assert!(!(a && b) || (a || b) == true, 4)

      // With comparisons
      assert!((5 > 3) && (2 < 4) == true, 5)
      assert!((10 >= 10) || (5 <= 3) == true, 6)

      emit Debug(`Test successful for Bool`)
    }
}
```

Ralph's boolean type works as you would expect from other programming languages. It supports the standard comparison operators (`==`, `!=`) for all primitive types and inequality operators (`<`, `>`, `<=`, `>=`) for numeric types. These can be combined with logical operators (`&&`, `||`, `!`) to create complex conditions.

In the contract, `assert!` is a built-in function that checks the condition, aborts the execution and returns a numerical error code if the condition is false.

To test the `Bool` contract, we'll create a test file named `bool.ts`. The test code follows the same pattern we used for the `HelloWeb3` contract, using the Web3 SDK:

```typescript
import { web3 } from '@alephium/web3'
import { getSigner } from '@alephium/web3-test'
import { Bool } from '../artifacts/ts'

async function test() {
  web3.setCurrentNodeProvider('http://127.0.0.1:22973')
  const signer = await getSigner()
  const bool = await Bool.deploy(signer, { initialFields: {} })
  await bool.contractInstance.view.test()
}

test()
```

To run the test:

```bash
$ npx ts-node src/bool.ts
Testing Bool.test:
> Contract @ vR49p2i2SrKkcyhwu6TQV7hg2ebSeDsToFLxfFMCvwR1 - Test successful for Bool
```

For the remaining types, we'll focus on the contract code and test outputs, skipping the TypeScript test code unless necessary.

##### I256 and U256

Ralph supports 256-bit signed and unsigned integers. These are represented by the `I256` and `U256` types respectively. The `U256` type can represent values from 0 to 2^256-1, while the `I256` type can represent values from -2^255 to 2^255-1. By default, integer literals without a suffix are interpreted as `U256`, while literals with a negative sign or an `i` suffix are interpreted as `I256`.

```rust
Contract Integer () {
    pub fn test() -> () {
      // The type of `a` ... `e` is U256.
      let a = 10
      let b = 10u
      let c = 1_000_000_000
      let d = 1e18
      let e = 0xff

      // The type of `f` ... `h` is I256.
      let f = -10
      let g = 10i
      let h = -1e18

      // Basic arithmetic
      assert!(a + b == 20, 0)
      assert!(f - g == -20, 1)
      assert!(c * 2 == 2000000000, 2)
      assert!(h / 1000i == -1e15, 3)
      assert!(2 ** 8 == 256, 4)
      assert!(10 % 3 == 1, 5)

      // More complex expressions
      assert!((a * b + c) / d == 0, 6)

      // Overflow examples:
      // let h = u256Max!() + 1
      // let i = 0 - 1
      // let j = i256Max!() + 1i
      // let k = i256Min!() - 1i

      // Modulo 2^256 operators for U256 type
      assert!(u256Max!() |+| 1 == 0, 7) // Addition modulo 2^256
      assert!(0 |-| 1 == u256Max!(), 8) // Subtraction modulo 2^256
      assert!(u256Max!() |*| 2 == u256Max!() - 1, 9) // Multiplication modulo 2^256
      assert!((1 << 128) |**| 2 == 0, 10) // Power modulo 2^256

      // Modulo N operators for U256 type
      assert!(mulModN!(2, 3, 4) == 2, 11) // (2 * 3) % 4
      assert!(mulModN!(1 << 128, 1 << 128, u256Max!() - 1) == 2, 12)
      assert!(mulModN!(u256Max!(), u256Max!(), u256Max!()) == 0, 13)
      assert!(addModN!(2, 3, 4) == 1, 14) // (2 + 3) % 4
      assert!(addModN!(1 << 128, 1 << 128, u256Max!()) == 1 << 129, 15)
      assert!(addModN!(u256Max!(), u256Max!(), u256Max!()) == 0, 16)

      // Bitwise Operators for U256 type
      assert!(e & 0xf0 == 0xf0, 17)
      assert!(e | 0xf0 == 0xff, 18)
      assert!(e ^ 0xf0 == 0x0f, 19)
      assert!(e << 8 == 0xff00, 20)
      assert!(u256Max!() << 2 == u256Max!() - 3, 21)
      assert!(e >> 4 == 0x0f, 22)

      emit Debug(`Test successful for Integer`)
    }
}
```

Ralph supports standard arithmetic operators that you would expect in most programming languages: `+` (addition), `-` (subtraction), `*` (multiplication), `/` (division), `**` (exponentiation), and `%` (modulo). These operators work with both `I256` and `U256` types. Ralph performs automatic overflow checking at runtime to prevent silent arithmetic errors that could lead to security vulnerabilities.

For the `U256` type, Ralph provides specialized arithmetic operators that perform calculations modulo `2^256`. These operators (`|+|`, `|-|`, `|*|`, and `|**|`) allow for efficient overflow-safe arithmetic operations without runtime checks, as they naturally wrap around when exceeding the maximum value, as shown in the `Integer` contract:

```rust
assert!(u256Max!() |+| 1 == 0, 7) // Addition modulo 2^256
assert!(0 |-| 1 == u256Max!(), 8) // Subtraction modulo 2^256
assert!(u256Max!() |*| 2 == u256Max!() - 1, 9) // Multiplication modulo 2^256
assert!((1 << 128) |**| 2 == 0, 10) // Power modulo 2^256
```

Ralph also provides specialized modulo functions for the `U256` type: `addModN!` and `mulModN!`, which compute addition and multiplication modulo a specified value:

```rust
assert!(mulModN!(2, 3, 4) == 2, 11) // (2 * 3) % 4
assert!(mulModN!(1 << 128, 1 << 128, u256Max!() - 1) == 2, 12)
assert!(mulModN!(u256Max!(), u256Max!(), u256Max!()) == 0, 13)

assert!(addModN!(2, 3, 4) == 1, 14) // (2 + 3) % 4
assert!(addModN!(1 << 128, 1 << 128, u256Max!()) == 1 << 129, 15)
assert!(addModN!(u256Max!(), u256Max!(), u256Max!()) == 0, 16)
```

Ralph also supports bitwise operators for the `U256` type, allowing for low-level bit manipulation operations that are essential for cryptographic algorithms and efficient data processing. Here are examples demonstrating these operators:

```rust
assert!(0xff & 0xf0 == 0xf0, 17)  // Bitwise AND
assert!(0xff | 0xf0 == 0xff, 18)  // Bitwise OR
assert!(0xff ^ 0xf0 == 0x0f, 19)  // Bitwise XOR
assert!(0xff << 8 == 0xff00, 20)  // Left shift
assert!(u256Max!() << 2 == u256Max!() - 3, 21)  // Left shift
assert!(0xff >> 4 == 0x0f, 22)    // Right shift
```

##### ByteVec

The `ByteVec` type represents a sequence of bytes (8-bit unsigned integers). It's a versatile data type used for handling binary data, encoding strings, or storing cryptographic values like hashes and signatures. ByteVec literals are denoted by starting with `#` followed by a hexadecimal string.

```rust
Contract ByteVec () {
    pub fn test() -> ()  {
      // ByteVec literals must start with `#` followed by a hex string.
      let a = #00112233
      let b = #  // Empty ByteVec

      // ByteVec concatenation
      assert!(#0011 ++ #2233 == a, 0)
      assert!(a ++ b == a, 1)

      // String literals starts with `b`.
      let c = b`Hello`
      let d = b`World`
      assert!(c ++ b` ` ++ d == b`Hello World`, 2)

      // Convert other types to ByteVec
      assert!(toByteVec!(true) == #01, 3)
      assert!(toByteVec!(false) == #00, 4)
      assert!(toByteVec!(100) == #4064, 5)
      assert!(toByteVec!(-100i) == #7f9c, 6)

      emit Debug(`Test successful for ByteVec`)
   }
}
```

Ralph doesn't have a native string type, but supports string literals that are encoded as `ByteVec` values:

```rust
// String literals starts with `b`.
let a = b`Hello`
let b = b`World`
let c = a ++ b` ` ++ b
```

ByteVec values can be concatenated using the `++` operator, and other types can be converted to ByteVec using the `toByteVec!` built-in function.

##### Address

An address on Alephium is a unique identifier that represents an account or a contract. All networks (i.e. mainnet, testnet, devnet) share the same address format.

There are currently 4 different address types on Alephium, each represented by a unique byte prefix:

- `0x00` - Pay to public key hash (`P2PKH`)
- `0x01` - Pay to multiple public key Hash (`P2MPKH`)
- `0x02` - Pay to script hash (`P2SH`)
- `0x03` - Pay to contract (`P2C`)

Each address type is followed by a specific content bytes format:

- `P2PKH` - serialized public key hash
- `P2MPKH` - serialized public key hashes and multisig threshold
- `P2SH` - serialized script hash
- `P2C` - serialized contract id

The complete address in bytes is constructed by concatenating the address type and the content bytes:

```
address = address type || content bytes
```

The string representation of an address is the base58 encoding of its byte representation. Each address on Alephium belongs to a group, which can be derived deterministically from the address. In Ralph, address literals must start with `@` followed by a valid base58-encoded Alephium address.

```rust
Contract AddressTest () {
    pub fn test() -> ()  {
      // Address literals must start with `@`
      let p2pkh = @1DrDyTr9RpRsQnDnXo2YRiPzPW4ooHX5LLoqXrqfMrpQH
      let p2mpkh = @2jW1n2icPtc55Cdm8TF9FjGH681cWthsaZW3gaUFekFZepJoeyY3ZbY7y5SCtAjyCjLL24c4L2Vnfv3KDdAypCddfAY
      let p2sh = @ibsc1yJLJxxVcsPfSDJoR3mzrasrZq2Rn63dFQGcDAYE
      let p2c = @26j4viXkBzJd5SaDtQzyGM6joqoECmajncT4QS3tmT9hb

      assert!(prefix(p2pkh) == #00, 0)
      assert!(prefix(p2mpkh) == #01, 1)
      assert!(prefix(p2sh) == #02, 2)
      assert!(prefix(p2c) == #03, 3)

      assert!(groupOfAddress!(p2pkh) == 0, 4)
      assert!(groupOfAddress!(p2mpkh) == 0, 5)
      assert!(groupOfAddress!(p2sh) == 0, 6)
      assert!(groupOfAddress!(p2c) == 2, 7)

      emit Debug(`Test successful for Address`)
   }

    fn prefix(address: Address) -> ByteVec {
       return byteVecSlice!(toByteVec!(address), 0, 1)
    }
}
```

The `prefix` function is a helper function that extracts the address type from an address. The `groupOfAddress!` function is a built-in function that returns the group of an address.

With the Danube upgrade, Alephium will introduce groupless addresses known as `P2PK`. This enhancement enables wallets and dApps to abstract away the complexity of the group concept, offering a smoother and more user-friendly experience.

#### Composite Types

Ralph supports five composite types: `Tuple`, `Struct`, `Array`, `Enum`, and `Map`.

##### Tuple

Ralph supports `tuple`, a product type containing an ordered, fixed-size collection of values of potentially different types. Elements are accessed by their position in the tuple.

```rust
Contract TupleExample() {
    pub fn test() -> () {
        // A tuple with different types
        let (mut first, second, third) = createTuple()
        first = first + 1

        assert!(first == 101, 0)
        assert!(second == b`tuple example`, 1)
        assert!(third == false, 2)
        emit Debug(`Tuple test successful`)
    }

    fn createTuple() -> (U256, ByteVec, Bool, Address) {
        return 100, b`tuple example`, false
    }
}
```

In the example above, `createTuple()` returns a tuple with three elements, which are destructured into `first`, `second`, and `third` in the `test()` function. The `first` element is explicitly declared as mutable with `mut` keyword, allowing us to modify its value. Other elements are immutable by default.

##### Struct

Ralph supports `struct`, a product type defined globally with fields that can be mutable or immutable. Unlike tuples where elements are accessed by position, struct elements are referred to by their field names using dot notation (e.g. `myStruct.fieldName`). To modify a nested field (e.g. `foo.x.y.z = 123`), all selectors in the chain (`foo`, `x`, `y`, and `z`) must be declared as mutable.

```rust
// Structs have to be defined globally
struct Foo { x: U256, mut y: U256 }
struct Bar { z: U256, mut foo: Foo }

Contract Struct() {
   pub fn test() -> () {
       let f0 = Foo { x: 1, y: 2 }
       // f0.y = 3 won't work as f0 is immutable despite the field y being mutable

       let mut f1 = Foo { x: 1, y: 2 }
       f1.y = 3 // This works as both f1 and y are mutable
       assert!(f1.y == 3, 0)

       // let b0 = Bar { z: 4, foo: f0 }
       // b0.foo.y = 5 won't work as b0 is immutable

       let mut b1 = Bar { z: 5, foo: f0 }
       b1.foo.y = 6
       assert!(b1.foo.y == 6, 1)

       emit Debug(`Test successful for Struct`)
  }
}
```

Structs are value types that are copied when assigned to new variables, rather than being passed by reference:

```rust
struct Foo { x: U256, mut y: U256 }

Contract Struct() {
    pub fn test() -> () {
        let foo0 = Foo { x: 0, y: 1 }
        let mut foo1 = foo0

        // Modifying foo1.y will not change foo0.y
        foo1.y = 2
        assert!(foo0.y == 1, 0)
        assert!(foo1.y == 2, 1)

        emit Debug(`Test successful for Struct`)
    }
}
```

Ralph also supports struct destructuring with syntax similar to TypeScript:

```rust
struct Foo { x: U256, y: U256 }

Contract Struct() {
  pub fn test() -> () {
    let foo = Foo { x: 0, y: 1 }

    let Foo { mut x, y } = foo
    assert!(x == 0, 0)
    assert!(y == 1, 1)
    x = 1

    // Variables `x` and `y` already exist, create two new variables `x1` and `y1`
    let Foo { x: x1, y: y1 } = foo
    assert!(x1 == 0, 2)
    assert!(y1 == 1, 3)

    emit Debug(`Test successful for Struct`)
  }
}
```

##### Fixed Size Array

Fixed size arrays are product types, similar to tuples and structs, but with a key difference: they contain elements of the same type, and the size is part of the type signature. This means the array's length must be known at compile time and cannot be changed during execution.

```rust
struct Foo { x: U256, y: U256 }

Contract Array() {
    pub fn test() -> () {

        let a0 = [0, 1, 2, 3]  // [U256; 4]
        assert!(a0[2] == 2, 0)

        let a1 = [[0, 1], [2, 3]] // [[U256, 2]; 2]
        assert!(a1[0][1] == 1, 1)

        let a2 = [0i; 3]  // [I256; 3]
        assert!(a2[2] == 0i, 2)

        let a3 = [#00, #11, #22, #33]  // [ByteVec; 4]
        assert!(a3[2] == #22, 3)

        let a4 = [Foo{ x: 1, y: 2 }, Foo{ x: 3, y: 4 }] // [Foo; 2]
        assert!(a4[0].x == 1, 4)
        assert!(a4[1].y == 4, 5)

        emit Debug(`Test successful for Array`)
  }
}
```

Fixed size arrays can be initialized in two ways: by providing a list of elements within square brackets, like `[0, 1, 2, 3]` as shown in `a0`, or by specifying a single element followed by a semicolon and the size of the array, like `[0i; 3]` as shown in `a2`. This second syntax creates an array with all elements initialized to the same value.

The elements of a fixed size array can be either primitive types (e.g. `U256`, `I256`, `ByteVec`) or composite types such as `Tuple`, `Struct`. It can also be other fixed sized arrays which would make it multi-dimensional arrays.

##### Map

In Ralph, `Map` is a key-value data structure defined as global contract attributes, eliminating the need for explicit initialization. Maps are declared using the syntax `mapping[KeyType, ValueType] mapName` where:

- `KeyType` can be any primitive type (`Bool`, `U256`, `I256`, `Address`, `ByteVec`)
- `ValueType` can be any valid Ralph type except another `Map`

Maps provide efficient storage and retrieval of values based on unique keys, making them ideal for maintaining state in smart contracts.

Under the hood, each Map entry is constructed as a subcontract of the current contract. Unlike other blockchain implementations where maps can cause state bloat with unlimited entries, Alephium's approach prevents this by requiring a small deposit for each map entry. While the deposit amount could potentially be updated in the future upgrades, using the `mapEntryDeposit!()` built-in function in Ralph ensures your code remains future-proof by always returning the correct required amount. The deposit is automatically returned to a specified recipient when the map entry is removed.

Maps provide three essential built-in methods: `insert!` for creating entries, `remove!` for deleting entries, and `contains!` for checking existence. Values can be accessed and updated using bracket syntax `map[key] = newValue`. The example below demonstrates these operations.

```rust
Contract Counters() {
  // All maps must be defined at the contract level
  mapping[Address, U256] counters

  @using(preapprovedAssets = true, checkExternalCaller = false)
  pub fn create() -> () {
    let key = callerAddress!()
    let depositor = key
    // The depositor will deposit a minimal ALPH deposit for the new map entry
    counters.insert!(depositor, key, 1)
  }

  @using(checkExternalCaller = false)
  pub fn increase() -> () {
    let key = callerAddress!()
    let value = counters[key]
    // Update the map entry value
    counters[key] = value + 1
  }

  @using(checkExternalCaller = false)
  pub fn currentCount(key: Address) -> U256 {
    if (counters.contains!(key)) {
      return counters[key]
    } else {
      return 0
    }
  }

  @using(checkExternalCaller = false)
  pub fn clear() -> U256 {
    let key = callerAddress!()
    let depositRecipient = key
    let value = counters[key]
    // Each map entry removal redeems the map entry deposit
    counters.remove!(depositRecipient, key)
    return value
  }
}
```


The `Counters` contract demonstrates maps in Ralph by implementing a simple personal counter. Users can create a counter tied to their address (with a map entry deposit), increment it, check its value, and eventually clear it (recovering their deposit).

Function annotations play a crucial role in Ralph smart contracts, and we'll explore them in more details in the next section. For now, let's briefly examine the annotations used in the `Counters` contract to understand their purpose:

- `@using(preapprovedAssets = true)`: This annotation requires the caller to approve assets before calling the function. In this case, the caller needs to approve enough ALPH to cover the map entry deposit.
- `@using(checkExternalCaller = false)`: By default, Ralph requires public functions to check the caller, otherwise it won't compile. This annotation allows any caller to call the function.

Let's test the `Counters` contract using the TypeScript code below:

```typescript
import { MAP_ENTRY_DEPOSIT, web3 } from '@alephium/web3'
import { getSigner } from '@alephium/web3-test'
import { Counters } from '../artifacts/ts'

async function test() {
  web3.setCurrentNodeProvider('http://127.0.0.1:22973', undefined, fetch)

  const signer = await getSigner()
  const { contractInstance: counters } = await Counters.deploy(
    signer, { initialFields: {} }
  )

  async function getCurrentCount() {
    const { returns } = await counters.view.currentCount(
      { args: { key: signer.address } }
    )
    return returns
  }

  console.assert(await getCurrentCount() === 0n);
  await counters.transact.create({ signer, attoAlphAmount: MAP_ENTRY_DEPOSIT })
  console.assert(await getCurrentCount() === 1n)
  await counters.transact.increase({ signer })
  console.assert(await getCurrentCount() === 2n)
  await counters.transact.clear({ signer })
  console.assert(await getCurrentCount() === 0n);
}

test()
```

`getCurrentCount` is a helper function that calls the `currentCount` view function of the `Counters` contract to get the current count of the counter for the signer's address. After deploying the `Counters` contract, the current count for signer address is 0.

After calling the `create` function, the counter for the signer's address is initialized to 1. We use the `transact` method since this function modifies blockchain state, and we provide the required `MAP_ENTRY_DEPOSIT` amount of ALPH to cover the map entry cost.

Next, we call the `increase` function to increment the counter to 2. Finally, we call the `clear` function which resets the counter to 0 and returns the map entry deposit to the signer's address.

##### Enum

Ralph supports `enum` types, which let you define a type with several possible variants. Enums can be defined either globally (outside any contract) for use across multiple contracts, or locally within a specific contract. They're especially useful for representing a fixed set of related values, such as error codes or status indicators.

```rust
enum GlobalErrorCode {
  INVALID_CALLER_CONTRACT = 0
}

Contract Foo(owner: Address, parentId: ByteVec) {
  enum LocalErrorCode {
    INVALID_OWNER = 1
  }

  pub fn foo(caller: Address) -> () {
    assert!(callerContractId!() == parentId, GlobalErrorCode.INVALID_CALLER_CONTRACT)
    assert!(caller == owner, LocalErrorCode.INVALID_OWNER)
  }
}
```

The value for enum fields can only be constant value of primitive types such as `U256`, `I256`, `Bool`, `Address` and `ByteVec`.

