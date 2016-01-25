# Hive.js Documentation
Hello stranger! This repository will familiarize you with the setup of Hive.js as well as its internals.

Checkout the [table of contents](SUMMARY.md).

# Getting started

## What is Hive.js?

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