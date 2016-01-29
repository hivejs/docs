# Developing components / plugins


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
All the core providers reside in [hive-core](https://github.com/hivejs/hive-core).
 * hooks -- allows registration and emission of hooks
 * logger -- provides logging functionality
 * config -- loads and provides the configuration
 * cli -- register your subcommands here
 * services -- register your services here
 * broadcast -- allows document-wise broadcasting of messages and new changes
 * http -- a koa.js instance
 * orm -- Sets up the orm (waterline) and emits the orm:initialize hook to allow tweaking of settings
 * ot -- Allows registration of ot types
 * ui -- Allows registration of directories with static files, client-side components and stylesheets
 * sync -- Manages gulf Documents
 * auth -- Allows registration of authentication methods and authorization implementations
 * importexport

### Core client-side providers
All the core client-side providers reside in [hive-ui](https://github.com/hivejs/hive-ui)
 * ui -- kicks off everything on the client side and provides redux store
 * session -- allows registration of authentication methods and handles authentication
 * editor -- allows registration of editors for OT types and connects them to the server
 * api -- enables access to the REST API
 * importexport -- access to import/export functionality
 * settings -- exposes settings in the UI and allows components to get and set them

### Core services
You can start these services with the `-s|--start` option of `hive(1)`.
 * http -- the http server
 * queue -- a semaphore that tells each worker when it's turn has come to commit changes

### Core commands
 * [hive(1)](https://github.com/hivejs/hive) -- main binary
 * hive-init(1) -- easily set-up your hive instance

### Other core components
 * [models](https://github.com/hivejs/hive-models) -- registers the built-in data models and emits models:load and models:loaded to allow addition and tweaking of models
 * [interface-rest-api](https://github.com/hivejs/hive-interface-rest-api) -- registers the REST API, which is the stadard hive interface (one way to interface with hive in your app)
 * [interface-shoe](https://github.com/hivejs/hive-interface-shoe) -- the streaming collaboration interface used by hive-ui-editor
 * [broadcast-memory](https://github.com/hivejs/hive-broadcast-memory) -- broadcast transport stub for when you have only one worker
 * [broadcast-smokesignal](https://github.com/hivejs/hive-broadcast-smokesignal) -- broadcast transport via [smokesignal](https://github.com/marcelklehr/smokesignal) p2p network

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