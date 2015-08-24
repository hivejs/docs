# The client-side of hive.js

## Client-side providers
A component can register files to be loaded by the client-side loader with the `assets` provider. (Don't worry, hive-assets is a noop on the client-side, so simple components will work there, too.) Those files need to export a setup function and may consume and/or provide providers, like server-side components.

### ui
Default implementation is hive-ui, which also includes the [bootstrap](http://getbootstrap.com) stylesheet.

#### ui.baseURL
the protocol, domain, port and root path of the hive installation.

#### ui.page
A page.js instance. See [visionmedia/page.js](https://github.com/visionmedia/page.js).

### auth
Default implementation is hive-ui-auth.

#### auth.registerAuthenticationProvider(method:String, provider:Object)
Registers a client-side authentication method, where `method` is the name of the authentication method, e.g. `"github"`, and provider is an object that looks as follows:

```js
var provider = {
  description: String
, silent: function*(){} // or a function that returns a promise
, ask: function*(){} // or a function that returns a promise
}
```
 * `silent` should be a function that somehow figures out the users credentials for this authentication method silently, e.g. by reading a cookie or retrieving an OAuth token from the current URL. If that is not possible just return `undefined` otherwise return the credentials.
 * `ask` is called if silent authentication fails and the user has chosen an authentication method to identify themselves with. This should ask the user for their credentials or otherwise redirect them to a site that takes their credentials, e.g. an OAuth endpoint.
* `description` is used to display the user more information about the authentication methods they can choose from. A short sentence should be enough, e.g. `"Login with your account at github.com"`.

#### auth.authenticate*()
tries to authenticate the user with the registered authentication providers using silent authentication first and if that fails asks the user for a method and their credentials. It returns an access token that can be used e.g. with hive-api-client or hive-shoe-client.

### models

#### models.load*(apiClient:Object)
returns the models, wiring them up with an API client (an instance of `hive-api-client`). For route `/:id` this will be done automatically and the models made available on `ctx.models`.

#### models.Backbone
The backbone instance used by the models. It is recommended that you use this instance instead of requiring your own backbone, since that can cause weird issues since they won't recognize each other's classes.

### editor
The default implementation is hive-ui-editor.

#### editor.registerEditor(type:String, editorSetup:Function*)
Registers an editor for a given ot type. The `editorSetup` function should load the editor and append any DOM elements as children of `#editor`. It should return a gulf Document.

## Client-side hooks

### ui:start (opts:Object)
called upon initialization, before page.js runs its dispatch algorithm. `opts` will be passed to page.js.

### models:load (models:Object)
called *before* the backbone models are actually created with the input that will be fed to `Backbone.Model.extend`.

### models:loaded (models:Object)
called after the model objects are created. Wait for this hook until you use the models. If you're on the editor page and your component depends on `editor`, then the models will be available in `ctx.models` without the need to wait for any hooks.
