# Asset

The `Asset` class represents a token balance on an Antelope chain and is the combination of two `UInt64` values, one for the balance and one for the `Symbol`.

`10.0000 FOO` is a string representation of an `Asset`.

## Value

Each `Asset` has a `.value` property which can be accessed to utilize the value of the asset as a native javascript `Number`.

```ts
const balance = Asset.from("10.0000 FOO")

console.log(balance.value) // Number(10)

const balance = Asset.from("0.0001 FOO")

console.log(balance.value) // Number(0.0001)
```

Due to the oddities surrounding floating point math in Javascript, if you need to perform mathematical operations on these values, it is recommended you use the `.units` value instead.

## Units

The units portion of an `Asset` contains the numeric amount of the asset represented as an `Int64` value. It can be accessed through the `.units` property on the `Asset`.

```ts
const balance = Asset.from("10.0000 FOO")

console.log(balance.units) // Int64.from(100000)
console.log(Number(balance.units)) // Number(100000)
```

With the `units` value of an asset being an `Int64`, it can make use of all the built-in integer math operations.

TODO: Add link to Integer Math section.

## Symbol

The `Symbol` portion of an `Asset` represents its name and decimal precision. The `.symbol` value exists on the `Asset` class, providing the string representation of the symbol and the precision.

```ts
const balance = Asset.from("10.00000000 FOO")
console.log(balance.symbol.name) // String('FOO')
console.log(balance.symbol.precision) // Number('8')
```

## Creating

### From string

```ts
const balance = Asset.from("10.0000 FOO")
// 10.0000 FOO
```

### From number

```ts
const balance = Asset.from(1, "4,FOO")
// 1.0000 FOO

const balance = Asset.from(0.05, "8,BAR")
// 0.05000000 BAR

const balance = Asset.from(0.05, Asset.Symbol.from("4,FOO"))
// 0.0500 FOO

const balance = Asset.from(0.05, Asset.Symbol.fromParts("FOO", 4))
// 0.0500 FOO
```

### From units

```ts
const balance = Asset.fromUnits(100, "4,EOS")
// 0.0100 EOS

const balance = Asset.fromUnits(100, "8,WAX")
// 0.00000100 WAX
```

## Usage

### Displaying Assets

When displaying an `Asset` to a user as a string or to place it into an unserialized smart contract action, the asset object itself just needs to be cast to a `String`.

```ts
const balance = Asset.from("10.0000 FOO")
```

### Accessing numeric values

If just the number

```ts
const balance = Asset.from("10.0000 FOO")
console.log(balance.value) // Number(10)

const balance = Asset.from("0.0001 FOO")
console.log(balance.value) // Number(0.0001)
```

### Working with Integers

All storage and mathmatical operations use Integers. The `Int64` value of an asset is stored as the `units` property on every asset.

```ts
const balance = Asset.from("10.0000 FOO")
console.log(balance.units) // Int64.from(10000)
console.log(Number(balance.units)) // Number(10000)

const balance = Asset.from("0.0001 FOO")
console.log(balance.units) // Int64.from(1)
console.log(Number(balance.units)) // Number(1)
```

```ts
const balance = Asset.from("10.0000 FOO")
```

```ts
const balance = Asset.from("10.0000 FOO")
```

```ts
const balance = Asset.from("10.0000 FOO")
```

```ts
const balance = Asset.from("10.0000 FOO")
```
