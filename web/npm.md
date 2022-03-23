# npm

## npm

- https://www.npmjs.com/
- Node package manager
- Pre-installed with node.js
- Easily install packages/modules
- Modules are javascript libraries
- Share and re-use code

npm consists of three distinct components:

- the website: Use the website to discover packages.
- the Command Line Interface (CLI): The CLI runs from a terminal, and is how most developers interact with npm.
- the registry: The registry is a large public database of JavaScript software and the meta-information surrounding it.

## package.json

- Manifest file for application information
- List dependencies name & version
- specify version updates rules
- create npm script
- created with npm init

```
{
"name": "landio-html",
"version": "1.0.0",
"description": "![Version badge](https://d25lcipzij17d.cloudfront.net/badge.svg?id=gh&type=6&v=1.2.7&x2=0)",
"main": "index.js",
"scripts": {},
"repository": {
    "type": "git",
    "url": "git+https://github.com/tatygrassini/landio-html.git"
    },
"author": "",
"license": "ISC",
"bugs": {
"url": "https://github.com/tatygrassini/landio-html/issues"
},
"homepage": "https://github.com/tatygrassini/landio-
html#readme",
"devDependencies": {
    "browser-sync": "^2.24.7",
    "gulp": "^3.9.1",
    "gulp-autoprefixer": "^6.0.0",
    "gulp-clean-css": "^3.10.0",
    "gulp-include": "^2.3.1",
    "gulp-notify": "^3.2.0",
    "gulp-rename": "^1.4.0",
    "gulp-sass": "^4.0.1",
    "gulp-sourcemaps": "^2.6.4",
    "gulp-uglify": "^3.0.1"
    },
"dependencies": {
    "hoek": "^5.0.4"
    }
```

## semantic versioning

- semver.org
- Patch releases: 1.0 or 1.0.x or ~1.0.
- Minor releases: 1 or 1.x or ^1.0.
- Major releases: * or x
- Vidéo explicative: https://www.youtube.com/watch?v=kK4Meix58R

```
"dependencies": {
    "my_dep": "^1.0.0","another_dep": "~2.2.0"
}, 
```
## npm config

```bash
npm config set init-author-name "fg"
npm config set init-author-email "fg@example.com"
npm config set init-author-url "http://www.example.com"
npm config set init-license "GPL"
npm init --yes
```

## npm install

- Install all development/production packages 
- *-- production* npm will not install modules listed in devDependencies

```bash
npm install --production
npm install
```

## local / global

- global (-g) the package is installed globally
- local the package is installed inside the project (./node_modules)

```bash
npm install lodash --save
npm install --global live-server
```

## development / production

- --save update package.json “dependencies” for production environment
- --save-dev update package.json devDependencies” for dev environment

```bash
npm install lodash --save
npm install gulp gulp-sass --save-dev
```

## uninstall / remove packages

```bash
npm remove live-server
npm uninstall gulp gulp-sass --save-dev
```

## update / list packages

```
npm update
npm update gulp gulp-sass

npm list
npm list --depth 1
```

## scripts

```
"scripts": {
    "start": "grunt serve",
    "build": "grunt"
}, 
```

```
npm start
npm run build
```

# commands shortcuts

commands | shortcut 
---------|----------
 install | i
 --global | -g
 --save | -S
 --save-dev | -D
remove | rm
list | ls

