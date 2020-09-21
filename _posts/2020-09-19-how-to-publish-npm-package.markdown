---
layout: post
title:  "Publish a Javascript package to npm using git, babel, watch and jest on macos"
date:   2020-09-19 12:05:50 +0530
categories: jekyll update
---

## git

1. Create the git project on github.com using the web interface. Choose a project name that is available on git as well as npm.

2. cd to the directory where you want your development folder to be

3. Clone the git repo you just created to your local machine

```shell
git clone < git url >
```

4. Create your project directory and Javascript code file

```shell
mkdir src
cd src
touch index.js
```
Write your Javascript code in index.js

## npm

4. Move back to your main project directory and initialise npm 

```shell
cd ..
npm init
```

Accept all npm defaults except entry point which should be set to build/index.js

## babel

5. Install babel and babel presets for basic transformations 

```shell
npm install --save-dev @babel/cli @babel/core @babel/preset-env
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
  },
...
```

6. Set up babel to read your Javascript file from the src directory and write the transformed file into the build directory. In the package.json file, make the following entry inside the existing "scripts" object

```json
...
"scripts": {
  "build": "babel src -d build",  
...
},
...
```

7. Run the transformation

```shell
npm run build
```

Check the transformed file in build/index.js This is the file that will be published to npm

## watch

8. Watch your code directory and automatically run build when changes are made

```shell
npm install --save-dev watch
```

In the package.json file, make the "dev" entry inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
...
},
...
```

Open a separate terminal tab and run the dev process

```shell
npm run dev
```

Make changes to your code file and check if it is detected and build runs automatically

## jest

9. Install unit testing framework jest

```shell
npm install --save-dev jest
```

In the package.json file, replace the existing entry for "test" inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
  "test": "jest build",
},
...
```

10. Create the test file in the src directory

```shell
cd src
touch index.test.js
```

11. Write Unit test cases in the following format

```javascript
import pkg from '.'

test("test case description", () => (
		expect(fnCall()).toBe("expected value")
	))
```

12. Run the test process and check if the test cases pass

```shell
npm test
```

13. Watch the build directory and run tests automatically when it changes. In the package.json file, make the "test:watch" entry inside the "scripts" object

```json
...
"scripts": {
  "dev": "watch 'npm run build' src",
  "build": "babel src -d build",
  "test": "jest build",
  "test:watch": "npm test -- --watch",
},
...
```
Open a separate terminal tab and run the test:watch process

```shell
npm run test:watch
```

Make changes to your code file and check if it is detected and build and test runs automatically

## link

14. Use your package locally before publishing it to npm.

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

Import and use your package in index.js and check that it works correctly

## npm publish

15. Create a .npmignore file to prevent your source code from being published

```shell
echo "src" >> .npmignore
```

16. Install the np package to publish to npm

```shell
npm install --save-dev np
```

17. Update package.json to enable publishing. In the package.json file, make the "prepublish" and "release" entries inside the "scripts" object

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

18. Commit your files and update github. This is necessary because npm will not publish unless your git is in order. First create a .gitignore file to suppress unnecessary files from enering into the repository

```shell
echo -e "node_modules\nbuild" >> .gitignore
git add -A
git commit -m "Initial commit"
git push
```

19. Create an account on the npmjs.com website if you don't have one already and then publish your package

```shell
npm add user
npm run release
```

20. If you want to unpublish your package

```shell
npm unpublish <pkg> -f
``` 
