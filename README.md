# Hive.js Documentation
Hello stranger! This repository will familiarize you with the setup of Hive.js as well as its internals.

Checkout the [table of contents](SUMMARY.md).

## Installation and setup
 * [Installation](installation.md)
 * Installing plugins

## Development

### Peripherals
 * Data model
 * Authentication
 * Using the REST API interface
 * Using the shoe interface

### Core / plugins
 * [Getting started](getting-started.md)
 * Data model
 * [server-side interfaces](server-side.md)
 * [client-side interfaces](client-side.md)

# Gettings started

## What is a hive instance?
Similar to a git repository, a hive instance is simply a folder where your instance lives:
code, configuration and any other stuff you might want to add all in one place.

```
your-hive-instance/
+-node_modules/
  +-components..
+-config/
  +-development.json
  +-test.json
  +-production.json
+-package.json
```

#### Components
The hive code base is organized in components that live in `your-hive-instance/node_modules/` and
are conveniently managed via `your-hive-instance/package.json` as you would expect from a node project.

#### Config
Configuration files are stored in `your-hive-instance/config/` as `development.json`, `test.json` and `production.json`.

#### hive-init
Running `hive-init(1)` will briefly guide you through the set-up of your hive instance dir, installing
the standard components as well as creating the necessary config files.


## What are components?
Everything you would like to add to hive is going to be wrapped in a component.

A `hive-<component>` can register a *provider* and can consume providers by other components.
A provider can do stuff for you, e.g. an auth provider can do authentication.

Application tasks (e.g. http, queue, etc.) are encapsuled as *services*.
The beauty of services is that they can be started internally in the main
process or externally in a separate process. You can register your services with
the services provider.

Note that hive doesn't implement a way to re-start specific services since this
would require assumptions that would limit your choices about the set-up and
architecture of your instance.

Usually a component is either a *provider* or a *service*, although in rare cases it might be both, e.g. the http component is a kind of registry provider
while encapsuling the actual http server in a service.

## Parts of hive

### Core server-side providers
 * [hooks](https://github.com/hivejs/hive-hooks) -- allows registration and emission of hooks
 * [logger](https://github.com/hivejs/hive-logger) -- provides logging functionality
 * [config](https://github.com/hivejs/hive-config) -- loads and provides the configuration
 * [cli](https://github.com/hivejs/hive-cli) -- register your subcommands here
 * [services](https://github.com/hivejs/hive-services) -- register your services here
 * [broadcast](https://github.com/hivejs/hive-broadcast) -- allows document-wise broadcasting of messages and new changes
 * [http](https://github.com/hivejs/hive-http) -- a koa.js instance
 * [orm](https://github.com/hivejs/hive-orm) -- Sets up the orm (waterline) and emits the orm:initialize hook to allow tweaking of settings
 * [ot](https://github.com/hivejs/hive-ot) -- Allows registration of ot types
 * [assets](https://github.com/hivejs/hive-assets) -- Allows registration of directories with static files and client-side components
 * [sync](https://github.com/hivejs/hive-sync) -- Manages gulf Documents
 * [auth](https://github.com/hivejs/hive-auth) -- Allows registration of authentication methods and authorization implementations

### Core client-side providers
 * [ui](https://github.com/hivejs/hive-ui) -- kicks off everything on the client side and provides the page.js instance
 * [ui-auth](https://github.com/hivejs/hive-ui-auth) -- allows registration of authentication methods and handles authentication
 * [ui-editor](https://github.com/hivejs/hive-ui-editor) -- allows registration of editors for OT types and connects them to the server
 * [ui-models](https://github.com/hivejs/hive-ui-models) -- enables access to the models as Backbone.Model objects

### Core services
 * [http](https://github.com/hivejs/hive-http) -- the http server
 * [queue](https://github.com/hivejs/hive-queue) -- a semaphore that tells each worker when it's turn has come to commit changes

### Core commands
 * [hive(1)](https://github.com/hivejs/hive) -- main binary
 * hive-init(1) -- easily set-up your hive instance

### Other core components
 * [models](https://github.com/hivejs/hive-models) -- registers the built-in data models and emits models:load and models:loaded to allow addition and tweaking of models
 * [interface-rest-api](https://github.com/hivejs/hive-interface-rest-api) -- registers the REST API, which is the stadard hive interface (one way to interface with hive in your app)
 * [interface-shoe](https://github.com/hivejs/hive-interface-shoe) -- the streaming collaboration interface used by hive-ui-editor
 * [broadcast-memory](https://github.com/hivejs/hive-broadcast-memory) -- broadcast transport stub for when you have only one worker
 * [broadcast-smokesignal](https://github.com/hivejs/hive-broadcast-smokesignal) -- broadcast transport via [smokesignal](https://github.com/marcelklehr/smokesignal) p2p network

### Libraries
 * [client-rest-api](https://github.com/hivejs/hive-client-rest-api) -- a consumer of the REST API
 * [client-shoe](https://github.com/hivejs/hive-client-shoe) -- a consumer of hive-shoe-interface

## Architecture
In the drawing below you can see how all these components interact on the server.
```
          Clients
           |   ^
   --------|---|-------------------------------------------
           v   |
        [  Interfaces  ]---> [       ]
        [  (Auth)------]---> [Plugins]    // A worker process
         |     |     ^       [       ]
         v     v     |       [       ]
    [Model] [  Sync  |] ---> [       ]
     |       ^    |  |
     |       |    |  |
    -|-------|----|--|---------------------------------------
     v       v    v  |
   [DB] [Queue] [Broadcast]               // Other processes

```
