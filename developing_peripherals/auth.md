# Authentication/Authorization

If you want to access hive.js functionality on behalf of a user, you will need to use the [OAuth protocol](https://tools.ietf.org/html/rfc6749) to obtain an access token, which you can use to authenticate with the hive server.

## tl;dr

In order to obtain an access token, redirect the user's browser to the following URL:

```
https://hive.example.com/authorize?response_type=token&scope=...&redirect_uri=...
```

* `response_type` At the moment hive.js only supports the implicit grant type (aka. `response_type=token`).

* `redirect_uri` The callback URI that the user will be redirected to; should be a https url.
* `scope` A value that is currently specific to the hive.js authorization provider, this may change in future versions; leave out for full access.