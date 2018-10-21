# GraphQL

Reading the entire [graphql guide](https://graphql.org/learn/queries/) is reccomended.

## Essentials

However, the following are the most important parts.

- [Fields](https://graphql.org/learn/queries/#fields)
- [Arguments](https://graphql.org/learn/queries/#arguments)
- [Variables](https://graphql.org/learn/queries/#variables)
- [Mutations](https://graphql.org/learn/queries/#mutations)
- [All of schema](https://graphql.org/learn/schema/)
- [Execution](https://graphql.org/learn/execution/)
- [graphql-tools](https://www.apollographql.com/docs/graphql-tools/)
- [Generating a schema](https://www.apollographql.com/docs/graphql-tools/generate-schema.html)
- [Resolvers](https://www.apollographql.com/docs/graphql-tools/resolvers.html)

## Recommended

- [Thinking in graphs](https://graphql.org/learn/thinking-in-graphs/)
- [Authorization](https://graphql.org/learn/authorization/)
- [Pagination](https://graphql.org/learn/pagination/)

Good example of a CRUD api:

- [Prisma Queries](https://www.prisma.io/docs/reference/prisma-api/queries-ahwee4zaey/)
- [Prisma Mutations](https://www.prisma.io/docs/reference/prisma-api/mutations-ol0yuoz6go)

## Further reading

- [GitHub Api](https://developer.github.com/v4/)
- [Clothespin api](https://github.com/hsadev/clothespin-api)
- [Tamm api](https://github.com/hsadev/tamm-api)
- [TodoApp + discussion on architecture](https://github.com/hsadev/todoapp/)
- [GraphQL APIs](https://github.com/APIs-guru/graphql-apis)

## More resources

[Awesome graphql](https://github.com/chentsulin/awesome-graphql)

## Guidelines

### Folder organization

There are two possible types of folder organization:

Unit Organization:

```
/graphql/
        users/
              Query.js
              User.js
              Mutation.js
              typedef.js
        posts/
              Query.js
              Post.js
              Mutation.js
              typedef.js
        comments/
                Query.js
                Comment.js
                Mutation.js
                typedef.js
```

Functional organization:

```
/graphql/
        query/
              Query.js
              User.js
              Post.js
              Comment.js
        mutation/
              Mutation.js
              userMutations.js
              postMutations.js
              commentMutations.js
```

Which is better depends on the app. Just make sure you think about it.
