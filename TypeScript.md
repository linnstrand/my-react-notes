# TypeScript

- Syntactical superset of JavaScript.
- Earlier Error Catching with Static Type Checking (error caught without needing to run the code).

### Why it's great

As long as you are careful with type annotations...

- Catches spelling errors, and gives suggestions.
- Autocomplete, speeds up the coding by providing a list of available properties as you type.
- Tells you if you forgot to call a method, or your functions return value isn't what you expect.
- Strict rules, forced to follow rules when writing code, making code clearer.
- Code changes that breaks code will be easier to spot. You changed a parameter for an object, and forgot that it was used in another part of a large code base? With strict TS, you are less lightly to trigger bugs in another teams code.

#### downsides?

- Some rules can be hard to follow.
- Initially takes more time, with setting up types, interfaces.

## Types

- `typeof` For type of variable

### Primitive Types

- boolean, number, string

### undefined vs unknown vs optional

- Null: Object have no value.
- Undefined: value given to uninitialized variable.
- Optional parameters are declared with `?`, making them possible undefined.

#### Any

Dynamic type used to opt out of type checking. Maybe from a third party. Or before we have decided on how the data should look.

#### Never

WHen we are certain something will never happen, we use the `never` type

### User Defined Data Types

- Enums: Great for states or options. Fruit.Apple. Loading.Error. Memory efficient custom constants
- classes: Great for complex objects, and encapsulating logic. Will feel logical for a C# dev.
- interfaces: The blueprint for an object. Similar to declaring type, but creates a name that is used everywhere.
- tuples: arrays with fixed number of elements that can have values of different data types.

### Complex Types

#### Literal Types

For _specific_ strings and numbers in type positions.

#### Unions

Formed from 2+ other types (members). "Either or" values for type. To work with union types, see type guards. (`typeof`, `Array.isArray` )

- A flexible way to make sure your input corresponds to restrictions. `type Result = "pass" | "fail"` is a easy and clear way to make sure your react component is used the right way.
- String Literal is this, but for strings

Generics:
`type StringArray = Array<string>`

### Intersection Types: Merges 2 types.

### Structural or duck typing

If Two objects have the same shape, they are the same type. Any object with x and y being numbers would fit.

```typescript
interface Point {
  x: number;
  y: number;
}
```

### Type Assertions

For when you know something that TS doesn't. `const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;`. The rule allows conversions from a less specific version to a more specific version, but NOT impossible coercions.

##### Non-null Assertion operator (Postfix !)

To remove null check from a type without explicit checking, write `!` after the expression. For when you KNOW the value isn't null.

## Narrowing Types

The process of refining types to more specific types than declared is called narrowing.

### Type Guards

- check type use `typeof`
- use `'in`operator to check if property exists.
- Discriminated unions: Separate an object into 2+ types with different values for a specific property. This property can be used to check type of object.
- `instanceof` useful for values that can be constructed with `new`

##### User defined type guard

```typescript
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

### Truthiness narrowing

Falsy: 0
NaN
""
null
undefined

## Functions
