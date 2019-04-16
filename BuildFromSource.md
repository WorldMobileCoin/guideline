# wmcc-builder (WorldMobileCoin)

__NOTE__: Build wmcc-desktop from source

---

**WorldMobileCoin** is a new cryptocurrency.

Official website: https://www.worldmobilecoin.com

## Installation Prerequisites
NodeJS (v8.0.0+) https://nodejs.org/en/  
GIT (v2) https://git-scm.com/downloads  
A proper C/C++ compiler:
> g++/gcc (Unix)  
> Microsoft Visual C++ Build Tools (Windows)  

** Please refer to [node-gyp](https://github.com/nodejs/node-gyp) for native build

## Install builder
```
$ git clone git://github.com/worldmobilecoin/wmcc-builder.git
$ cd wmcc-builder
$ npm install
```

## Build client
```
Linux:
$ npm run build-linux
Windows:
$ npm run build-win64
OSX:
$ npm run build-osx
```

Released files and executable can be found on release folder
