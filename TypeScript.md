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

- Some rules can be hard to follow, slowing coding down until skill increases.
- Initially takes more time, with setting up types, interfaces.

## Types

### Structural or duck typing

If Two objects have the same shape, they are the same type. Any object with x and y being numbers would fit. `x: number, y: number`

### Primitive Types

- boolean, number, string, null, undefined

#### undefined vs unknown vs optional

- **Null**: Object have no value.
- **Undefined**: value given to uninitialized variable.
- **Any**: Dynamic type used to opt out of type checking. Maybe from a third party. Or before we have decided on how the data should look.
- **Unknown** represents `any` but are safer because it's not legal to do anything with `unknown`.
- **Void**: Opposite of Any. When we return undefined. Does not prevent a function from returning anything.
- **Optional** parameters are declared with `?`, making them possible undefined.
- **Never**: When we are certain something will never happen, we use the `never` type. When type guard prevents something from happen orw hen there is no return

#### User Defined Data Types

- Enums: Great for states or options. Fruit.Apple. Loading.Error. Memory efficient custom constants
- Classes: Great for complex objects, and encapsulating logic. Will feel logical for a C# dev.
- Interfaces: The blueprint for an object. Preferred over type.
- Tuples: arrays with fixed number of elements that can have values of different data types.

## Interface vs type

- Intersection merges types, which means you get a type with all the properties of the merged types. Extends does not allow repeated props.
- Two types with the same name = error
  Two interfaces with the same name = the two interfaces are merged
- Types cannot extend another type, but interfaces can extend another interface
- Interfaces cannot be mapped types, i.e. `type Optional<T> = { [K in keyof T]?: T[K] | undefined }`

### Defining Types

- Use type when defining an alias for primitive types (string, boolean, number, bigint, symbol, etc)
- Use type when defining tuple types
- Use type when defining function types `type Sum = (x: number, y: number) => number`
- Use type when defining a union type.
- Use type when trying to overload functions in object types via composition/intersection
- Use type for mapped types.

#### Union Types

- Formed from 2+ other types (members), and can be any of those types. "Either or" values for type.
- A flexible way to make sure your input corresponds to restrictions. `id: number | string` is a easy and clear way to make sure your react component is used the right way.
- String Literal is this, but for _specific_ strings. `type Result = "pass" | "fail"`

#### Intersection Types:

- Merges 2 types. `type ColorfulCircle = Colorful & Circle;`

### Type Assertions

For when you know something that TS doesn't. `const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;`. The rule allows conversions from a less specific version to a more specific version, but NOT impossible coercions.

### Type Guards

- check type use `typeof`
- use `'in`operator to check if property exists.
- Discriminated unions: Separate an object into 2+ types with different values for a specific property. This property can be used to check type of object.
- `instanceof` useful for values that can be constructed with `new`
- Truthiness narrowing: Falsy: 0, NaN, "", null, undefined

```typescript
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

## Functions

- Type does not have to be specified when it can be inferred.
- Parameters can be optional (?) or default (=).
- Rest parameters uses `...` syntax.
- An array can be made into an immutable tuple with `as const`.
- parameter destructing unpacks objects.
- Generics are used when a function does not need to know the type. The return type is decided by the function call.

### Signatures

- Call Signatures: the function type expression syntax doesn’t allow for declaring properties. If we want to describe something callable with properties, we can write a call signature in an object type
- Construct Signatures: Constructor that returns new object.
- Overload Signatures + Implementation Signature. Useful when you want to accept 1 or 3. parameters, but not 3. The implementation signature will have 2 optional params, but won't be callable with 2.

### Constraints

- `<T extends { length: number }>` The type must have `length: number` parameter.
- type parameter constraints: `<T, K extends keyof T>(obj: T, key: K)`
- class type constraints `<T>(c:  {new (): T }) : T { return new c() }`

### Non-null Assertion operator (Postfix !)

To remove null check from a type without explicit checking, write `!` after the expression. For when you KNOW the value isn't null.

## Object Types

- Readonly can be modified, just not re-written to.
- Index signature: when you know the shape of values, but not all the names. When I know key and value types. I could then use `x in y` for [key: string].
- Extending types helps create "almost the same" types without the need to repeat code. `interface ColorfulCircle extends Colorful, Circle {}`.
- Generics for when an interface needs to be used for different types.

## Classes

Template for creating objects.

- Fields, Constructors, Methods
- Static members, but not classes (just use objects for them). Careful with the names, as classes are functions.
  -constructor body not necessary if visibility modifiers are used for the arguments.

## Types from Types

### Keyof:

- Takes an object type, and produces a string or numeric _literal_ union of it's keys. (x | y)
- type P = keyof Point; P can be of the objects keys.
- If the type has a string or number index signature, keyof will return those types instead. (string | number)

### Typeof

- `ReturnType<T>`: For when you have access to the function, but not the type definition of the return type.

### Indexed Access Type

- To get the type of a specific property.

### Conditional Types

- Easier typing for overloads

```typescript
type NameOrId<T extends number | string> = T extends number ? IdLabel : NameLabel;

function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {..}

let a = createLabel("typescript");
// let a: NameLabel

let b = createLabel(2.8);
// let b: IdLabel
```

### Mapped Types

Generic type which uses an union of `PropertyKey`s to iterate through keys to create a type.

```typescript
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};
// OptionsFlags will take all the properties from the type Type and change their values to be a boolean.

type FeatureFlags = {
  darkMode: () => void;
  newUserProfile: () => void;
};

type FeatureOptions = OptionsFlags<FeatureFlags>;
// is equal to
type FeatureOptions = {
  darkMode: boolean;
  newUserProfile: boolean;
};

// Add and remove mutability and optionality with +/-
```

#### Key Remapping via `as`

Things can get cool with template literal strings.

```typescript
type Getters<Type> = {
  [Property in keyof Type as `get${Capitalize<
    string & Property
  >}`]: () => Type[Property];
};

interface Person {
  name: string;
  age: number;
  location: string;
}

type GetPerson = Getters<Person>;
// results in
type GetPerson = {
  getName: () => string;
  getAge: () => number;
  getLocation: () => string;
};

// You can filter out keys by producing never via a conditional type
type RemoveKindField<Type> = {
  [Property in keyof Type as Exclude<Property, "kind">]: Type[Property];
};

// properties from values.
type EventConfig<Events extends { kind: string }> = {
  [E in Events as E["kind"]]: (event: E) => void;
};
```

### Template literal types

- When an union is used in the interpolated position, the type is set of _every possible string literal that could be represented by each union member_
  For each interpolated position in the template literal, the unions are cross multiplied.

## Utility Types

### Value transformation types

- Awaited : `type A = Awaited<Promise<string>>` type A = string
- Partial: All properties are optional.
- Required: All properties are required.
- Readonly: All properties are readonly.
- Pick<Type, Keys>: Type from selected properties `Pick<Person, "name" | "id">`
- Omit<Type, Keys>: Type from excluded properties `Omit<Person, "address" | "id">`
- Record<Type, Keys>: `Record<T, K>`. Useful for mapping properties from one type to another, and keep dependencies on both types to see if we break it.

```typescript
const statusTypes: Record<Status, { icon: string; color: string }> = {
  not_started: { icon: "icon-not-started", color: "gray" },
  progress: { icon: "icon-progress", color: "orange" },
  completed: { icon: "icon-completed", color: "green" },
  failed: { icon: "icon-failed", color: "red" },
};
```

### Union manipulation types

- Exclude<UnionType, ExcludedMembers>: Excluding from UnionType all union members that are assignable to ExcludedMembers. Exclude is sort of like Array.filter or Omit, but for union types.

- Extract<Type, Union>: Opposite of Exclude.
- NonNullable<Type>: Exclude<Type, null | undefined>;

### Function Types

- Parameters<Type>: Constructs a tuple type from the types used in the parameters of a function Type.
  `type AddParameters = Parameters<typeof add>; // => [a: number, b: number]`
- ReturnType<Type>: Get return type of any function we passes in. Useful to get return types from built in functions, or from a library. Particularly if it doesn't export the return type definition.
- InstanceType<Type>: Takes a class type and returns the type that will be instantiated by the constructor

### String Manipulation Types

- Uppercase<StringType>
- Lowercase<StringType>
- Capitalize<StringType>
- Uncapitalize<StringType>

## "as const

- You should not have multiple sources of truth for a type.
- "as const" makes a object readonly.
- Alternative to enums.
- Different to Object.freeze, because freeze is shallow.

- (typeof)[keyof keyof]
