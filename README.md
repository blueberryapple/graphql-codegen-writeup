# graphql-codegen-writeup

## Concept

This document will inform users how to use the [GraphQL code generator by The Guild.](https://the-guild.dev/graphql/codegen)

## Documentation

The GraphQL code generator allows users to be able to generate TypeScript type definitions and SDKs based on their GraphQL schema.

### How-To Guide

To get started please install the npm packages:

```sh
npm i -S graphql
npm i -D typescript ts-node @graphql-codegen/cli @graphql-codegen/client-preset
```

In order to generate code using your GraphQL schema you must first define your `config.ts` file to inform the code generator where your schema file is located:

```ts
import { CodegenConfig } from "@graphql-codegen/cli";

const config: CodegenConfig = {
  schema: "https://swapi-graphql.netlify.app/.netlify/functions/index",
  documents: ["src/**/*.tsx"],
  ignoreNoDocuments: true, // for better experience with the watcher
  generates: {
    "./src/gql/": {
      preset: "client",
    },
  },
};

export default config;
```

Once your code generator is configured, you may now run:

```sh
npm run graphql-codegen
```

Assuming you had a GraphQL query defined:

```ts
const allFilmsWithVariablesQueryDocument = graphql(/* GraphQL */ `
  query allFilmsWithVariablesQuery($first: Int!) {
    allFilms(first: $first) {
      edges {
        node {
          ...FilmItem
        }
      }
    }
  }
`);
```

you will now have a React Query hook generated for you that is fully type safe:

```ts
const { data } = useQuery(["films"], async () =>
  request(
    "https://swapi-graphql.netlify.app/.netlify/functions/index",
    allFilmsWithVariablesQueryDocument,
    {
      first: 10, // variables are typed too!
    }
  )
);
```

### Tutorial

WIP

### Reference

CLI options

| Option          | Shorthand | Usage                                                                                                       |
| --------------- | --------- | ----------------------------------------------------------------------------------------------------------- |
| `--config`      | `-c`      | Specifies the codegen config file to use.                                                                   |
| `-watch`        | `-w`      | Overrides the `watch` config to true. You can also specify a glob expression to create a custom watch list. |
| `--silent`      | `-s`      | Overrides the `silent` config to true.                                                                      |
| `--verbose`     | `-v`      | - Overrides the verbose config to true.                                                                     |
| `--debug`       | `-d`      | - Overrides the debug config to true.                                                                       |
| `--errors-only` | `-e`      | - Overrides the errorsOnly config to true.                                                                  |
| `--require`     | `-r`      | - Specifies require.extensions before loading the .yml file.                                                |
| `--overwrite`   | `-o`      | - Overrides the overwrite config to true.                                                                   |
| `--profile`     |           | Use the profiler to measure performance. (see "Profiler" in "Advanced Usage")                               |
| `--project`     | `-p`      | - To generate only one project out of a Multi Project config file.                                          |
| `--check`       |           | - Enable dry-run mode (see below)                                                                           |

### Explanation

The code generator makes use of a high configurable plugin based system to be able to generate code. Using the abstract syntax tree, authors are able to read and write code files based on specified GraphQL schemas.
