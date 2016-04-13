# Installation
*First up, a warning: This is alpha software. You can still try to install it anyway, of course. But be prepared that things might break.*

## Pre-requisites
To install hive.js on your machine you'll need to have installed the following:
 * [node.js](http://nodejs.org) v5
 * [npm](http://npmjs.org) v3 (usually bundled with node.js)

If you have installed the pre-requisites, install the hive command globally with the following command:
```
npm install -g hive
```

## Set up your instance
Now, we use the hive command to set up your hive instance: Run the following command in the directory where you want to set up your instance.
```
hive init
```

### Choose a database backend
Hive.js uses the [waterline](https://github.com/balderdashy/waterline) ORM, which supports a variety of database backends. You can [pick one of these](https://github.com/balderdashy/waterline-docs#supported-adapters) (hive-init(1)) will ask you and help you configure it). `sails-memory` is good for testing purposes, but if you're headed for production, you'll want a real database like mysql or postgresql.

### Choose a broadcast transport
Hive.js is scalable. This means that you can spin up any number of workers to handle the load. However, these workers need to communicate and that's when broadcast transports come into play. Currently available are:

 * hive-broadcast-memory (simple stub that only works when you have just one worker)
 * hive-broadcast-smokesignal (sets up a peer-to-peer network through which the workers will communicate)

hive-init(1) will ask you which one you want.

### Choose one or more editors
Currently available editors are

 * `hive-editor-text-textarea`: Collaborate on plain text using a simple textarea.
 * `hive-editor-text-codemirror`: Collaborate on plain text using the [codemirror](http://codemirror.net) code editor.
 * `hive-editor-html-ckeditor`: Collaborate on HTML documents using [ckeditor](http://ckeditor.com/).
 * `hive-editor-svg-method-draw`: Collaborate on SVG images using [MethodDraw](https://github.com/duopixel/Method-Draw)

hive-init(1) will ask you which ones you want to install.

## Install an auth provider
Currently there are two auth providers:

* `hive-auth-github`
* `hive-auth-guest`

Both implement allow-all authorization. Which means you don't want to use them in production (which you shouldn't use hive for anyway). That being sad, especially hive-auth-guest works splendidly for testing. You can install it by running `npm i hive-auth-guest` in your instance directory.

## Fix babel
Currently a lot of modules used by hive on the client-side depend on an outdated version of babel, but work with the latest if you remove their `.babelrc` files.

Here's a one-liner to help you out. Run the following in your hive instance directory:

```
$ cd node_modules; find . -name .babelrc | grep -v packager | xargs rm
```

(Note: This doesn't work on windows. I'm not a good batch coder. If you are -- any contributions are greatly appreciated. Otherwise, you'll have to dig through the directories: Currently babel is [only enabled](https://github.com/hivejs/hive-ui/blob/master/index.js#L42) for modules that start with  `redux`, `flux` or  `reducers`. Go through all directories that start with these words and delete the file called `.babelrc`.)

## Adjust the settings
These are just the mandatory settings plus the settings for hive-broadcast-smokesignal.
```js
{
  "ui": {
    "baseURL": "http://localhost:1235"
  }
, "orm": {
    "adapters": [
      "sails-memory"
    ]
  , "connections": {
      "default": { "adapter": "sails-memory" }
    }
  }
, "http": {
    "port": 1235
  }
, "authToken": {
    "secret": "shhh"
  }
, "oauth": {
    "secret": "shhhh"
  }
, "queue": {
    "port": 12351
  , "address": "127.0.0.1"
  }
, "broadcast-smokesignal": {
    "networkMask": "192.168.2.1/255.255.255.0"
  , "port": 12352
  , "seeds": [
      {"address":"192.168.2.100", "port":12352}
    ]
  }
, "logger": {
    "appenders": [
      { "type": "console" }
    ]
  }
}
```

## Run it
Now you can start hive.js by running

```
$ NODE_ENV=test hive -s http -s queue
```

On windows, use:
```
> set NODE_ENV=test
> hive -s http -s queue
```

(This will start a hive process with the http server and the queue service. When spinning up additional workers, you shouldn't start the queue service, so leave out `-s queue` in that case.)

Now check out `http://localhost:1235/documents/1` in your browser. If all is well, you will be asked to authenticate. Afterwards you'll be notified that the document you're trying to access doesn't exist. So far, so good.
