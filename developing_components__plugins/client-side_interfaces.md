# Client-side interfaces

<!-- toc -->

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
   * The current locale
   * modify with
   *  * ui.action_setLocale(locale:String):SET_LOCALE
   */
  locale: 'en'
  
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
     * if not logged in, this will be null
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
    
    /**
     * A boolean, that tells you if the server is reachable
     * (via the shoe stream interface)
     */
  , streamConnected: true
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
, importexport: {
    exportTypes: ui.config['importexport:exportTypes']
  , importTypes: ui.config['importexport:importTypes']
  , showExportDropdown: false
  , showImportDropdown: false
  , exporting: false
  , exportError: false
    /**
     *
     */
  , importing: false
  , importError: false
  }
}
```

## Client-side providers
A component can register files to be loaded by the client-side loader with the `ui` provider. Those files, like server-side components, need to export a setup function and may consume and/or provide providers.

### ui
Package: `hive-ui`

#### ui.reduxReducerMap
Add a new property to this `Object` and assign it a reducer to have your own namespace in the state atom. The reducer will receive your state sub-tree only, instead of the whole state tree.

#### ui.reduxRootReducers
Add your root reducers to this `Array`. This is a great way for hooking into other components actions, but be sure to *always* return the state you got when you're not interested in an action.

#### ui.reduxMiddleware
Add your redux middle-ware to this `Array`. This is a great way to invoke side-effects such as talking with the server, storing cookies or waiting for a route (see `ui.route()`).

#### ui.start(config:Object)
Called by the bootstrapping code with the client-side config received by the server. Creates the redux store applying the middleware and combining all reducers.

#### ui.onStart:AtomicEmitter
Emitted after the ui was `ui.start()`ed. You can listen to this event by simply calling it with a listener: `ui.onStart(listener)`.

#### ui.baseURL
the protocol, domain, port and root path of the hive installation.

#### ui.config
An object containing the configuration received from the server, populated during `ui.start()`.

#### globalize
The [globalize instance](https://github.com/jquery/globalize).

#### ui._(messagePath): Function(params...|params:Object)
A shortcut for `ui.globalize.messageFormatter`. See [Localization]() for more information.

#### ui.store
The redux store, populated during `ui.start()`. You can `store.dispatch(action:Object)` actions and `store.subscribe(listener:function)` to changes (see  [the redux API reference](http://redux.js.org/docs/api/Store.html)).

#### ui.onRenderHeader:AtomicEmitter(store, children)
Emitted when the header is rendered. You can listen to this event by simply calling it with a listener: `ui.onRenderHeader(listener)`.

#### ui.onRenderNavbar:AtomicEmitter(store, children)
Emitted when the navbar is rendered. You can listen to this event by simply calling it with a listener: `ui.onRenderNavbar(listener)`.

#### ui.onRenderBodyAtomicEmitter(store, children)
Emitted when the body is rendered. You can listen to this event by simply calling it with a listener: `ui.onRenderBody(listener)`.

#### ui.action_route(route:String):ROUTE
This action allows you to route to a different URL.

#### ui.action_loadState(state:Object):LOAD_STATE
This action will replace the state tree with the one you supply. A ROUTE action will be dispatched automatically as well.

#### ui.route(action, route:String):Bool|Object
Use this helper in your middle-ware to listen for routes. You can specify a route in the familiar express style: `/users/:user`. If action is a ROUTE and the route matches the path in the action's payload, this will return the parameters as an Object, otherwise it'll return `false`.

### session
Package: `hive-ui`

#### session.registerAuthenticationProvider(method:String, provider:Object)
Registers a client-side authentication method, where `method` is the name of the authentication method, e.g. `"github"`, and `provider` is an object that looks as follows:

```js
var provider = {
  description: String
, silent: function(){}
, ask: function(children){}
}
```
 * `silent()` should be a function that somehow figures out the users credentials for this authentication method *silently*, e.g. by reading a cookie or retrieving an OAuth token from the current URL. If that is not possible just return `undefined` otherwise return the credentials.
 * `ask(children:Array)` is called if silent authentication fails and the user has chosen an authentication method to identify themselves with. This should ask the user for their credentials or otherwise redirect them to a site that takes their credentials, e.g. an OAuth endpoint. You can add a virtual-dom tree to the `children` array and it will appear inside a panel on the screen. When you have obtained the user's credentials, dispatch [`session.action_login(credentials)`]()
 * `description` is used to display the user more information about the authentication methods they can choose from. A short sentence should be enough, e.g. `"Login with your account at github.com"`.

You don't need to implement both silent and ask, if that doesn't suit your use case.

#### session.onStreamConnected:AtomicEmitter
Emitted when the shoe stream is connected to the server. This will also be emitted when the stream reconnects after the user was offline.

#### session.onLogin:AtomicEmitter
Emitted after the user was successfully authenticated.

#### session.onceLoggedIn(cb:Function)
This is a helper function that allows you defer code until the user logged in. If the user is logged in already at call time, this will call the callback immediately using `setImmediate`.

### api
Package: `hive-ui`

This is redux middle-ware that integrates `hive-client-rest-api`. Dispatch the below actions to talk to the API in the name of the logged-in user. The names of the action creators should be pretty self-explanatory.


#### api.action_authenticate(method, credentials, scope):API_AUTHENTICATE
This action is used internally by the session component.

#### api.action_user_create(attributes:Object): API_USER_CREATE
#### api.action_user_get(id:Number): API_USER_GET
#### api.action_user_update(id:Number, attributes:Object): API_USER_UPDATE
#### api.action_user_delete(id:Number): API_USER_DELETE
#### api.action_user_getDocuments(id:Number): API_USER_GET_DOCUMENTS
#### api.action_user_getSnapshots(id:Number): API_USER_GET_SNAPSHOTS
#### api.action_document_create(attributes:Object): API_DOCUMENT_CREATE
#### api.action_document_get(id:Number): API_DOCUMENT_GET
#### api.action_document_update(id:Number, attributes:Object): API_DOCUMENT_UPDATE
#### api.action_document_delete(id:Number): API_DOCUMENT_DELETE
#### api.action_document_getSnapshots(id:Number): API_DOCUMENT_GET_SNAPSHOTS
#### api.action_document_getSnapshotsSince(id, since): API_DOCUMENT_GET_SNAPSHOTS_SINCE
#### api.action_document_change(id:Number, changes:String, parent:Number): API_DOCUMENT_CHANGE
(`changes` must be a changeset as required by the document's ottype, serialized with `JSON.stringify`)
#### api.action_snapshot_get(id:Number): API_SNAPSHOT_GET

### editor
Package: `hive-ui`

#### editor.registerEditor(name:String, type:String, description:String, editoreditorSetup:Function(editor:Element))
Registers an editor called `name` for a given ot `type` with a `description`. The `editorSetup` function should load the editor and append any DOM elements as children of the element passed as `editor`. It should return a promise that resolves to a gulf Document. Editor names must be unique and are used for displaying options together with `description`s.

### settings
Package: `hive-ui`

#### settings.action_setForDocument(map:Object): SETTINGS_SET_FOR_DOCUMENT
#### settings.getForDocument(key)
#### settings.action_setForUser(map:Object): SETTINGS_SET_FOR_USER
#### settings.getForUser(key)
#### settings.action_setForUserDocument(map:Object): SETTINGS_SET_FOR_USER
#### settings.getForUserDocument(key)
#### settings.action_setView(view): SETTINGS_SET_VIEW
#### settings.onRenderUserSettings:(children:Array)
This is an event emitter. Use it to display your own user settings by adding virtual-dom trees to `children`. Save changes by dispatchting the above actions.
#### settings.onRenderDocumentSettings:(children:Array)
This is an event emitter. Use it to display your own document settings by adding virtual-dom trees to `children`. Save changes by dispatchting the above actions.
#### settings.onRenderUserDocumentSettings:(children:Array)
This is an event emitter Use it to display your own personalized document settings by adding virtual-dom trees to `children`. Save changes by dispatchting the above actions.

### importexport
Package: `hive-ui`

#### action_toggleExportDropdown() : IMPORTEXPORT_TOGGLE_EXPORT_DROPDOWN
#### action_export(exportType:String) : IMPORTEXPORT_EXPORTED
#### action_exporting(type:String)
#### action_toggleImportDropdown() : IMPORTEXPORT_TOGGLE_IMPORT_DROPDOWN
#### action_import(files:FileList) : IMPORTEXPORT_IMPORTED
#### action_importing(filename:String) : IMPORTEXPORT_IMPORTING