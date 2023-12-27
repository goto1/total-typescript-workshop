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

#### Excluding Parts of a Discriminated Union

Use the `Exclude` type helper to construct a type by excluding from `UnionType` all union members that are assignable to `ExcludedMembers`.

- [Exclude<UnionType, ExcludedMembers> | TypeScript: Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers)

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

type NonKeyDownEvents = Exclude<Event, { type: "keydown" }>;

type tests = [
  Expect<
    Equal<
      NonKeyDownEvents,
      | { type: "click"; event: MouseEvent }
      | { type: "focus"; event: FocusEvent }
    >
  >
];
```

#### Extract Object Properties into Individual Types

Use the _indexed access type_ to look up a specific property on another property.

- [Indexed Access Types | TypeScript: Documentation](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)

```ts
export const fakeDataDefaults = {
  String: "Default string",
  Int: 1,
  Float: 1.14,
  Boolean: true,
  ID: "id",
};

type FakeDataDefaults = typeof fakeDataDefaults;

export type StringType = FakeDataDefaults["String"];
export type IntType = FakeDataDefaults["Int"];
export type FloatType = FakeDataDefaults["Float"];
export type BooleanType = FakeDataDefaults["Boolean"];
export type IDType = FakeDataDefaults["ID"];

type tests = [
  Expect<Equal<StringType, string>>,
  Expect<Equal<IntType, number>>,
  Expect<Equal<FloatType, number>>,
  Expect<Equal<BooleanType, boolean>>,
  Expect<Equal<IDType, string>>
];
```

#### Extract the Discriminator from a Discriminated Union

Use the _indexed access type_ to access parts of a discriminated union.

- [Indexed Access Types | TypeScript: Documentation](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)

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

type EventType = Event["type"];

type tests = [Expect<Equal<EventType, "click" | "focus" | "keydown">>];
```

#### Resolve an Object's Values as Literal Types

Use the `const` assertion to signal to the language that:

- no literal types in that expression should be widened (e.g. no going from `"hello"` to `string`)
- object literals get `readonly` property
- array literals become `readonly` tuples

```ts
export const programModeEnumMap = {
  GROUP: "group",
  ANNOUNCEMENT: "announcement",
  ONE_ON_ONE: "1on1",
  SELF_DIRECTED: "selfDirected",
  PLANNED_ONE_ON_ONE: "planned1on1",
  PLANNED_SELF_DIRECTED: "plannedSelfDirected",
} as const;

export type GroupProgram = (typeof programModeEnumMap)["GROUP"];
export type AnnouncementProgram = (typeof programModeEnumMap)["ANNOUNCEMENT"];
export type OneOnOneProgram = (typeof programModeEnumMap)["ONE_ON_ONE"];
export type SelfDirectedProgram = (typeof programModeEnumMap)["SELF_DIRECTED"];
export type PlannedOneOnOneProgram =
  (typeof programModeEnumMap)["PLANNED_ONE_ON_ONE"];
export type PlannedSelfDirectedProgram =
  (typeof programModeEnumMap)["PLANNED_SELF_DIRECTED"];

type tests = [
  Expect<Equal<GroupProgram, "group">>,
  Expect<Equal<AnnouncementProgram, "announcement">>,
  Expect<Equal<OneOnOneProgram, "1on1">>,
  Expect<Equal<SelfDirectedProgram, "selfDirected">>,
  Expect<Equal<PlannedOneOnOneProgram, "planned1on1">>,
  Expect<Equal<PlannedSelfDirectedProgram, "plannedSelfDirected">>
];
```

#### Create a Union From an Object's Values

Use a union into an indexed access type to get the specific members as another union, or use `Exclude` in an indexed access to exclude members that you _don't_ want, instead of adding all members that you do want.

```ts
export const programModeEnumMap = {
  GROUP: "group",
  ANNOUNCEMENT: "announcement",
  ONE_ON_ONE: "1on1",
  SELF_DIRECTED: "selfDirected",
  PLANNED_ONE_ON_ONE: "planned1on1",
  PLANNED_SELF_DIRECTED: "plannedSelfDirected",
} as const;

// Solution #1
export type IndividualProgram = (typeof programModeEnumMap)[
  | "ONE_ON_ONE"
  | "SELF_DIRECTED"
  | "PLANNED_ONE_ON_ONE"
  | "PLANNED_SELF_DIRECTED"];

// Solution #2
export type IndividualProgram = (typeof programModeEnumMap)[Exclude<
  keyof typeof programModeEnumMap,
  "GROUP" | "ANNOUCEMENT"
>];

type tests = [
  Expect<
    Equal<
      IndividualProgram,
      "1on1" | "selfDirected" | "planned1on1" | "plannedSelfDirected"
    >
  >
];
```

#### Get All of an Object's Values

Use `typeof` and `keyof` operators with indexed access to get all of an object's values.

```ts
const frontendToBackendEnumMap = {
  singleModule: "SINGLE_MODULE",
  multiModule: "MULTI_MODULE",
  sharedModule: "SHARED_MODULE",
} as const;

type Obj = typeof frontendToBackendEnumMap;

type BackendModuleEnum = Obj[keyof Obj];

type tests = [
  Expect<
    Equal<BackendModuleEnum, "SINGLE_MODULE" | "MULTI_MODULE" | "SHARED_MODULE">
  >
];
```

#### Create Unions out of Array Values

```ts
const fruits = ["apple", "banana", "orange"] as const;

type AppleOrBanana = (typeof fruits)[0 | 1];
type Fruit = (typeof fruits)[number];

type tests = [
  Expect<Equal<AppleOrBanana, "apple" | "banana">>,
  Expect<Equal<Fruit, "apple" | "banana" | "orange">>
];
```
