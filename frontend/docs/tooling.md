# Tooling

## Overview

We're committed to the quality of our development stack and tooling. This document contains guides on the tools we use for daily development as a frontend engineer. This also contains our unified ESLint config that you can use and extend from.

## Table of Contents

- [IDE](#ide)
- [TypeScript](#typescript)
- [Linting and Formatting](#linting-and-formatting)
- [Tests](#tests)

---

## IDE

Use [Visual Studio Code](https://code.visualstudio.com). It has first-class support for JavaScript and TypeScript, complete with code-aware statement completion, Emmet integration for JSX elements, as well top-notch debugging environment.

## TypeScript

Below are guides on how we use TypeScript projects.

### Prefer using Babel TS preset on frontend projects

The TypeScript compiler supports most modern ES2015+ JavaScript features up to stage-3 proposals. However, in some cases, chaining TypeScript with Babel, or using the Babel transpiler altogether allows us to use features like hot reloading, additional Babel plugins for e.g. `styled-components`, and a faster compile time.

CRA 2.1 already uses Babel as the default TypeScript transpilation pipeline, so everything's already handled for you.

Note that the Babel compiler **only** removes the types from your code. **It does not do any additional type-checking!** If compiling TS using Babel, make sure to run type-checking separately with `tsc --noEmit`. It's also better to include in your CI process!

### Use `--strict` mode

Make sure to enable strict mode in your tsconfig! Anything less will cause a huge mess in our codebase further down the road.

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

If you do need to disable some rules that the `--strict` flag emits, override it after the `strict` rule in your tsconfig:

```json
{
  "compilerOptions": {
    "strict": true,
    "strictBindCallApply": false
  }
}
```

## Linting and Formatting

### ESLint

we provides a ESLint config to enforce the styleguides outlined in this document. To use it, install some dependencies.

```sh
$ yarn add --dev @typescript-eslint/eslint-plugin, eslint, eslint-config-airbnb, eslint-config-next, eslint-config-prettier, eslint-plugin-import, eslint-plugin-node, eslint-plugin-prettier, eslint-plugin-promise, eslint-plugin-react, eslint-plugin-react-hooks, eslint-plugin-test-selectors,
```

Then, extend installed dependencies in your `.eslintrc` file.

```json
{
  "extends": [
    "airbnb",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "prettier",
    "plugin:prettier/recommended",
    "plugin:react-hooks/recommended",
    "plugin:test-selectors/recommended",
    "next"
  ],
}
```

### Prettier

[Prettier](https://prettier.io/) is a tool to automatically format your code during save. It supports various editors, from VSCode, Atom, Sublime, and even Emacs.

To use this ESLint config in conjunction with Prettier, copy the following `.prettierrc` template:

```json
{
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "jsxBracketSameLine": false,
  "arrowParens": "avoid",
  "endOfLine": "auto"
}
```

Then install the Prettier eslint config and plugin:

```sh
$ yarn add --dev eslint-plugin-prettier eslint-config-prettier prettier
```

And finally, include them as follows.

```json
{
  "plugins": ["@typescript-eslint", "test-selectors", "xms-reseller-web", "prettier"],
  "extends": [
    "airbnb",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "prettier",
    "plugin:prettier/recommended",
    "plugin:react-hooks/recommended",
    "plugin:test-selectors/recommended",
    "next"
  ],
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "globals": {
    "__DEV__": true
  },
  "rules": {
    "multiline-ternary": "off",
    "no-return-await": "off",
    "space-before-function-paren": "off",
    "@typescript-eslint/space-before-function-paren": "off",
    "@typescript-eslint/no-floating-promises": "off",
    "@typescript-eslint/indent": "off",
    "test-selectors/anchor": "off",
    "dot-notation": "off",
    "jsx-a11y/anchor-is-valid": "off",
    "jsx-a11y/label-has-associated-control": "off",
    "react/jsx-closing-tag-location": "off",
    "react/jsx-curly-brace-presence": "off",
    "react/jsx-filename-extension": [
      "error",
      {
        "extensions": [".jsx", ".tsx"]
      }
    ],
    "react/jsx-one-expression-per-line": "off",
    "react/jsx-props-no-spreading": "off",
    "react/jsx-wrap-multilines": "off",
    // Cannot use `Component.displayName` pattern on class components in TS.
    "react/static-property-placement": "off",
    // Require or disallow a space immediately following the // or /* in a comment
    // https://eslint.org/docs/rules/spaced-comment
    "spaced-comment": [
      "error",
      "always",
      {
        "line": {
          "exceptions": ["-", "+"],
          "markers": ["=", "!", "#region", "#endregion", "/"] // space here to support sprockets directives and typescript reference comments
        },
        "block": {
          "exceptions": ["-", "+"],
          "markers": ["=", "!", "#region", "#endregion", ":", "::"],
          // space here to support sprockets directives and flow comment types
          "balanced": true
        }
      }
    ]
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".mjs", ".jsx", ".ts", ".tsx", ".json"]
      },
      "typescript": {}
    },
    "import/extensions": [".js", ".mjs", ".jsx", ".ts", ".tsx"]
  },
  "overrides": [
    {
      "files": ["**/*.ts", "**/*.tsx"],
      "parserOptions": {
        // typescript-eslint specific options
        "warnOnUnsupportedTypeScriptVersion": true
      },
      "rules": {
        // Allow unused variables that starts with, or is `_`
        "no-unused-vars": "off",
        "@typescript-eslint/no-unused-vars": [
          "warn",
          {
            "vars": "all",
            "args": "after-used",
            "ignoreRestSiblings": true,
            "argsIgnorePattern": "^_",
            "varsIgnorePattern": "^_"
          }
        ],
        // Override the base rules so to not clash with each other.
        // https://github.com/typescript-eslint/typescript-eslint/issues/2540
        "no-use-before-define": "off",
        "@typescript-eslint/no-use-before-define": [
          "error",
          {
            "functions": true,
            "classes": true,
            "variables": true
          }
        ],
        // Allow `.ts` and `.tsx` extensions to be omitted
        "import/extensions": [
          "error",
          "ignorePackages",
          {
            "js": "never",
            "mjs": "never",
            "jsx": "never",
            "ts": "never",
            "tsx": "never"
          }
        ],
        // Extend this config for TypeScript files
        "import/no-extraneous-dependencies": [
          "error",
          {
            "devDependencies": [
              "test/**",
              // tape, common npm pattern
              "tests/**",
              // also common npm pattern
              "spec/**",
              // mocha, rspec-like pattern
              "**/jest.config.ts",
              // jest config
              "**/jest.setup.ts",
              // jest setup
              "**/__tests__/**",
              // jest pattern
              "**/__mocks__/**",
              // jest pattern
              "test.{ts,tsx}",
              // repos with a single test file
              "test-*.{ts,tsx}",
              // repos with multiple top-level test files
              "**/*{.,_}{test,spec}.{ts,tsx}"
              // tests where the extension or filename suffix denotes that it is a test
            ],
            "optionalDependencies": false
          }
        ],
        // No need to use prop types on TS files.
        "react/prop-types": "off",
        "@typescript-eslint/camelcase": "off",
        "@typescript-eslint/explicit-module-boundary-types": ["off"]
      }
    },
    {
      "files": [".eslintrc.js", "*.config.js"],
      "parserOptions": {
        "sourceType": "script"
      },
      "env": {
        "node": true
      }
    }
  ],
  "ignorePatterns": ["next.config.js", "next-env.d.ts", ".eslint/rules/**", "**/__tests__/**"]
}
```

## Tests

Write tests. Please.

Writing tests ensures that:

- We could add new functionality without breaking old ones
- We could refactor parts of the codebase without fearing that stuff would break.

We use [Jest](https://jestjs.io/) as our test runner. To run test coverage within Jest, run `jest --coverage`. New projects must aim for at least 70% test coverage. Don't forget to use code coverage/code quality tracking tools like [codecov](https://codecov.io/) or [CodeClimate](https://codeclimate.com/).

We use [react-testing-library](https://testing-library.com/docs/react-testing-library/intro) as our React testing framework. This library helps us write better tests for our React components by encouraging good test practices.
