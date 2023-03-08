# GraphQL Guide

[Resource](https://graphql.org/learn/)

## [Queries and Mutations](https://graphql.org/learn/queries/)

**Important**: GraphQL responses have the same shape as the results, so everything is always as expected.

Queries can be for subfields or even objects.

### Comments

Comments in graphql start with `#`, such as:

```graphql
{
  hero {
    name
    # Queries can have comments!
  }
}
```

### Fields

Every field can get it's own set of arguments, event scalars, useful for implementing data transformations.

Fields are passed to objects like `(field_name: field_value)`. For example:

```graphql
{
  human(id: "1000") {
    name
    height(unit: FOOT)
  }
}
```

GraphQL has a default set of types, but you can define your own types to be queried.

### Aliases

**Aliases** are useful to make different queries for the same type. An alias is defined like `alias: collection(...fields)`, for example:

```graphql
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}

# Gives

{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```

### Fragments

**Fragments** are reusable units usable in queries. Fragments contain a set of fields and you can include them when you need to like `...fragment_name`, for example:

```graphql
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```

**Fragments** can use variables declared in the query (or mutation) too, for example:

```graphql
query HeroComparison($first: Int = 3) {
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  friendsConnection(first: $first) {
    totalCount
    edges {
      node {
        name
      }
    }
  }
}
```

### Operation Name

_Queries_ are defined with the keyword `query`, and can be _anonymous_ or _have a name_. Named queries are helpful when debugging queries.

Named query:

```graphql
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
```

Anonymous query:

```graphql
query {
  hero {
    name
    friends {
      name
    }
  }
}
```

### Variables

When working with variables, there's no need to manipulate the query string, GraphQL has variables you can work with.

To start using variables:

1. Replace the static query value with `$variableName`
2. Declare `$variableName` as one of the variables accepted by the query (_Variable Definition_)
3. Pass `variableName: value` in the sepparate, transport-specific usually JSON

```graphql
# QUERY
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}

# TRANSPORT JSON
{
  "episode": "JEDI"
}
```

**NEVER** do string interpolation for the queries

#### Variable Definitions

**Variable definition** is the part `($episode: Episode)` from the previous query. It has the variable name with `$` and the type (in that case `Episode`).

**Optional variables** are all unless they have a `!` next to the type. If the variable requires a non null argument, the variable needs to be required as well.

#### Default Variables

**Default variables** can be declared by adding `= value` to the variable definition, such as `($episode: Episode = JEDI)`. You can call the query without the param.

### Directives

**Directives** is attached to a field or fragment inclussion and affects how the query executes and which parts execute.

There are 2 main _directives_:

- `@include(if: Boolean)` --> Include the field only if the `if` condition is `true`
- `@skip(if: Boolean)` --> Skip the field only if the `if` condition is `true`

GraphQL servers can add custom directives, but the previous 2 have to always be supported.

### Mutations

It is a convention that in order to modify values, a **Mutation** should be used. _Mutations_ also return an object type, so you can ask for fields in that object.

```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    # Data returned from new object create
    stars
    commentary
  }
}
# VARIABLES
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie! BLABLA"
  }
}
```

**Query** fields run in **parallel**, but **mutations** run in **series**.

### Inline Fragments

GraphQL has support for **interfaces** and **unions**. If the query returns an union or interface type, you can use _inline fragments_ to access the concrete underlying type, for example:

```graphql
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    # Character can be either Human or Droid
    # Character is an interface
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
# VARIABLES
{
  "ep": "EMPIRE"
}
```

#### Meta Fields

There are some situations where you don't know exactly what you might get, in those cases you can query for `__typename` which contains the type of the object being returned. In that scenario you can use _inline fragments_ to determine what to return. For example:

```graphql
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```

The **search** returns a union that can be any of multiple types. There are multiple meta fields for each object.

## [Schemas and Types](https://graphql.org/learn/schema/)

Every GraphQL service defines which types you can query for.

### Object Types

This is the most basic components of a GraphQL schema. Objects can be represented like:

```graphql
# Character is a GraphQL Object Type
type Character {
  # Fields from Character
  name: String! # Built-in scalar types, non-nullable because of !
  appearsIn: [Episode!]! # Array of episode objects, it will allways be an array, empty or not
}
```

**Scalar types** --> Resolve to a single scalar object, can't have sub-selections.

### Arguments

Fields can have 0 or more arguments, for example:

```graphql
type Starship {
  id: ID!
  name: String!
  length(unit: LengthUnit = METER): Float
}
```

All arguments are named can be _required_ or _optional_. If optional, it can have a _default_ value.

### Query and Mutation Types

The **query** and **mutation** types are special schema types. All services will have a _query_ but may not have a _mutation_ type.

Those definitions define which fields you can query for on each _query_ or _mutation_.

### Scalar Types

At some point the types must resolve to something concrete, so scalar types are the leaves of the queries.

The _scalar types_ defined in GraphQL are:

- `Int` --> Signed 32-bit integer
- `Float` --> Signed double-precission floating-point value
- `String` --> UTF-8 charecter sequence
- `Boolean` --> `true` or `false`
- `ID` --> Represents a unique identifier, often to fetch and object or as the key of the cache. ID is serialized the same way as a String, but specifying ID implies it's not human readable.

GraphQL service implementations can define custom scalar types, but it's up to the service implementation to define how the type should be serialized, deserialized, and validated.

### Enum Types

`enum` types are special types restricted to a set of scalar values. It can allow for validation of the arguments and to better communicate errors.

An `enum` can be defined like:

```graphql
enum Episode {
  NEWHOPE
  EMPIRE
  JEDI
}
```

### Lists and Non-Null

Fields can be declared as **non-null** by appending a `!` to the name. If the field receives a `null` value, it will trigger a GraphQL execution error.

**Lists** work in a similar way, it's declared that the field will return an array of the set type by surrounding it with `[]`.

These modifiers can be combined:

- List of non-null strings --> `myField: [String!]`
  ```graphql
  myField: null // valid
  myField: [] // valid
  myField: ['a', 'b'] // valid
  myField: ['a', null, 'b'] // error
  ```
- Non-null list of strings --> `myField: [String]!`
  ```graphql
  myField: null // error
  myField: [] // valid
  myField: ['a', 'b'] // valid
  myField: ['a', null, 'b'] // valid
  ```

These types can be _nested_ in any number without limits.

### Interfaces

_Interfaces_ are abstract types that include a set of fields that a type must include to implement the interface. For example

```graphql
interface Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
}

type Human implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  starships: [Starship]
  totalCredits: Int
}

type Droid implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  primaryFunction: String
}
```

If you are querying for an Interface type, in order to access parameters on the underlying type you need to use **inline fragments** (`... on Type {...}`).

### Union Types

Similar to interfaces, but you don't specify common fields. For example:

```graphql
union SearchResult = Human | Droid | Starship
```

The underlying types have to be **concrete** types, they cannot be other unions or interfaces.

In case you need to access the values of the underlying types, **inline fragments** become useful again here:

```graphql
{
  search(text: "an") {
    __typename
    ... on Character {
      name
    }
    ... on Human {
      height
    }
    ... on Droid {
      primaryFunction
    }
    ... on Starship {
      name
      length
    }
  }
}
```

### Input Types

In the case of mutations, it's possible that you might need to pass the whole object. In that case, it differences by using the keyword `input`, such as:

```graphql
input ReviewInput {
  stars: Int!
  commentary: String
}
```

Fields can make reference to other _input_ types but not _output_ types, you cannot mix that. Input type fields cannot have arguments too.

## Validations

Some common validations (more validation info [here](https://github.com/graphql/graphql-js/tree/main/src/validation)):

- Fragments cannot call themselves

  ```graphql
  {
    hero {
      ...NameAndAppearancesAndFriends
    }
  }

  fragment NameAndAppearancesAndFriends on Character {
    name
    appearsIn
    friends {
      ...NameAndAppearancesAndFriends
    }
  }
  ```

- Fields for a type must exist, cannot query non-existent fields on types:

  ```graphql
  # INVALID: favoriteSpaceship does not exist on Character
  {
    hero {
      favoriteSpaceship
    }
  }
  ```

- Cannot query just for an object, need to specify the fields:

  ```graphql
  # INVALID: hero is not a scalar, so fields are needed
  {
    hero
  }
  ```

- Cannot query for fields inside a scalar:

  ```graphql
  # INVALID: name is a scalar, so fields are not permitted
  {
    hero {
      name {
        firstCharacterOfName
      }
    }
  }
  ```

- If querying for an interface, you cannot include fields of the underlying type without an inline fragment:

  ```graphql
  # INVALID: primaryFunction does not exist on Character
  {
    hero {
      name
      primaryFunction
    }
  }
  ```
