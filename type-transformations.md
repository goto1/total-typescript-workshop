## Type Transformations

### Inference Basics

#### Get the Return Type of a Function

Use the `ReturnType` type helper to construct a type consisting of the return type of a function.

- [ReturnType<Type> | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#returntypetype)

```ts
const myFunc = () => {
  return "hello";
};

type MyFunc = typeof myFunc;
/**
 *  type MyFunc = () => string
 */

type MyFuncReturn = ReturnType<MyFunc>;

type tests = [Expect<Equal<MyFuncReturn, string>>];
```

#### Extract Function Parameters Into A Type

Use the `Parameters` type helper to construct a tuple type from the types used in the parameters of a function type `Type`. This utility type helper is really useful for extracting type information that you don't necessarily have control of, such as code in external libraries.

- [Parameters<Type> | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#parameterstype)

```ts
const makeQuery = (
  url: string,
  opts?: {
    method?: string;
    headers?: {
      [key: string]: string;
    };
    body?: string;
  }
) => {};

type MakeQueryParameters = Parameters<typeof makeQuery>;

type MakeQueryParametersSecondArgument = MakeQueryParameters[1];
/**
 * type MakeQueryParametersSecondArgument = {
 *    method?: string | undefined;
 *    headers?: { [key: string]: string } | undefined;
 *    body?: string | undefined;
 * }
 */

type tests = [
  Expect<
    Equal<
      MakeQueryParameters,
      [
        url: string,
        opts?: {
          method?: string;
          headers?: {
            [key: string]: string;
          };
          body?: string;
        }
      ]
    >
  >
];
```

#### Extract The Awaited Result of a Promise

Use the `Awaited` type helper to model operations like `await` in `async` functions, or the `.then()` method on `Promise`s - specifically, the way they recursively unwrap `Promise`s.

- [Awaited<Type> | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#awaitedtype)

```ts
const getUser = () => {
  return Promise.resolve({
    id: "123",
    name: "John",
    email: "john@example.com",
  });
};

type GetUserPromise = ReturnType<typeof getUser>;
type ReturnValue = Awaited<GetUserPromise>;

type tests = [
  Expect<Equal<ReturnValue, { id: string; name: string; email: string }>>
];
```

#### Create a Union Type From an Object's Keys

Use the `keyof` operator, which takes an object type, to produce a string or numeric literal union of its keys.

- [Keyof Type Operator | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)

```ts
const testingFrameworks = {
  vitest: {
    label: "Vitest",
  },
  jest: {
    label: "Jest",
  },
  mocha: {
    label: "Mocha",
  },
};

type TestingFrameworks = typeof testingFrameworks;
type TestingFramework = keyof TestingFrameworks;

type tests = [Expect<Equal<TestingFramework, "vitest" | "jest" | "mocha">>];
```

### Unions and Indexing

#### Understand The Terminology Around Unions

A discriminated union has common properties which are used to differentiate between members of the union. In the case below for `type A`, `type` is the discriminator. This common aspect is called the discriminator.

A union type is a way to represent a value that can be one of several types. It allows a variable, parameter, or property to hold values of different types. A union type is expressed using the pipe (`|`) symbol between the types, as seen below for `type B`.

- [Union types | TypeScript: Documentation - Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types)

```ts
// Discriminated union
type A =
  | {
      type: "a";
      a: string;
    }
  | {
      type: "b";
      b: string;
    }
  | {
      type: "c";
      c: string;
    };

// Union
type B = "a" | "b" | "c";
```

#### Extracting Member of a Discriminated Union

Use the `Extract` type helper to construct a type by extracting from `Type` all union members that are assignable to `Union`.

- [Extract<Type, Union> | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#extracttype-union)

```ts
export type Event =
  | {
      type: "click";
      event: MouseEvent;
    }
  | {
      type: "focus";
      event: FocusEvent;
    }
  | {
      type: "keydown";
      event: KeyboardEvent;
    };

type ClickEvent = Extract<Event, { event: MouseEvent }>;

type tests = [Expect<Equal<ClickEvent, { type: "click"; event: MouseEvent }>>];
```
