# Interface: REST API

## Authorization
For authentication purposes either include an `access_token` query parameter carrying the access token or set the `Authorization` header to `token dfg6wdg89wd65...`

All methods will return a 401 status code if you failed to provide an access  token, a 403 status code if the method exceeds the scope of your access token.

## Methods

### Create a document
```
POST /api/v1/documents
{"type": "text/plain"}
```
```
200 OK
{"id": 2, "type": "text/plain", "firstSnapshot": "e8fg85erg56erg"}
```

### Get a document
```
GET /api/v1/documents/2
```
```
200 OK
{"id": 2, "type": "text/plain", "firstSnapshot": "e8fg85erg56erg"}
```

### Modifying a document's attributes
```
PUT /api/v1/documents/2
{"settings": {"editor:editor": "CodeMirror"}}
```
```
200 OK
{"id": 2, "type": "text/plain", "firstSnapshot": "e8fg85erg56erg", "settings": {...}}
```

### Delete a document
```
DELETE /api/v1/documents/2
```
```
200 OK
{"message": "ok"}
```

### Change a document's contents
```
POST /api/v1/documents/2/snapshots
{"parent": "658deg654erh", "changes": "..."}
```
```
200 OK
{"id": "9834zf9obv", "parent": "658deg654erh", "contents": "hello world\n", "changes": "..."}
```

### Import a file
(Appends to the end of the document. Attachments must not be larger than 8MiB!)

```
POST /api/v1/documents/2/import
```
```
200 OK
{"message": "ok"}
```

### Get a document's authors
```
GET /api/v1/documents/2/authors
```
```
200 OK
[{"id": 17, "name": "foo"}, {"id": 678, "name": "bar"}]
```

### Get a document's snapshots
```
GET /api/v1/documents/2/snapshots
```
```
200 OK
[{"id": "9834zf9obv", "parent": "658deg654erh", "contents": "hello world\n", "changes": "..."}, ...]
```

### Get all snapshots of a document after a certain revision
```
GET /api/v1/documents/2/snapshots?since=658deg654erh
```
```
200 OK
[{"id": "9834zf9obv", "parent": "658deg654erh", "contents": "hello world\n", "changes": "..."}, ...]
```

### Create a user
```
POST /api/v1/users
{"name": "marcel", "type": "github", "foreignId": "marcelklehr"}
```
```
200 OK
{"id": 17, "name": "marcel", "type": "github", "foreignId": "marcelklehr"}
```

### Get a user
```
GET /api/v1/users/17
```
```
200 OK
{"id": 17, "name": "marcel", "type": "github", "foreignId": "marcelklehr"}
```
### Manipulate a user's attributes
```
PUT /users/17
{"name": "marcelklehr"}
```
```
200 OK
{"id": 17, "name": "marcelklehr", "type": "github", "foreignId": "marcelklehr"}
```

### Delete a user
```
DELETE /api/v1/users/17
```
```
200 OK
{"message": "ok"}
```

### Get documents a user has edited
```
GET /api/v1/users/17/documents
```
```
200 OK
[{"id": 2, "type": "text/plain", "firstSnapshot": "e8fg85erg56erg"}, ...]
```

### Get all snapshots a user has authored
```
GET /api/v1/users/17/snapshots
```
```
200 OK
[{"id": "9834zf9obv", "parent": "658deg654erh", "contents": "hello world\n", "changes": "..."}, ...]
```

### Get a snapshot
```
GET /api/v1/snapshots/9834zf9obv
```
```
200 OK
{"id": "9834zf9obv", "document": 2, "parent": "658deg654erh", "contents": "hello world\n", "changes": "..."}
```

### Export a snapshot
```
GET /api/v1/snapshot/9834zf9obv/export?type=text/plain
```
```
200 OK
hello world\n
```
