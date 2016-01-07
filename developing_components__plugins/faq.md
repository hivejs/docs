# FAQ

## Provider interfaces, delegators, the hooks provider -- when to use what?
When the time has come and you want to outsource some of your functionality, you have the choice between provider interfaces, delegators and the global hooks provider. Now, which do you use?

### Provider interface
If your component depends on the functionality of the outsourced code, e.g. you need its return value, then that's a clear sign that you need to define an interface that an external provider will implement.

An example of this is the broadcast interface, implemented e.g. by Hive.js core component `hive-broadcast-memory`.

### Delegator
If you want to be able to plug in more than one component (e.g. different authentication providers should be supported), you can create a provider that allows to register delegates, and calls out to the delegates to execute the task or provide the requested functionality  for it, combining their output if necessary.

An example for a delegating provider is the `auth` provider implemented by Hive.js core component `hive-auth`: Authentication providers are to be named `auth-` and register with the auth provider *providing* an authentication method.

#### Hooking
If your component does not directly depend on the functionality of outsourced parts, use the hook system -- just call a hook with a few arguments and be done with it.

The difference between a hook and an event listener is that you will want to wait for the hook to finish executing, while with events it's just "fire and forget".