# Installation
## Pre-requisites
To install hive.js on your machine you'll need to have installed the following:
 * [node.js](http://nodejs.org) v4
 * [npm](http://npmjs.org) (usually bundled with node.js)

If you have installed the pre-requisites, install the hive command globally with the following command:
```
npm install -g hivejs
```

## Set up your instance
Now, we use the hive command to set up your hive instance: Run the following command in the directory where you want to set up your instance.
```
hive init
```

## Choose a database backend
Hive.js uses the [waterline](https://github.com/balderdashy/waterline) ORM, which supports a variety of database backends. You can [pick one of these](https://github.com/balderdashy/waterline-docs#supported-adapters) and install it with npm install, e.g. `npm install sails-memory` is good for testing purposes.

## Choose a broadcast transport
Hive.js is scalable. This means that you can spin up any number of workers to handle the load. However, these workers need to communicate and that's when broadcast transports come into play. Currently available are:

 * hive-broadcast-memory (simple stub that only works when you have just one worker)
 * hive-broadcast-smokesignal (sets up a peer-to-peer network through which the workers will communicate)

By default hive-init(1) installs hive-broadcast-memory, so if you want some other transport you need to uninstall it first and then install the new transport with `npm install hive-broadcast-<name>`, where `<name>` is the name of the transport.

## Choose one or more editors
Currently available editors are

 * `hive-editor-text-textarea`: Collaborate on plain text using a simple textarea.
 * `hive-editor-text-codemirror`: Collaborate on plain text using the [codemirror](http://codemirror.net) code editor.
 * `hive-editor-html-ckeditor`: Collaborate on HTML documents using [ckeditor](http://ckeditor.com/).
 * `hive-editor-svg-method-draw`: Collaborate on SVG images using [MethodDraw](https://github.com/duopixel/Method-Draw)

You can install each of them by running `npm install hive-editor-<name>`, where `<name>` is the name of the editor.

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
