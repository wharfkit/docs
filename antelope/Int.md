# Int

Binary integer with the underlying value represented by a BN.js instance. 

Follows C++11 standard for arithmetic operators and conversions.

## Types

A collection of all the integer types used in Antelope

- Int8
- Int16
- Int32
- Int64
- Int128
- UInt8
- UInt16
- UInt32
- UInt64
- UInt128
- VarInt
- VarUInt

## Usage

These examples will use `UInt64` as the type of `Int`, but all operations and usage should be similar no matter which of the types are used.

Many operations will return an instance of BN.js, which you can read more about here:

https://github.com/indutny/bn.js

### Static Methods

Retrieving minimum and maximum values for the type of `Int`.

```ts
// Retrieve the minimum value for a UInt64 as a UInt64
const minimum = UInt64.min() 

console.log(minimum)
// BN { negative: 0, words: [ 0 ], length: 1, red: null }

console.log(Number(minimum))
// 0 
```

```ts
// Retrieve the maximum value for a UInt64 as a UInt64
const maximum = UInt64.max()

console.log(maximum)
/*
BN {
  negative: 0,
  words: [ 67108863, 67108863, 4095, <1 empty item> ],
  length: 3,
  red: null
}
*/

console.log(Number(maximum)) 
// 18446744073709552000
```

### Math

A number of mathematical operations has been implemented using bn.js and exposed through helpers on every `Int` type.

All return values will be typed to match the types of the integers used in the operation.

These operations are exposed in two ways, one which allows mutation of the current integer value and another which performs the operation without mutation and returns the value.

#### Return Value

```ts
const a = UInt64.from(2)
const b = UInt64.from(4)

const sum = a.adding(b)
// 2 + 4 = 6
console.log(sum) // UInt64.from(6)
console.log(Number(sum)) // 6

const difference = b.subtracting(a)
// 4 - 2 = 2
console.log(difference) // UInt64.from(2)
console.log(Number(difference)) // 2

const product = a.multiplying(b)
// 2 * 4 = 8
console.log(product) // UInt64.from(8)
console.log(Number(product)) // 8

const quotient = b.dividing(a)
// 4 / 2 = 2
console.log(quotient) // UInt64.from(2)
console.log(Number(quotient)) // 2

```

When it comes to division of integers, any remainder needs to be automatically handled. For this, a second parameter can be provided to decide which of the 3 options are used: 

 - `floor` (default): Round down to nearest integer.
 - `round`: Round to nearest integer.
 - `ceil`: Round up to nearest integer.

```ts
const a = UInt64.from(7)
const b = UInt64.from(3)

b.dividing(a)
// 7 / 3 = floor(2.33) = 2

b.dividing(a, 'round')
// 7 / 3 = round(2.33) = 2

b.dividing(a, 'ceil')
// 7 / 3 = ceil(2.33) = 3
```

#### Mutating

```ts
/** Mutating add. */
add(num: IntType)

/** Mutating subtract. */
subtract(num: IntType)

/** Mutating multiply. */
multiply(by: IntType) 

/**
* Mutating divide.
* @param behavior How to handle the remainder, default is to floor (round down).
* @throws When dividing by zero.
*/
divide(by: IntType, behavior?: DivisionBehavior) 
```


```ts
/** Add `lhs` to `rhs` and return the resulting value. */
add(lhs: Int, rhs: Int, overflow: OverflowBehavior = 'truncate'): Int

/** Add `lhs` to `rhs` and return the resulting value. */
sub(lhs: Int, rhs: Int, overflow?: OverflowBehavior): Int

/** Multiply `lhs` by `rhs` and return the resulting value. */
mul(lhs: Int, rhs: Int, overflow?: OverflowBehavior): Int

/**
* Divide `lhs` by `rhs` and return the quotient, dropping the remainder.
* @throws When dividing by zero.
*/
div(lhs: Int, rhs: Int, overflow?: OverflowBehavior): Int

/**
* Divide `lhs` by `rhs` and return the quotient + remainder rounded to the closest integer.
* @throws When dividing by zero.
*/
divRound(lhs: Int, rhs: Int, overflow?: OverflowBehavior): Int 

/**
* Divide `lhs` by `rhs` and return the quotient + remainder rounded up to the closest integer.
* @throws When dividing by zero.
*/
divCeil(lhs: Int, rhs: Int, overflow?: OverflowBehavior): Int

random() 

/**
* The underlying BN.js instance – don't modify this
* directly – take a copy first using `.clone()`.
*/
value: BN

/** Number as bytes in little endian (matches memory layout in C++ contract). */
get byteArray(): Uint8Array

/**
* Compare two integers, if strict is set to true the test will only consider integers
* of the exact same type. I.e. Int64.from(1).equals(UInt64.from(1)) will return false.
*/
equals(other: IntType | Uint8Array, strict = false) {
    const self = this.constructor as typeof Int
    if (strict === true && isInstanceOf(other, Int)) {
        const otherType = other.constructor as typeof Int
        if (self.byteWidth !== otherType.byteWidth || self.isSigned !== otherType.isSigned) {
            return false
        }
    }
    try {
        return this.value.eq(self.from(other).value)
    } catch {
        return false
    }
}

/** Mutating add. */
add(num: IntType)

/** Non-mutating add. */
adding(num: IntType)

/** Mutating subtract. */
subtract(num: IntType)

/** Non-mutating subtract. */
subtracting(num: IntType)

/** Mutating multiply. */
multiply(by: IntType) 

/** Non-mutating multiply. */
multiplying(by: IntType)

/**
* Mutating divide.
* @param behavior How to handle the remainder, default is to floor (round down).
* @throws When dividing by zero.
*/
divide(by: IntType, behavior?: DivisionBehavior) 

/**
* Non-mutating divide.
* @param behavior How to handle the remainder, default is to floor (round down).
* @throws When dividing by zero.
*/
dividing(by: IntType, behavior?: DivisionBehavior)

/**
* Convert to a JavaScript number.
* @throws If the number cannot be represented by 53-bits.
**/
toNumber()
```