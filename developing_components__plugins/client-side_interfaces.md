# Client-side interfaces

## Redux store
The default Hive.js UI is based on redux. If you're not familiar with redux, head over to [the redux docs](http://redux.js.org/).

tl;dr: Redux is a framework making use of functional reactive programming techniques. It features a central state atom (a simple object), that can be modified by dispatching actions. Reducer functions take state atom and an action and spit out the new state. You can subscribe to changes to the state atom and render the interface components accordingly.

Since the state atom is central to everything you will do on the client-side, we have a snapshot for you to inspect right here:

```js
{
  /**
   * The current route
   * modify with
   *  * ui.action_route(path:String):ROUTE
   *  * ui.action_loadState(state:Object):LOAD_STATE
   */
  router: '/documents/1'
  
  /**
   * Modify with
   * * session.action_chooseAuthMethod(authMethod):SESSION_CHOOSE_AUTH_METHOD
   * * session.action_loggingIn():SESSION_LOGGING_IN
   * * session.action_login(credentials):SESSION_LOGIN
   */
, session: {
    /**
     * if true, this will cause a message to be shown in the
     * choose-auth-dialog
     */
    authFailed: false
    
    /**
     * The auth method the user has chosen, 'token' if the default
     * silent token cookie authentication is used
     */
  , authMethod: 'token'

    /**
     * `True` while authentication is running - a loading indicator
     * will be displayed
     */
  , logginIn: false
  
    /**
     * The grant response by the `/token` endpoint
     */
  , grant: {
      access_token: 'DIOJgUZGVGHHZTU...'
    , token_type: 'bearer'
    , user: 17
    }
    
    /**
     * The user object of the currently logged-in user
     * if not logged in, this will be null
     */
  , user: {
      ...
    }
  }
  /**
   * Modified by
   * ... the API of the `editor`component has not been finalized yet
   */
, editor: {
    /**
     * When the user navigates to an editor, this will be true
     */
    active: true
    
    /**
     * `True` if there was an error loading the document object
     */
  , notFound: null
    
    /**
     * the document object of teh document being (or about to be) edited 
     */
  , document: {
      ...
    }
    
    /**
     * The ID of the registered editor chosen (either explicitly by the
     * user or implicitly)
     */
  , editor: "CodeMirror"
  
    /**
     * `True` if there was an error loading the editor instance
     */
  , loadError: null
  }

  /**
   * There is currently no way to interface with this component
   * It's read-only-ish. You can modify its state manually, though.
   */
, editorTextCodemirror: {
    lineNumbers: false
  , mode: 'javascript'
  }

  /**
   * Modified by 
   * * oauth.action_activate():OAUTH_ACTIVATE
   * * oauth.action_grant(granted:Bool):OAUTH_GRANT
   */
, oauth: {
    /**
     * `True` if the oauth provider is active i.e. we're on /authorize
     */
    active: false
    
    /**
     * `True` if the user has taken an action (either granted or denied;
     * either way, we're done)
     */
  , done: false
  
    /**
     * The redirect_uri as passed in the query string(String)
     */
  , redirect_uri: null
 
    /**
     * The scope as passed in the query string (String)
     */
  , scope: null
  }
}
```

## Client-side providers
A component can register files to be loaded by the client-side loader with the `ui` provider. Those files, like server-side components, need to export a setup function and may consume and/or provide providers.

### ui
Default implementation is hive-ui, which also includes the [bootstrap](http://getbootstrap.com) stylesheet.

#### ui.reduxReducerMap
Add a new property to this `Object` and assign it a reducer to have your own namespace in the state atom. The reducer will receive your state sub-tree only, instead of the whole state tree.

#### ui.reduxRootReducers
Add your root reducers to this `Array`. This is a great way for hooking into other components actions, but be sure to *always* return the state you got when you're not interested in an action.

#### ui.reduxMiddleware
Add your redux middle-ware to this `Array`. This is a great way to invoke side-effects such as talking with the server, storing cookies or waiting for a route (see `ui.route()`).

#### ui.start(config:Object)
Called by the bootstrapping code with the client-side config received by the server. Creates the redux store applying the middleware and combining all reducers.

#### ui.onStart
An event emitter, emitted after the ui was `ui.start()`ed. You can listen to this event by simply calling it with a listener: `ui.onStart(listener)`.

#### ui.baseURL
the protocol, domain, port and root path of the hive installation.

#### ui.config
An object containing the configuration received from the server, populated during `ui.start()`.

#### ui.store
The redux store, populated during `ui.start()`. You can `store.dispatch(action:Object)` actions and `store.subscribe(listener:function)` to changes (see  [the redux API reference](http://redux.js.org/docs/api/Store.html)).

#### ui.action_route(route:String):ROUTE
This action allows you to route to a different URL.

#### ui.action_loadState(state:Object):LOAD_STATE
This action will replace the state tree with the one you supply. A ROUTE action will be dispatched automatically as well.

#### ui.route(action, route:String):Bool|Object
Use this helper in your middle-ware to listen for routes. You can specify a route in the familiar express style: `/users/:user`. If action is a ROUTE and the route matches the path in the action's payload, this will return the parameters as an Object, otherwise it'll return `false`.

### auth
Default implementation is hive-ui-auth.

#### auth.registerAuthenticationProvider(method:String, provider:Object)
Registers a client-side authentication method, where `method` is the name of the authentication method, e.g. `"github"`, and `provider` is an object that looks as follows:

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

You don't need to implement both silent and ask, if that doesn't suit your use case.

#### auth.authenticate*()
tries to authenticate the user with the registered authentication providers using silent authentication first and if that fails asks the user for a method and their credentials. It returns an access token that can be used e.g. with hive-api-client or hive-shoe-client.

### models

#### models.load*(apiClient:Object)
returns the models, wiring them up with an API client (an instance of `hive-api-client`). For route `/:id` this will be done automatically and the models made available on `ctx.models`.

#### models.Backbone
The backbone instance used by the models. It is recommended that you use this instance instead of requiring your own backbone, since that can cause weird issues since they won't recognize each other's classes.

### editor
The default implementation is hive-ui-editor.

#### editor.registerEditor(name:String, type:String, description:String, editoreditorSetup:Function*(editor:Element))
Registers an editor called `name` for a given ot `type` with a `description`. The `editorSetup` function should load the editor and append any DOM elements as children of the element passed as `editor`. It should return a gulf Document. Editor names must be unique and are used for displaying options together with `description`s.

### editor.createEditor(name:String, el:Element)
Creates an editor by `name` inside `el`.

### editor.askForEditorWithType(type:String)
Returns the editor name if only one is available for the passed `type`. If more are available, adds a dialog box to `document.body` which allows the user to choose between the available ones and returns the editors's name.

## Client-side hooks

### ui:start (opts:Object)
called upon initialization, before page.js runs its dispatch algorithm. `opts` will be passed to page.js.

### models:load (models:Object)
called *before* the backbone models are actually created with the input that will be fed to `Backbone.Model.extend`.

### models:loaded (models:Object)
called after the model objects are created. Wait for this hook until you use the models. If you're on the editor page and your component depends on `editor`, then the models will be available in `ctx.models` without the need to wait for any hooks.
