Heroku buildpack: Typescript
==============================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for [Typescript](http://www.typescriptlang.org/) apps. It uses [NPM](http://npmjs.org/) and [SCons](http://www.scons.org/). It is based on the [Heroku Node.js buildpack](https://github.com/heroku/heroku-buildpack-nodejs).

Usage
-----

Example usage:

    $ ls
    Procfile  package.json  src

    $ ls src
    app.coffee

    $ heroku create --stack cedar --buildpack https://github.com/pk11/heroku-buildpack-typescript.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom git buildpack... done
    -----> Coffeescript app detected
    -----> Resolving engine versions
           Using Node.js version: 0.8.12
           Using npm version: 1.1.49
    -----> Fetching Node.js binaries
    -----> Vendoring node into slug
    -----> Installing dependencies with npm
           coffee-script@1.4.0 node_modules/coffee-script

           express@3.0.0 node_modules/express
           ├── methods@0.0.1
           ├── fresh@0.1.0
           ├── range-parser@0.0.4
           ├── cookie@0.0.4
           ├── crc@0.2.0
           ├── commander@0.6.1
           ├── debug@0.7.0
           ├── mkdirp@0.3.3
           ├── send@0.1.0 (mime@1.2.6)
           └── connect@2.6.0 (pause@0.0.1, bytes@0.1.0, qs@0.5.1, formidable@1.0.11, send@0.0.4)
           Dependencies installed
    -----> Compiling coffee source
           Source compiled to target
    -----> Building runtime environment
    ...

The buildpack will detect your app as Typescript if it detects a file matching `*.ts` in your project root.  It will use NPM to install your dependencies, and vendors a version of the Node.js runtime into your slug.  The `node_modules` directory will be cached between builds to allow for faster NPM install time.

You must include Typescript in your `package.json`. The buildpack does not install Typescript automatically in order to allow you to specify your own Typescript version.

Compiled javascript is written to the `ts` directory in the slug. 

Node.js and npm versions
------------------------

You can specify the versions of Node.js and npm your application requires using `package.json`

    {
      "name": "myapp",
      "version": "0.0.1",
      "engines": {
        "node": ">=0.4.7 <0.7.0",
        "npm": ">=1.0.0"
      }
    }

To list the available versions of Node.js and npm, see these manifests:

* http://heroku-buildpack-nodejs.s3.amazonaws.com/manifest.nodejs
* http://heroku-buildpack-nodejs.s3.amazonaws.com/manifest.npm

Hacking
-------

To use this buildpack, fork it on Github.  Push up changes to your fork, then create a test app with `--buildpack <your-github-url>` and push to it.

To change the vendored binaries for Node.js, NPM, and SCons, use the helper scripts in the `support/` subdirectory.  You'll need an S3-enabled AWS account and a bucket to store your binaries in.

For example, you can change the default version of Node.js to v0.6.7.

First you'll need to build a Heroku-compatible version of Node.js:

    $ export AWS_ID=xxx AWS_SECRET=yyy S3_BUCKET=zzz
    $ s3 create $S3_BUCKET
    $ support/package_nodejs 0.6.7

Open `bin/compile` in your editor, and change the following lines:

    DEFAULT_NODE_VERSION="0.6.7"
    S3_BUCKET=zzz

Commit and push the changes to your buildpack to your Github fork, then push your sample app to Heroku to test.  You should see:

    -----> Vendoring node 0.6.7
