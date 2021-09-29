# Graphql Code generator <-> 8base configuration

We use [Graphql code generator](https://www.graphql-code-generator.com/docs/getting-started/index) 
which is a tools that read the graphql schema and generate a file with all the types
for tables, queries and mutations

### How to use?



To use this library, following these instructions:

1. Install this library, and GraphQL Codegen and the relevant plugins:

```
npm i -D @graphql-typed-document-node/core @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typed-document-node
```

And if you don't already have a dependency for `graphql`, add it to your project:

```
npm i graphql
```

> Codegen is needed because we need to precompile `.graphql` files into `DocumentNode`, and burns the types in it to create `TypedDocumentNode` object.
2. Create GraphQL-Codegen configuration file, and point to your GraphQL schema and your `.graphql` operations files:

```yml
schema: SCHEMA_FILE_OR_ENDPOINT_HERE
documents: "./src/**/*.graphql"
generates:
  src/shared/types/generated.ts:
    plugins:
      - typescript
      - typescript-operations
      - typed-document-node
    config:
      avoidOptionals:
        field: true
      maybeValue: T
```

