# Installation
*First up, a warning: This is alpha software. You can still try to install it anyway, of course. But be prepared that things might break.*

## Pre-requisites
To install hive.js on your machine you'll need to have installed the following:
 * [node.js](http://nodejs.org) v5 (recommended version; not tested in v6, yet, although unlikely to fail severely)
 * [npm](http://npmjs.org) v3 (the package manager for node.js; usually comes bundled with node.js)

If you have installed the pre-requisites, install the hive command line utility on your system with the following command:
```
npm install -g hive
```
(This will not install a complete hive.js instance, but just a little utility to manage such an instance. You can easily remove it again, by running `npm uninstall -g hive`.)

## Set up your instance
Now, we use the hive command line utility to set up a new hive.js instance. Create a new directory and run the following command in the new directory (The below sections explain the choices you can make; if you're running hive-init(1) for the first time in the current directory, you can safely confirm all yes/no questions.):
```
hive init
```

### Choose a database backend
Hive.js uses the [waterline](https://github.com/balderdashy/waterline) ORM, which supports a variety of database backends. You can [pick one of these](https://github.com/balderdashy/waterline-docs#supported-adapters) (hive-init(1) will ask you and help you configure it). `sails-memory` is good for testing purposes, but if you're headed for production, you'll want a real database like mysql or postgresql. Don't use `disk`, as it doesn't support binary data.

### Choose a broadcast transport
Hive.js is scalable. This means that you can spin up any number of workers to handle the load. However, these workers need to communicate and that's when broadcast transports come into play. Currently available are:

 * hive-broadcast-memory (simple stub that only works when you have just one worker)
 * hive-broadcast-smokesignal (sets up a peer-to-peer network through which the workers will communicate)

hive-init(1) will ask you which one you want. `memory` should be fine for most smaller installations. In any case, you can switch to a different transport later on, by uninstalling the current one and installing a new one, both using npm.

### Choose one or more editors / document types
Currently available editors are

 * `hive-editor-text-textarea`: Collaborate on plain text using a simple textarea.
 * `hive-editor-text-codemirror`: Collaborate on plain text using the [codemirror](http://codemirror.net) code editor.
 * `hive-editor-html-ckeditor`: Collaborate on HTML documents using [ckeditor](http://ckeditor.com/).
 * `hive-editor-svg-method-draw`: Collaborate on SVG images using [MethodDraw](https://github.com/duopixel/Method-Draw)

hive-init(1) will ask you which ones you want to install. You should install at least one. Like everything else, you can easily change this (install new editors or remove installed ones, both using npm) after finishing hive-init(1).

## Install an auth provider
Currently there are two auth providers:

* `hive-auth-github`
* `hive-auth-guest`

Both implement allow-all authorization. Which means you don't want to use them in production (which you shouldn't use hive for, yet, anyway). That being sad, especially hive-auth-guest works splendidly for testing. You can install it by running `npm install hive-auth-guest` in your instance directory.

## Fix babel
Currently a lot of modules used by hive on the client-side depend on an outdated version of the babel tool, but work with the latest version if you remove their `.babelrc` files.

Here's a one-liner to help you out. Run the following in your hive instance directory:

```
$ cd node_modules; find . -name .babelrc | grep -v packager | xargs rm
```

(Note: This doesn't work on windows. I'm not a good batch coder. If you are -- any contributions are greatly appreciated. Otherwise, you'll have to dig through the directories: Currently babel is [only enabled](https://github.com/hivejs/hive-ui/blob/master/index.js#L42) for modules that start with  `redux`, `flux` or  `reducers`. Go through all directories that start with these words and delete the file called `.babelrc`.)

## Adjust the settings
Open `config/default.json` in the editor of your choice and adjust the settings as you please. It is also possible to define settings only for specific environments by adding them to the respecitve config file in the config folder (`development.json`, `test.json` and `production.json`).

## Run it
Phew. All set. Now, how do we get this thing started?

First, you need to build the code and translations that are needed on the client-side:

```
hive ui-build
```

Now you can start hive.js by running

```
$ NODE_ENV=test hive -s orm -s http -s queue
```

On windows, use:
```
> set NODE_ENV=test
> hive -s orm -s http -s queue
```

This will start a hive process with a database connection, the http server and the queue service. When spinning up additional workers, you shouldn't start the queue service, so leave out `-s queue` in that case. NODE_ENV is an indicator for the environment in which the app is running. Possible values are `development`, `test` and `production`.

Now check out `http://localhost:1235/documents/1` in your browser. If all is well, you will be asked to authenticate. Afterwards you'll be notified that the document you're trying to access doesn't exist. So far, so good.

What next? New documents can be created [programatically with the REST API](http://docs.hivejs.org/developing_peripherals/interface_rest_api.html) or, after installing the my-documents plugin, via the GUI (see 'Installing plugins'; next chapter).
