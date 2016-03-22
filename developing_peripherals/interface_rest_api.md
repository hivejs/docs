# Interface: REST API

## Authorization
For authentication purposes either include an `access_token` query parameter carrying the access token or set the `Authorization` header to `token dfg6wdg89wd65...`

All methods will return a 401 status code if you failed to provide an access  token, a 403 status code if the method exceeds the scope of your access token.

## Methods
For the full set of Allowed methods, refer to the [JSON API specification v1.0](https://jsonapi.org). Below follow Hive-specific additions and special cases.

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

### Export a snapshot
```
GET /api/v1/snapshot/9834zf9obv/export?type=text/plain
```
```
200 OK
hello world\n
```
