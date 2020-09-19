---
layout: post
title:  "Publish a Javascript package using git, npm, babel, watch and jest on macos"
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
...
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
...
},
...
```
Open a separate terminal tab and run the test:watch process

```shell
npm run test:watch
```

Make changes to your code file and check if it is detected and build and test runs automatically

## link

TBA
