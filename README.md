Heroku buildpack: Typescript
==============================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for [Typescript](http://www.typescriptlang.org/) apps. It uses [NPM](http://npmjs.org/) and [SCons](http://www.scons.org/). It is based on the [Heroku Node.js buildpack](https://github.com/heroku/heroku-buildpack-nodejs).

Usage
-----

Example usage:

    $ ls
    Procfile  package.json  app.ts

    $ heroku create --stack cedar --buildpack https://github.com/pk11/heroku-buildpack-typescript.git

    $ git push heroku master
    ...
    -----> Removing .DS_Store files
    -----> Fetching custom git buildpack... done
    -----> Typescript app detected
    -----> Resolving engine versions
       Using Node.js version: 0.8.14
       Using npm version: 1.1.65
    -----> Fetching Node.js binaries
    -----> Vendoring node into slug
    -----> Installing dependencies with npm
       npm http GET https://registry.npmjs.org/typescript-require/0.1.7
       npm http GET https://registry.npmjs.org/connect-redis
       npm http GET https://registry.npmjs.org/crypto
       npm http GET https://registry.npmjs.org/debug
       npm http GET https://registry.npmjs.org/express
       npm http GET https://registry.npmjs.org/express-blocks
       npm http GET https://registry.npmjs.org/express-expose
       npm http GET https://registry.npmjs.org/formidable
       npm http GET https://registry.npmjs.org/gm
       npm http GET https://registry.npmjs.org/http-get
       ...
             Dependencies installed
    -----> Compiling typescript source
            Source compiled to target
    -----> Building runtime environment
    -----> Discovering process types
            Procfile declares types -> web
    -----> Compiled slug size: 27.5MB
    -----> Launching... done, v173
    ...

The buildpack will detect your app as Typescript if it detects a file matching `*.ts` in your project root.  It will use NPM to install your dependencies, and vendors a version of the Node.js runtime into your slug.  

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
