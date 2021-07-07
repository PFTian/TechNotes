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

```
yarn add eslint --dev
```

If you want to install ESLint globally cross all of your projects, you can install ESLint with

__npm:__
```
npm install -g eslint
```

__yarn:__
```
yarn add -g eslint
```

I personally prefer to use local eslint, So I will use local eslint for the rest of the tutorial.

### Config ESLint with Airbnb package


