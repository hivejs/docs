# Localization

All core client-side components (aka. `hive-ui`) can be translated to another language. This is called localization and you can use the same tools to do the same for your plugins.

## Messages format
First you'll need to define your messages. This is done in hierarchical JSON. Every component allocates a new group of messages so that there are no collisions. Your messages files will need to have the locale ID as their name (like this: `en.json`) and need to be in a sub-directory of your component, that you must register with the server-side `ui` provider:

```js
ui.registerLocaleDir(__dirname+'/locales')
```

This is an example `en.json`:

```json
{
  "plugin-presence": {
    "users": "{count} Users"
  , "users-subheading": "currently viewing this document"
  , "you": "this is you!"
  }
}

```

## Globalize.js
Hive uses the excellent [globalize](https://github.com/jquery/globalize). Messages are loaded automatically, provided you have registered your locale directory.

In your client-side code you can use [globalize's API](https://github.com/jquery/globalize/blob/master/doc/api/message/message-formatter.md) as follows:

```js
ui.globalize.messageFormatter('plugin-presence/users')(userCount)
```

For convenience you can use the shorthand:

```js
ui._('plugin-presence/users')(userCount)
```

If your render functions are tied to the main redux store (e.g. you're using `ui.onRenderNavbar` and its cousins) using the message formatter is enough: since the locale is part of the store your render functions will be re-evaluated once it changes. If you want to be notified when the locale changes, listen to `ui.onLocalize`.