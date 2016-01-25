# Setup

Hive.js is built on top of the [Node.js](https://nodejs.org) framework. It has been tested to work on both POSIX compliant and Windows systems.

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