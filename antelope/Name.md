```ts
/^[a-z1-5.]{0,13}$/



name.value // UInt64 value of the name


Name.from('teamgreymass')
Name.from(UInt64(...))
Name.from(name)


const string = 'teamgreymass'
const name = Name.from(string)

name.equals('teamgreymass') // true


String(name) // 'teamgreymass' (as string)
```