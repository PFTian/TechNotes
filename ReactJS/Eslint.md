# Setup ESLint, Prettier, commitlint, lint-staged and Husky v7 pre-commit and commit-msg git hooks for a ReactJS Project with VSCode Editor from scratch.

### **_Goal: In this tutorial, we will check code quality (`ESLint`) and code format (`Prettier`) for the changes of staged files (`lint-staged`) before we push a commit with conventional commit format (`commitlint`)._**

**ESLint -** [ESLint](https://eslint.org/docs/user-guide/getting-started) is a tool for identifying and reporting on patterns found in ECMAScript/Javascript code, with the goal of making code more consistent and avoiding bugs.

**Prettier -** [Prettier](https://prettier.io/docs/en/index.html) is an opinionated code formatter that ensures that all outputted code conforms to a consistent style.

**commitlint -** [commitlint](https://github.com/conventional-changelog/commitlint#what-is-commitlint) is a tool to check if your commit messages meet the [conventional commit format](https://www.conventionalcommits.org/en/v1.0.0/)

**lint-staged -** [lint-staged](https://github.com/okonet/lint-staged) is a tool that runs linters to make sure your staged file get formatted before commintting to your code base.

**Husky -** [Husky](https://typicode.github.io/husky/#/) is a tool that can help you easily to trigger the git hooks and run your scripts at each git hook stage.

[Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) that we will use in this tutorial

- **commit-msg** is a git hook that validates your project state or commit message before allowing a commit to go through.

- **pre-commit** is a git hook that is useful for identifying issues before code submission. It even run before you type a commit message.

## ESLint

ESLint is a javascript tool that can check your code for potential errors and bad code practices. It helps you enforce a code statndard and style guide in your codebase.

If you create your ReactJS app with [Create React App](https://reactjs.org/docs/create-a-new-react-app.html), the basic ESLint setup has been included in your project.

Nowadays the most popular style guides based on ESLint is [Airbnb Javascript Style Guide](https://github.com/airbnb/javascript). We will use Airbnb rules in this tutorial.

### Setup ESLint

Run the below command to install the ESLint to the project.

**npm:**

```bash
npm install eslint --save-dev
```

**yarn:**

```bash
yarn add eslint --dev
```

If you want to install ESLint globally cross all of your projects, you can install ESLint with

**npm:**

```bash
npm install -g eslint
```

**yarn:**

```bash
yarn add -g eslint
```

I personally prefer to use local eslint, So I will use local eslint for the rest of the tutorial.

### Config ESLint with Airbnb Package

Creating a ESLint configuration file with running:

```bash
npx eslint --init
```

You need to answer serveral questions about configuration files.

Here is my choices:

- **_How would you like to use ESLint?_**

  **_‣ To check syntax, find problems, and enforce code style_**

- **_What type of modules does your project use?_**

  **_‣ JavaScript modules (import/export)_**

- **_Which framework does your project use?_**

  **_‣ React_**

- **_Does your project use TypeScript?_**

  **_‣ No_** (Depends on which language you use in your project)

- **_Where does your code run?_**

  **_Browser_** (Choose Node if you are programing backend)

- **_How would you like to define a style for your project?_**

  **_▸ Use a popular style guide_**

- **_Which style guide do you want to follow?_**

  **_Airbnb: https://github.com/airbnb/javascript_**

- **_What format do you want your config file to be in?_**

  **_▸ JSON_**

Then the programe will check dependencies and ask if you want to install the dependencies.

```
The config that you've selected requires the following dependencies:

eslint-plugin-react@^7.21.5 eslint-config-airbnb@latest eslint@^5.16.0 || ^6.8.0 || ^7.2.0 eslint-plugin-import@^2.22.1 eslint-plugin-jsx-a11y@^6.4.1 eslint-plugin-react-hooks@^4 || ^3 || ^2.3.0 || ^1.7.0
```

Choose `yes` to install all the dependencies

- **_Would you like to install them now with npm?_**

  **_‣ Yes_**

Now after you install the ESLint with Airbnb package,

1. Open your VSCode,
2. Go to `Extensions` <img src="https://user-images.githubusercontent.com/10986601/124714671-9ec5f900-df34-11eb-8753-06bcab58f58d.png" width="25" height="25">,
3. Search `ESlint`,
4. Choose the one made by `Dirk Baeumer`,
5. Reopen your project with VSCode.

Open the VSCode terminal and click the `PROBLEMS` tab, you will see ESLint already checked your file's potential syntax problems based on the Airbnb rules that you installed with above steps.

![image](https://user-images.githubusercontent.com/10986601/124716273-7939ef00-df36-11eb-878f-00b72ee72999.png)

And there will be an `.eslintrc.json` file created at your project root folder.

```json
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["airbnb"],
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": 12,
    "sourceType": "module"
  },
  "plugins": [],
  "rules": {}
}
```

You can put your rules under the `rules` block.

### Normal Problems:

**`'React' must be in scope when using JSX`**

For React 17.x.x, based on [React Docs](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#eslint).

> If you are using `eslint-plugin-react`, the `react/jsx-uses-react `and `react/react-in-jsx-scope rules` are no longer necessary and can be turned off or removed.

So we can turn these two rules off at the `rules` block:

```json
{
  // ...
  "rules": {
    // ...
    "react/jsx-uses-react": "off",
    "react/react-in-jsx-scope": "off"
  }
}
```

**`JSX not allowed in files with extension '.js'`**

[Airbnb React/JSX Style Guide](https://github.com/airbnb/javascript/tree/master/react#naming) suggests to use `.jsx` extension for React components (eslint: react/jsx-filename-extension), you can rename your `.js` to `.jsx`. If you don't want to strictly use `.jsx` as extension of your files, you can turn the rules `off` or add the following rules in the `.eslintrc.json` file

```json
{
  // ...
  "rules": {
    // ...
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }]
  }
}
```

For these who use Typescript, your can add rules like

```json
{
  // ...
  "rules": {
    // ...
    "react/jsx-filename-extension": [
      "error",
      { "extensions": [".js", ".jsx", ".ts", ".tsx"] }
    ]
  }
}
```

For solving typescript common problems, please check [this tutorial](https://andrebnassis.medium.com/setting-eslint-on-a-react-typescript-project-2021-1190a43ffba).

### ESLint ignore file

`.eslintignore` is the file where we can add files and folders that will not be applied the ESLint rules.

For example, we can add `node_modules` into this file.

```
node_modules/**
```

### Run ESLint

#### Check specific file

```bash
npx eslint yourTargetFile.js
```

#### Fix errors automatically

```bash
npx eslint yourTargetFile.js --fix
```

#### Run ESLint with ignoring warnings

```bash
npx eslint yourTargetFile.js --quiet
```

## Prettier

We use `Prettier` to formate our code, cooperating with ESLinter. [This article](https://prettier.io/docs/en/comparison.html) is the comparition between `Prettier` and `Linters`. Based on [Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html), `Prettier` is for `code formatting concerns`, Linters are for `code-quality concerns`.

#### eslint-plugin-prettier

`eslint-plugin-prettier` allows you run the Prettier as ESLint rule and report issues that differ from Prettier's expected output as ESLint errors.

[This](https://stackoverflow.com/questions/44690308/whats-the-difference-between-prettier-eslint-eslint-plugin-prettier-and-eslint) is the comparison among `eslint-config-pretter`, `eslint-plugin-prettier` and `prettier-eslint`

#### eslint-config-prettier

Since ESLint also contains stylistic rules which might conflict with Prettier, we can turn off these rules that conflict or are unnecessary with Prettier by using `eslint-config-pretter`

### VSCode Prettier Plugin

Open your VSCode Extensions and search `Prettier`, install the `Prettier - Code formatter` made by `Prettier`.

### Prettier Installation

In your project folder, install the dependencies about Prettier.

```bash
npm install prettier eslint-config-prettier eslint-plugin-prettier --save-dev
```

### Config Prettier

Add `eslint-plugin-prettier` to `"prettier"` and `"rules"` of your `.eslintrc.json` file that you created from last section.

```json
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

This plugin ships with a `plugin:prettier/recommended` config that sets up both the `plugin` and `eslint-config-prettier` in one go.

Add `eslint-config-prettier` to `"extends"` of your `.eslintrc.json` file that you created from last section.

```json
{
  // ...
  "extends": ["airbnb", "plugin:prettier/recommended"]
}
```

Since airbnb rules could have conflit rules with Prettier, for example, Airbnb javascript style guide asks

> Always use double quotes (") for JSX attributes, but single quotes (') for all other JS.

However, Prettier uses [double quotes](https://prettier.io/docs/en/options.html#quotes) by default even though it doesn't suggest.

For solving this kind of conflits, We need to create a `.prettierrc` file under your project folder with code:

```json
{
  "semi": true,
  "trailingComma": "es5",
  "printWidth": 120,
  "singleQuote": true,
  "arrowParens": "always",
  "proseWrap": "preserve"
}
```

### Add Prettier ignore file

We can create `.prettierignore` file under the poject root folder to contain the files that we want to ignore prettier check.

```
node_modules/**
```

### Add Prettier as npm command

We can also format all of our code by using prettier with a command line.

Open your `package.json` and add the below code block to your `scripts`.

```json
{
  // ...
  "scripts": {
    // ...
    "format": "prettier --write \"**/*.{js,jsx,json,md}\""
  }
}
```

## commitlint

`commintlint` helps your team adhering to a commit convention. You can check [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) for more details.

### Install commitlint

```bash
npm install @commitlint/cli @commitlint/config-conventional --save-dev
```

### Create `commitlint.config.js` file

Create the `commitlint.config.js` file at your project root folder, add the below configuration to the file.

```js
module.exports = { extends: ['@commitlint/config-conventional'] };
```

### Test the configuration

We install the commitlint locally, so run

```bash
echo 'foo: bar' | node_modules/.bin/commitlint
```

We will get

```
⧗   input: foo: bar
✖   type must be one of [build, chore, ci, docs, feat, fix, perf, refactor, revert, style, test] [type-enum]

✖   found 1 problems, 0 warnings
ⓘ   Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint
```

## lint-staged

Since git hook will check all the files of the projects, this kind of checking will be time-consuming for a large project,. For solving this problem, we can install `lint-staged` package to only check the files that are only changed and staged.

### Install lint-staged

```bash
npm install lint-staged --save-dev
```

### Config lint-staged

Based on [lint-staged document](https://github.com/okonet/lint-staged), we can config lint-staged in `.lintstagedrc` file with `JSON` or `YAML` format.

Let's create a file called `.lintstagedrc.json` at the root directory and add the below lint-staged command in it. You can check `lint-staged document` to figure out what command that lint-staged supports.

```json
{
  "src/**/*.+(js|json|ts|tsx)": ["eslint"],
  "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": ["prettier --write"]
}
```

## Husky

[Husky](https://typicode.github.io/husky/#/?id=features) is a tool to easily trigger the git hooks and run our scripts at each stage of a commit.

### Install Husky

*When I write this article, the latest version of Husky is `7`*

Run the below command:

```bash
npm install husky --save-dev
```

*Optional: If you want to enable git hooks automatically after install, you can run:*

```bash
npm set-script prepare "husky install"
```

In the `pacakge.json` file, You should have:

```json
// package.json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

### Enable Git hooks

Run the below code to enable git hooks

```bash
npx husky install
```

There will be a `.husky/` folder created in your project, where you can put your git hooks scripts.

For example, run

```bash
npx husky add .husky/pre-commit "npm test"
```

This command will create a `pre-commit` file in the `.husky` folder with content `npm test`. Each time when you push a new commit, it will the test at first, if the test failed, the whole commit will be automatically aborted.

### Support commitlint with commit-msg git hook

Add `commit-msg` file at your root directory with the below command:

```bash
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

`commit-msg` is a git hook that can validate your project state or commit message before allowing a commit to go through.

Now if you commit your code again, you will find you can only commit your changes with following the conventional commit message format.

### Support lint-staged with pre-commit git hook

Add `pre-commit` file at your root directory with the below command:

```bash
npx husky add .husky/pre-commit 'npm run lint-staged'
```

Open your `package.json` file, add the below code to the `scripts`:

```json
{
  // ...
  "scripts:" {
    // ...
    "lint-staged": "lint-staged",
  }
}
```

Now go to `.husky/pre-commit` file, add the below code to the file

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run lint-staged
```

## Conclusion

Now if you commit the changes of your project again,

1. `ESLint` and `Prettier`
   will check your code quality and format of your changed files at `pre-commit` hook stage.

2. The tool `commitlint` will check if your commit message follow the `Conventional Commit Format` at `commit-msg` stage.

*NOTE: pre-commit will be trigger at first and then commitlint get triggered*

Either of above steps failed, the whole commit will be aborted, which means you won't commit your dirty code to the repository any more.
