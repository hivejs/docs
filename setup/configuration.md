# Configuration

## Main confguration
Please note: changing configuration currently requires a restart of the web
server. If you alter configuration for the UI, make sure to build the project
again using the `hive ui-build` command, before restarting.

The main config file is located at `config/default.json`. Hive lets you override
settings for each environment by modifying the respective config eg.
`config/development.json` or `config/production.json`.

## Defining environment
The environment used is defined from the `NODE_ENV` environment variable when
you start the application. You can override this.

### Example for Linux or Mac:
Set the environment by running `export NODE_ENV=development` before you start
the server.

### Example for Windows:
Set the environment by running `set NODE_ENV=development` before you start the
server.

## Overriding config using environment variables
You can inject settings via environment variables in the `hive_` namespace. `_`
is used as the hierarchy delimiter, e.g. to change the port the http server
listens on you would set `HIVE_HTTP_PORT` to `1337`.

### Logging (WIP)
Logging is done using the `log4js-node` package. By default, everything logged
is being redirected to `STDOUT`. If you need to connect your logger to another
way, you can see all the different ways of doing the [Official node4js-node wiki page
on Appenders](https://github.com/nomiddlename/log4js-node/wiki/Appenders).
