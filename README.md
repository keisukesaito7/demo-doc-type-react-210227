# demo-doc-type-react-210227

## 0. Motivation

- Docker で React&TypeScript の環境を構築したい
- ESlint と Prettier の設定も入れられるといいな

## 1. ゼロから始める環境構築

### ~ docker-compose build

```
$ mkdir demo-doc-type-react-210227
$ cd demo-doc-type-react-210227
$ vim Dockerfile          // Dockerfileを記述
$ vim docker-compose.yml  // docker-compose.ymlを記述
$ docker-compose build
```

### create-react-app

```
$ docker-compose run --rm frontend sh -c 'npx create-react-app frontend --template typescript'
```

### アプリ起動（Control + C で終了）

```
$ docker-compose up
```

## 2. GitHub から clone する環境構築

### git clone

```
$ git clone https://github.com/keisukesaito7/demo-doc-type-react-210227.git
```

### 起動まで

```
$ docker-compose build
$ docker-compose run --rm frontend sh -c 'cd frontend && yarn install'
$ docker-compose up
```

## 3. ESLint

### ESLint のバージョン確認

create-react-app でアプリを作ると ESLint のパッケージがが入っている。
ESLint のバージョンは下のコマンドで確認できる。

```
$ docker-compose run --rm frontend sh -c 'cd frontend && npm ls eslint'
```

### TypeScript のバージョンを最新に

React17.0 から導入された JSX の新しい変換形式が TypeScript の構文チェックを通るようになったため。

```
$ docker-compose run --rm frontend sh -c 'cd frontend && yarn upgrade typescript@latest'
```

### ESLint の設定ファイルを作る

```
$ dcr --rm frontend sh -c 'cd frontend && yarn eslint --init'
? How would you like to use ESLint?
》To check syntax, find problems, and enforce code style

? What type of modules does your project use? JavaScript modules (import/export)
》JavaScript modules (import/export)

? Which framework does your project use?
》React

? Does your project use TypeScript?
》Yes

? Where does your code run?
》Browser

? How would you like to define a style for your project?
》Use a popular style guide

? Which style guide do you want to follow?
》Airbnb: https://github.com/airbnb/javascript

? What format do you want your config file to be in?
》JavaScript

The config that you've selected requires the following dependencies:

eslint-plugin-react@^VERSION @typescript-eslint/eslint-plugin@latest eslint-config-airbnb@latest eslint@^VERSION eslint-plugin-import@^VERSION eslint-plugin-jsx-a11y@^VERSION eslint-plugin-react-hooks@^VERSION @typescript-eslint/parser@latest
? Would you like to install them now with npm?
》No
```

※エラーで終わることがあるが、気にしなくて OK（本当か？？）

```
error Command failed with exit code 2.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

### .eslintrc.js で使ってるプラグインを手動でインストール

```
$ docker-compose run --rm frontend sh -c 'cd frontend && yarn add -D \
eslint-plugin-react \
@typescript-eslint/eslint-plugin \
eslint-config-airbnb \
eslint-plugin-import \
eslint-plugin-jsx-a11y \
eslint-plugin-react-hooks \
@typescript-eslint/parser'
```

↓ 一行で書くとこんな感じ（コピペするならこっち）

```
$ docker-compose run --rm frontend sh -c 'cd frontend && yarn add -D eslint-plugin-react @typescript-eslint/eslint-plugin eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react-hooks @typescript-eslint/parser'
```

### .eslintrc.js の編集

読み込んだプラグインの設定を書かないと反映されません。
なので設定します。

```
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    "plugin:react/recommended",
    "airbnb",
    "airbnb/hooks",
    "plugin:import/errors",
    "plugin:import/warnings",
    "plugin:import/typescript",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/recommended-requiring-type-checking",
  ],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    project: "./tsconfig.eslint.json",
    sourceType: "module",
    tsconfigRootDir: __dirname,
  },
  plugins: ["react", "import", "jsx-a11y", "@typescript-eslint", "react-hooks"],
  root: true,
  rules: {
    'no-use-before-define': 'off',
    '@typescript-eslint/no-use-before-define': [
      'error',
    ],
    'lines-between-class-members': [
      'error',
      'always',
      {
        exceptAfterSingleLine: true
      }
    ],
    'no-void': [
      'error',
      {
        allowAsStatement: true,
      },
    ],
    'padding-line-between-statements': [
      'error',
      {
        blankLine: 'always',
        prev: '*',
        next: 'return',
      },
    ],
    '@typescript-eslint/no-unused-vars': [
      'error',
      {
        'vars': 'all',
        'args': 'after-used',
        'argsIgnorePattern': '_',
        'ignoreRestSiblings': false,
        'varsIgnorePattern': '_',
      },
    ],
    'import/extensions': [
      'error',
      'ignorePackages',
      {
        js: 'never',
        jsx: 'never',
        ts: 'never',
        tsx: 'never',
      }
    ],
    'react/jsx-filename-extension': [
      'error',
      {
        extensions: ['.jsx', '.tsx']
      }
    ],
    'react/jsx-props-no-spreading': [
      'error',
      {
        html: 'enforce',
        custom: 'enforce',
        explicitSpread: 'ignore',“},
    ],
    'react/react-in-jsx-scope': 'off',
  },
  overrides: [
    {
      'files': ['*.tsx'],
      'rules': {
        'react/prop-types': 'off',
      },
    },
  ],
  settings: {
    'import/resolver': {
      node: {
        paths: ['src'],
      },
    },
  },
};

```

### tsconfig.eslint.json でチェックするファイルを限定

```
{
  "extends": "./tsconfig.json",
  "include": ["src/**/*.js", "src/**/*.jsx", "src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules"]
}
```

### .eslintignore

```
build/
public/
**/coverage/
**/node_modules/
**/*.min.js
*.config.js
.*lintrc.js
```

### VSCode の設定

```
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
},
"editor.formatOnSave": false,
"eslint.packageManager": "yarn",
"typescript.enablePromptUseWorkspaceTsdk": true,"
```

### yarn lint で linter が走るように

```
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "lint": "eslint 'src/**/*.{js,jsx,ts,tsx}'",
  "lint:fix": "eslint --fix 'src/**/*.{js,jsx,ts,tsx}'",
  "postinstall": "typesync"
},
```
