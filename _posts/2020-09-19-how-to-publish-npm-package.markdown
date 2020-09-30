---
layout: post
title:  "Publish a Javascript package to npm using git, babel, watch and jest on macos"
date:   2020-09-19 12:05:50 +0530
categories: jekyll update
---

## git

1. Create the git project on github.com using the web interface. Choose a project name that is available on git as well as npm.

1. cd to the directory where you want your development folder to be

1. Clone the git repo you just created to your local machine

```shell
git clone < git url >
```

1. Create your project directory and Javascript code file.

```shell
mkdir src
cd src
touch index.js
```
Write Javascript code in index.js This may be a small function related to your intended library or a placeholder function as shown below. Export your api using the CommonJS approach used by npm

```javascript
function sayHello(str){
  return "hello" + (str ? " " + str : "")
}

module.exports = {sayHello};
```

## npm

1. Move back to your main project directory and initialise npm 

```shell
cd ..
npm init
```

Accept all npm defaults except entry point which should be set to build/index.js and version number which should be set to 0.1.0 

## babel

Note: Babel is useful even in packages intended for npm due to the compatibility issues of various node versions with modern Javascript features. However, if you are sure that you do not want to use babel, skip this section and directly use the src folder instead of build. Also update the entry point in package.json mentioned above to src/index.js

1. Install babel and babel presets for basic transformations 

```shell
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

Configure the babel presets to run by adding the babel key to your package.json

```json
...
  "babel": {
    "presets": [
      [
        "@babel/preset-env"
      ]
    ]
  }
...
```

1. Set up babel to read your Javascript file from the src directory and write the transformed file into the build directory. In the package.json file, make the following entry inside the existing "scripts" object

```json
...
"scripts": {
  "build": "babel src -d build",
...
},
...
```

1. Run the transformation

```shell
npm run build
```

Check the transformed file in build/index.js This is the file that will be published to npm

## local test

1. Test your code locally using the node repl

```shell
node

> var pkg = require('./build/index.js')
> pkg.sayHello("world")
'hello world'
```

## unit testing

1. Install the unit testing framework, jest

```shell
npm install --save-dev jest
```

In the package.json file, replace the existing entry for "test" inside the "scripts" object

```json
...
"scripts": {
  "build": "babel src -d build",
  "test": "jest build"
},
...
```

1. Create the test file in the src directory

```shell
cd src
touch index.test.js
```

1. Write Unit test cases in the following format

```javascript
const pkg = require('./index.js')

test("test if the function says hello", () => (
    expect(pkg.sayHello("world")).toBe("hello world")
  ))
```

1. Run build and then run the test process and check if the test case passes

```shell
cd ..
npm run build
npm test
```

## watch

1. Watch your code directory and automatically run build and test when changes are made

```shell
npm install --save-dev watch
```

In the package.json file, make the "dev" entry inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
  "test": "jest build"
},
...
```

Open a separate terminal tab and run the dev process

```shell
npm run dev
```

Make a change to your code file and check if it is detected and build runs automatically

1. Watch the build directory and run tests automatically when it changes. In the package.json file, make the "test:watch" entry inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
  "test": "jest build",
  "test:watch": "npm test -- --watch"
},
...
```
Open a separate terminal tab and run the test:watch process

```shell
npm run test:watch
```

Make changes to your code file and check if it is detected and build and test runs automatically

## ghooks

1. Configure ghooks to run your tests automatically before git commit. Install ghooks

```shell
npm install --save-dev ghooks
```

1. Add config to package.json so that the tests run before commit

```json
...
"config": {
    "ghooks": {
      "pre-commit": "npm run test"
    }
  }
...
```

## git
1. Create a README.md file with instructions on how to install and use your code. Initially you could just mention that the project is work in progress.

```shell
touch README.md
```

```markdown
  # My Pkg name

  ~```shell
  $ npm install pkg 
  ~```

  ~```javascript
  const pkg = require('pkg')

  //ES2015 modules
  import pkg from 'pkg'
  console.log(pkg.sayHello("world"))
  //"hello world"

  ~```

```
Note: remove the tildas ~ in the block above


1. Commit your files and update github. First create a .gitignore file to suppress unnecessary files from enering into the repository.

```shell
echo -e "node_modules\nbuild" >> .gitignore
git add -A
git commit -m "Initial commit"
git push
```

## dev
1. Develop your required functionality by updating index.js and simultaneously add test cases to index.test.js Keep commiting to git Move to the next step once you are satisfied with the functionalty and want to make it available on npm. Also update the README file.

## link

1. Use your package locally before publishing it to npm.

Create a global reference to your package

```shell
npm link
```

Create a new folder where you can test your package

```shell
cd ..
mkdir test_< pkg name >
cd test_< pkg name >
npm init -y
npm link < pkg name >
touch index.js
```

Import and use your package in index.js Check that it works correctly by running it through node

```shell
node index.js
```

## npm publish

1. Create a .npmignore file to prevent your source code from being published

```shell
echo "src" >> .npmignore
```

1. Install the np package to publish to npm. You could also simply use "npm publish" but the np package carries out several checks which avoids mistakes.

```shell
npm install --save-dev np
```

1. Update package.json to enable publishing. In the package.json file, make the "prepublish" and "release" entries inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
  "test": "jest build",
  "test:watch": "npm test -- --watch",
  "prepublish": "npm run build",
  "release": "np"
},
...
```

1. Commit your files and update github. This is necessary because np will not publish unless your git is in order. Also tag your git commit with the version number so that you know exactly which git version was published to npm.

```shell
git add -A
git commit -m "Final changes"
git push
git tag 1.0.0
git push --tags
```

1. Create an account on the npmjs.com website if you don't have one already and then publish your package

```shell
npm adduser
npm run release
```

1. Check that the package is published on npmjs.com and using the below command

```shell
npm info < pkg >
```

1. If you want to unpublish your package

```shell
npm unpublish < pkg > -f
```

## final test

1. Download your package from the npm repository and use it. Follow the same steps given in the link section above. No need to run "npm link" and instead of "npm link < pkg name >" run "npm install < pkg name >"

## making changes

1. Make the changes to the code files.
1. Update the version number in package.json file based on the semantic versioning rules

```json
...
{
  ...
  "version": "0.2.0",
}
...
```

1. See the changes using git

```shell
git diff
```

1. Update git and then publish to npm

```shell
git add -A
git commit -m "Updates"
git push
git tag 1.1.0
git push --tags
npm adduser
npm run release
```

## to be added
1. CI using Travis
1. Code coverage (jest)
1. copy files
1. browser build

## Issues
1. Should the test file be removed from build?
