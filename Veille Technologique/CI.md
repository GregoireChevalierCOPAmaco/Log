ci.yml
```
name: CI Pipeline

on:
  push:
    branches:
      - develop
      - main
  pull_request:
    branches:
      - develop
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [20.x]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies backend
        run: npm install --legacy-peer-deps
        working-directory: ./Esl_soft-back


      - name: Install dependencies frontend
        run: npm install --legacy-peer-deps
        working-directory: ./Esl_soft-front


      - name: Install Angular CLI
        run: npm install -g @angular/cli

      - name: Install ESLint globally
        run: npm install -g eslint

      - name: Linting NestJS project
        run: npm run lint
        working-directory: ./Esl_soft-back

      - name: Linting Angular project
        run: npm run lint
        working-directory: ./Esl_soft-front

      - name: Build NestJS project
        run: npm run build
        working-directory: ./Esl_soft-back



      - name: Build Angular project
        run: npm run build
        working-directory: ./Esl_soft-front

      - name: Run backend tests
        run: npm run test
        working-directory: ./Esl_soft-back

      - name: Run frontend tests
        run: npm run test
        working-directory: ./Esl_soft-front
```

eslint.config.mjs

```
import globals from "globals";
import tseslint from "typescript-eslint";


export default [
  {files: ["**/*.ts"], languageOptions: {sourceType: "script"}},
  {languageOptions: { globals: globals.browser }},

  ...tseslint.configs.recommended,
  {
    rules:{
        "no-console": "off",
      '@next/next/no-html-link-for-pages': 'off',
      '@typescript-eslint/no-explicit-any': 'off',
      '@typescript-eslint/naming-convention': [
        'error',
        {
          selector: 'variable',
          format: ['camelCase'],
        },
          ],

    }
  }
];
```