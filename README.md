# Getting Started with Create React App

```sh
npx create-react-app survey-validation-libraries --template typescript
```

## Zod

https://github.com/colinhacks/zod

```sh
yarn add zod
```

## tiny-schema-validator

https://github.com/5alidz/tiny-schema-validator

```sh
yarn add tiny-schema-validator
```

## Material UI

https://mui.com/

```sh
yarn add @mui/material @mui/styled-engine-sc styled-components
yarn add @mui/icons-material
```

# Zod vs tiny-schema-validator

## validator (type)

| type       | zod            | tiny-schema-validator |
| :--------- | :------------- | :-------------------- |
| Constants  | literal()      | constant()            |
| Primitives | string()       | string()              |
|            | number()       | number()              |
|            | bigint()       |                       |
|            | boolean()      | boolean               |
|            | date()         |                       |
|            | undefined()    |                       |
|            | null()         |                       |
|            | void()         |                       |
|            | any()          |                       |
|            | unknown()      |                       |
|            | never()        |                       |
| Others     | enum()         | union(constant())     |
|            | optional()     |                       |
|            | nullable()     |                       |
|            | object()       |                       |
|            | array()        | listof()              |
|            | tuple()        | list()                |
|            | union()        | union()               |
|            | record()       | record()              |
|            |                | recordof()            |
|            | map()          |                       |
|            | set()          |                       |
|            | intersection() |                       |
|            | promise()      |                       |
|            | instanceof()   |                       |
|            | function()     |                       |
|            | preprocess()   |                       |

### Literals / Constants

|      | zod                           | tiny-schema-validator         |
| :--- | :---------------------------- | :---------------------------- |
| type | `string \| number \| boolean` | `string \| number \| boolean` |

### Strings

|                | zod        | tiny-schema-validator |
| :------------- | :--------- | :-------------------- |
| validation     | max()      | maxLength             |
|                | min()      | minLength             |
|                | length()   |                       |
|                | email()    |                       |
|                | url()      |                       |
|                | uuid()     |                       |
|                | cuid()     |                       |
|                | regex()    | pattern               |
|                | nonempty() |                       |
|                | optional() | optional              |
| custom message | yes        | yes                   |

### Numbers

|                | zod           | tiny-schema-validator      |
| :------------- | :------------ | :------------------------- |
| validation     | gt()          |                            |
|                | gte()         | min                        |
|                | lt()          |                            |
|                | lte()         | max                        |
|                | int()         |                            |
|                |               | is ('integer' \| 'float' ) |
|                | positive()    |                            |
|                | nonnegative() |                            |
|                | negative()    |                            |
|                | nonpositive() |                            |
|                | multipleOf()  |                            |
| custom message | yes           | yes                        |

### Booleans

|                | zod | tiny-schema-validator |
| :------------- | :-- | :-------------------- |
| custom message | yes | No                    |

### Optionals

#### zod

`optional` can be used for the following two patterns,

```ts
const schema = z.optional(z.string());
schema.parse(undefined); // => returns undefined
type A = z.infer<typeof schema>; // string | undefined

const user = z.object({
  username: z.string().optional(),
});
type C = z.infer<typeof user>; // { username?: string | undefined };
```

`.unwrap` method can remove `optional` from optional types.

```ts
const optionalString = z.string().optional();
const schema = optionalString.unwrap();
type A = z.infer<typeof schema>; // string
```

Optionals should work for the following cases,

```ts
const schemaA = z.array(z.string().optional());
type A = z.infer<typeof A>; // (string | undefined)[]

const schemaB = z.optional(z.array(z.string()));
type B = z.infer<typeof B>; // string[] | undefined
```

#### tiny-schema-validator

`string`, `number`, `boolean`, `list`, `listof`, `record` and `recordof` can be optional by passing the config.
Also custom validators can be used as,

```ts
const alphaNumeric = (() => {
  const config = {
    pattern: [/^[a-zA-Z0-9]*$/, "only-letters-and-number"],
  };
  return {
    required: (additional) =>
      _.string({ ...additional, ...config, optional: false }), // inferred as Required
    optional: (additional) =>
      _.string({ ...additional, ...config, optional: true }), // inferred as Optional
  };
})();

const Person = createSchema({
  // ...
  username: alphaNumeric.required({ maxLength: [20, "username-too-long"] }),
  // ...
});
```

`list`, `listof` and `recordof` receive validator(s) as a parameter, but the optional property of the validator is ignored.
The optional property only works for themselves, not validators are passed.

```ts
_.recordof(_.string({ optional: true /* THIS IS IGNORED */ }));
_.list([_.number({ optional: true /* THIS IS IGNORED */ }), _.number()]);

_.recordof(_.string(), { optional: true }); // THIS WORKS
_.list([_.number(), _.number()], { optional: true }); // THIS WORKS
```

## schema

### Zod

| method         |                                                                                          |
| :------------- | :--------------------------------------------------------------------------------------- |
| parse          | throws an error when the data is invalid.<br /> otherwise, it returns data.              |
| parseAsync     | Similar to `parse`. use for asynchronous refinements or transforms.                      |
| safeParse      | checks if the data is valid and then returns `{ success, data }` or `{ success, error }` |
| safeParseAsync | Similar to `safeParse`. use for asynchronous refinements or transforms.                  |
| refine         | Provide custom validation logic.                                                         |
| superRefine    | See: https://github.com/colinhacks/zod#superrefine                                       |
| transform      | Transform data after parsing.                                                            |
| default        | Set "default values".                                                                    |
| optional       | Returns an optional version of a schema.                                                 |
| nullable       | Returns an nullable version of a schema.                                                 |
| nullish        | Returns an "nullish" (`null` or `undefined`) version of a schema.                        |
| array          | Returns an array schema for the given type.                                              |
| promise        | Makes schema promise types                                                               |
| or             | Makes schema union types                                                                 |
| and            | Create intersection types                                                                |

### tiny-schema-validator

| method   |                                                                             |
| :------- | :-------------------------------------------------------------------------- |
| is       | checks if the data is valid and then returns boolean.                       |
| validate | returns errors if data is invalid (not throw).                              |
| produce  | throws an error when the data is invalid.<br /> otherwise, it returns data. |
| embed    | embeds the schema in other schemas                                          |
