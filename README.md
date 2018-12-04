# Heroku buildpack: PHP [![Build Status](https://travis-ci.org/heroku/heroku-buildpack-php.svg?branch=master)](https://travis-ci.org/heroku/heroku-buildpack-php)

![php](https://cloud.githubusercontent.com/assets/51578/8882982/73ea501a-3219-11e5-8f87-311e6b8a86fc.jpg)


This is the official [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for PHP applications.

It uses Composer for dependency management, supports PHP or HHVM (experimental) as runtimes, and offers a choice of Apache2 or Nginx web servers.

## Usage

You'll need to use at least an empty `composer.json` in your application.

    $ echo '{}' > composer.json
    $ git add composer.json
    $ git commit -m "add composer.json for PHP app detection"

If you also have files from other frameworks or languages that could trigger another buildpack to detect your application as one of its own, e.g. a `package.json` which might cause your code to be detected as a Node.js application even if it is a PHP application, then you need to manually set your application to use this buildpack:

    $ heroku buildpacks:set heroku/php

This will use the officially published version. To use the `master` branch from GitHub instead:

    $ heroku buildpacks:set https://github.com/heroku/heroku-buildpack-php

Please refer to [Dev Center](https://devcenter.heroku.com/categories/php) for further usage instructions.

## Custom Platform Repositories

The buildpack uses Composer repositories to resolve platform (`php`, `hhvm`, `ext-something`, ...) dependencies.

To use a custom Composer repository with additional or different platform packages, add the URL to its `packages.json` to the `HEROKU_PHP_PLATFORM_REPOSITORIES` config var:

    $ heroku config:set HEROKU_PHP_PLATFORM_REPOSITORIES="https://mybucket.s3.amazonaws.com/cedar-14/packages.json"

To allow the use of multiple custom repositories, the config var may hold a list of multiple repository URLs, separated by a space character, in ascending order of precedence.

If the first entry in the list is "`-`" instead of a URL, the default platform repository is disabled entirely. This can be useful when testing development repositories, or to forcefully prevent the use of unwanted packages from the default platform repository.

For instructions on how to build custom platform packages (and a repository to hold them), please refer to the instructions [further below](#custom-platform-packages-and-repositories).

**Please note that Heroku cannot provide support for issues related to custom platform repositories and packages.**

## Development

The following information only applies if you're forking and hacking on this buildpack for your own purposes.

### Pull Requests

Please submit all pull requests against `develop` as the base branch.

### Custom Platform Packages and Repositories

Please refer to the [README in `support/build/`](support/build/README.md) for instructions.

## Simon's Additions

There is already a machine for generating these builds, `wata-php-ext-builder`, but to do so again, you can run the following command. It's important you use the old stack.

```
heroku create --remote heroku-cedar-14 --stack cedar-14
```

On the dev machine, here's your commands to get everything set up.

```
# create cedar-14 app
heroku config:set WORKSPACE_DIR=/app/support/build AWS_ACCESS_KEY_ID=<ACCESS_KEY> AWS_SECRET_ACCESS_KEY=<SECRET> UPSTREAM_S3_BUCKET=lang-php S3_PREFIX=dist-cedar-14-stable/ S3_BUCKET=wata-build2 UPSTREAM_S3_PREFIX=dist-cedar-14-stable/ S3_REGION=s3 STACK=cedar-14 -a wata-php-ext-builder
heroku run bash
```

Here are the commands to build everything from the server

```
support/build/_util/deploy.sh --publish extensions/no-debug-non-zts-20160303/protobuf-3.6.1 --overwrite
support/build/_util/deploy.sh --publish extensions/no-debug-non-zts-20160303/grpc-1.15.0 --overwrite
```
