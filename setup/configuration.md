# Configuration

## How to set config options
The main config file is `config/default.json`
Hive allows you to tweak settings for different environments by adding them to the respecitve config file in the config folder (`config/development.json`, `config/test.json` and `config/production.json`). You can set the environment by setting an environment variable (figures, eh?) called `NODE_ENV`:

On Linux/Mac, use e.g. `export NODE_ENV=development`, while windows users do `set NODE_ENV=development`

You can also inject settings via env variables in the `hive_` namespace, while `_` (underscore) is the hierarchy delimiter, e.g. to dynamically change the port the http server listens on, set `hive_http_port` to `1337`.