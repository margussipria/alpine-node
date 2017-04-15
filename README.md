Minimal Node.js Docker Image
-----------------------------

Version v7.9.0 built on [Alpine Linux](https://alpinelinux.org/).

All versions use the one [margussipria/alpine-node](https://hub.docker.com/r/margussipria/alpine-node/) repository,
but each version aligns with the following tags (ie, `margussipria/alpine-node:<tag>`). The sizes are for the
*unpacked* images as reported by Docker – compressed sizes are about 1/3 of these:

- Full install built with npm:
  - `7`, `7.9`, `7.9.0` – 54.7 MB (npm 4.2.0)

Examples
--------

    $ docker run margussipria/alpine-node:7 node --version
    v7.9.0

    $ docker run margussipria/alpine-node:7 npm --version
    4.2.0

Example Dockerfile for your own Node.js project
-----------------------------------------------

If you don't have any native dependencies, ie only depend on pure-JS npm
modules, then my suggestion is to run `npm install` locally *before* running
`docker build` (and make sure `node_modules` isn't in your `.dockerignore`) –
then you don't need an `npm install` step in your Dockerfile and you don't need
`npm` installed in your Docker image – so you can use one of the smaller
`base*` images. (see [mhart/alpine-node:base-7](https://hub.docker.com/r/mhart/alpine-node/))

    FROM margussipria/alpine-node:7

    WORKDIR /src
    ADD . .

    # If you have native dependencies, you'll need extra tools
    # RUN apk add --no-cache --virtual .build-deps make gcc g++ python \
    #   && npm install \
    #   && apk del .build-deps

    EXPOSE 3000
    CMD ["node", "index.js"]

Caveats
-------

As Alpine Linux uses musl, you may run into some issues with environments
expecting glibc-like behavior – especially if you try to use binaries compiled
with glibc. You should recompile these binaries to use musl (compiling on
Alpine is probably the easiest way to do this).

Inspired by:

- https://github.com/alpinelinux/aports/blob/454db196/main/nodejs/APKBUILD
- https://github.com/alpinelinux/aports/blob/454db196/main/libuv/APKBUILD
- https://hub.docker.com/r/ficusio/nodejs-base/~/dockerfile/
