# GraphQL Interview Questions

## Basic Questions

### 1. What is GraphQL?
GraphQL is a query language for APIs and a runtime for executing those queries. It was developed by Facebook in 2012 and open-sourced in 2015. It provides a complete and understandable description of the data in your API.

### 2. What are the advantages of GraphQL over REST?
- Single endpoint for all queries
- Clients can request exactly the data they need
- No over-fetching or under-fetching
- Strongly typed schema
- Introspection capabilities
- Real-time data with subscriptions
- Better versioning strategy

### 3. What are the main operations in GraphQL?
- **Query**: Fetch data (read operations)
- **Mutation**: Modify data (create, update, delete)
- **Subscription**: Real-time updates via WebSocket

### 4. What is a GraphQL Schema?
A schema defines the structure of the API, including types, queries, mutations, and subscriptions. It serves as a contract between client and server.

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Query {
  user(id: ID!): User
  users: [User!]!
}
```

### 5. What are Scalar Types in GraphQL?
Built-in scalar types:
- `Int` - Signed 32-bit integer
- `Float` - Signed double-precision floating-point value
- `String` - UTF-8 character sequence
- `Boolean` - true or false
- `ID` - Unique identifier

### 6. What is the difference between `!` and `[]` in GraphQL?
- `!` - Non-nullable (field must have a value)
- `[]` - List/Array type
- `[String!]!` - Non-null array of non-null strings

## Intermediate Questions

### 7. What are Resolvers?
Resolvers are functions that resolve a value for a type or field in the schema. They fetch the actual data.

```javascript
const resolvers = {
  Query: {
    user: (parent, args, context, info) => {
      return getUserById(args.id);
    }
  }
}
```

### 8. Explain the Resolver Function Arguments
- **parent/root**: Result from the parent resolver
- **args**: Arguments passed to the field
- **context**: Shared object across all resolvers (auth, database, etc.)
- **info**: Information about the query execution state

### 9. What is the N+1 Problem in GraphQL?
The N+1 problem occurs when fetching related data requires multiple database queries. For example, fetching 100 users and their posts would result in 101 queries (1 for users + 100 for posts).

**Solution**: Use DataLoader for batching and caching.

### 10. What is DataLoader?
DataLoader is a utility that provides batching and caching for data fetching, solving the N+1 problem.

```javascript
const userLoader = new DataLoader(async (userIds) => {
  const users = await getUsersByIds(userIds);
  return userIds.map(id => users.find(user => user.id === id));
});
```

### 11. What are Fragments in GraphQL?
Fragments are reusable units of a query that can be shared across multiple queries.

```graphql
fragment UserInfo on User {
  id
  name
  email
}

query {
  user(id: "1") {
    ...UserInfo
  }
}
```

### 12. What are Directives in GraphQL?
Directives provide a way to dynamically change the structure and shape of queries.

- `@include(if: Boolean)` - Include field if true
- `@skip(if: Boolean)` - Skip field if true
- `@deprecated(reason: String)` - Mark field as deprecated

```graphql
query getUser($withEmail: Boolean!) {
  user(id: "1") {
    name
    email @include(if: $withEmail)
  }
}
```

### 13. What is Introspection?
Introspection allows querying the GraphQL schema itself to discover available types, fields, and operations.

```graphql
{
  __schema {
    types {
      name
      fields {
        name
        type {
          name
        }
      }
    }
  }
}
```

### 14. What are Input Types?
Input types are used for complex objects passed as arguments to mutations.

```graphql
input CreateUserInput {
  name: String!
  email: String!
  age: Int
}

type Mutation {
  createUser(input: CreateUserInput!): User
}
```

### 15. What are Interfaces in GraphQL?
Interfaces define a set of fields that multiple types can implement.

```graphql
interface Node {
  id: ID!
}

type User implements Node {
  id: ID!
  name: String!
}

type Post implements Node {
  id: ID!
  title: String!
}
```

## Advanced Questions

### 16. What are Union Types?
Union types represent values that can be one of several types.

```graphql
union SearchResult = User | Post | Comment

type Query {
  search(query: String!): [SearchResult!]!
}
```

### 17. Explain GraphQL Subscriptions
Subscriptions enable real-time data updates using WebSocket connections.

```graphql
type Subscription {
  messageAdded(channelId: ID!): Message
}
```

```javascript
const resolvers = {
  Subscription: {
    messageAdded: {
      subscribe: (parent, args, { pubsub }) => {
        return pubsub.asyncIterator(`MESSAGE_ADDED_${args.channelId}`);
      }
    }
  }
}
```

### 18. What is Schema Stitching?
Schema stitching combines multiple GraphQL schemas into a single unified schema.

### 19. What is Federation in GraphQL?
Apollo Federation is an architecture for composing multiple GraphQL services into a single graph. Each service owns specific types and fields.

### 20. How do you handle Authentication in GraphQL?
- Pass authentication token in HTTP headers
- Use context to validate user
- Implement authorization in resolvers

```javascript
const resolvers = {
  Query: {
    me: (parent, args, context) => {
      if (!context.user) {
        throw new Error('Not authenticated');
      }
      return context.user;
    }
  }
}
```

### 21. What is Error Handling in GraphQL?
GraphQL responses include an `errors` array alongside `data`.

```javascript
throw new ApolloError('User not found', 'USER_NOT_FOUND');
```

### 22. What are Custom Scalars?
Custom scalars define new data types beyond the built-in ones.

```javascript
const dateScalar = new GraphQLScalarType({
  name: 'Date',
  parseValue(value) {
    return new Date(value);
  },
  serialize(value) {
    return value.toISOString();
  }
});
```

### 23. What is Pagination in GraphQL?
Common pagination patterns:
- **Offset-based**: Using `offset` and `limit`
- **Cursor-based**: Using cursors with `first`, `after`, `last`, `before`
- **Relay Connection**: Standardized cursor-based pagination

```graphql
type Query {
  users(first: Int, after: String): UserConnection
}

type UserConnection {
  edges: [UserEdge]
  pageInfo: PageInfo!
}

type UserEdge {
  node: User
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}
```

### 24. What is Caching in GraphQL?
- Use unique IDs for normalization
- Implement cache-first policies
- Use Apollo Client or other caching solutions
- HTTP caching with GET requests for queries

## Tools and Libraries

### 25. Popular GraphQL Tools
- **Apollo Server**: Production-ready GraphQL server
- **Apollo Client**: Client-side state management
- **GraphQL Code Generator**: Generate types from schema
- **GraphQL Playground**: Interactive GraphQL IDE
- **Prisma**: Modern database toolkit with GraphQL
- **Hasura**: Instant GraphQL APIs over databases

## Best Practices

### 26. GraphQL Best Practices
- Design schema from client perspective
- Use DataLoader to avoid N+1 problems
- Implement proper error handling
- Use pagination for lists
- Version schema through field deprecation
- Keep resolvers thin, move logic to services
- Use meaningful names for types and fields
- Implement rate limiting and query complexity analysis
- Document schema with descriptions
- Use fragments to avoid repetition

### 27. Security Considerations
- Query depth limiting
- Query complexity analysis
- Rate limiting
- Authentication and authorization
- Input validation
- Disable introspection in production
- Use persisted queries
- Implement timeouts
