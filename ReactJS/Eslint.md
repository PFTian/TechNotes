# Setup ESLint, Prettier, commitlint, pre-commit hooks with Husky v6 and lint-staged for a ReactJS Project with VSCode from scratch.


__ESLint -__ [ESLint](https://eslint.org/docs/user-guide/getting-started) is a tool for identifying and reporting on patterns found in ECMAScript/Javascript code, with the goal of making code more consistent and avoiding bugs.

__Prettier -__ [Prettier](https://prettier.io/docs/en/index.html) is an opinionated code formatter that ensures that all outputted code conforms to a consistent style.

__commitlint -__ [commitlint](https://github.com/conventional-changelog/commitlint#what-is-commitlint) is a tool to check if your commit messages meet the [conventional commit format](https://www.conventionalcommits.org/en/v1.0.0/)

__Pre-commit -__ [Pre-commit](https://pre-commit.com/) is a git hook that is useful for identifying issues before code submission. We use pre-commit here to check code format with `ESLint` and `Prettier` rules that we set in the configuration file.

__lint-staged -__ [lint-staged](https://github.com/okonet/lint-staged) is a tool that running linters makes your staged file formatted before commintting to your code base.

## ESLint

ESLint is a javascript tool that can check your code for potential errors and bad code practices. It helps you enforce a code statndard and style guide in your codebase.

If you create your ReactJS app with [Create React App](https://reactjs.org/docs/create-a-new-react-app.html), the basic ESLint setup has been included in your project.

Nowadays the most popular style guides based on ESLint is [Airbnb Javascript Style Guide](https://github.com/airbnb/javascript). We will use Airbnb rules in this tutorial.

### Setup ESLint

Run the below command to install the ESLint to the project.

__npm:__
```bash
npm install eslint --save-dev
```
__yarn:__

```bash
yarn add eslint --dev
```

If you want to install ESLint globally cross all of your projects, you can install ESLint with

__npm:__
```bash
npm install -g eslint
```

__yarn:__
```bash
yarn add -g eslint
```

I personally prefer to use local eslint, So I will use local eslint for the rest of the tutorial.

### Config ESLint with Airbnb package

Creating a ESLint configuration file with running:

```bash
npx eslint --init
```

You need to answer serveral questions about configuration files.

Here is my choices:

* ___How would you like to use ESLint?___

  ___‣ To check syntax, find problems, and enforce code style___

* ___What type of modules does your project use?___
  
  ___‣ JavaScript modules (import/export)___

* ___Which framework does your project use?___

  ___‣ React___

* ___Does your project use TypeScript?___

  ___‣ No___ (Depends on which language you use in your project)

* ___Where does your code run?___
  
  ___Browser___ (Choose Node if you are programing backend)

* ___How would you like to define a style for your project?___

  ___▸ Use a popular style guide___

* ___Which style guide do you want to follow?___

  ___Airbnb: https://github.com/airbnb/javascript___

* ___What format do you want your config file to be in?___

  ___▸ JSON___

Then the programe will check dependencies and ask if you want to install the dependencies.

```
The config that you've selected requires the following dependencies:

eslint-plugin-react@^7.21.5 eslint-config-airbnb@latest eslint@^5.16.0 || ^6.8.0 || ^7.2.0 eslint-plugin-import@^2.22.1 eslint-plugin-jsx-a11y@^6.4.1 eslint-plugin-react-hooks@^4 || ^3 || ^2.3.0 || ^1.7.0
```
Choose `yes` to install all the dependencies
* ___Would you like to install them now with npm?___

  ___‣ Yes___

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
    "extends": [
        "airbnb",
        "plugin:react/recommended"
    ],
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 12,
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
    }
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
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx", ".ts", ".tsx"] }]
  }
}
```

### ESLint ignore file

`.eslintignore` is the file where we can add files and folders that will not be applied the ESLint rules.

For example, we can add `node_modules` into this file.

```
node_modules/**
```
