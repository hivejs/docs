# Data model

```
[       User         ]<-->[    Snapshot     ]<-->[      Document             ]
 - id:Number               - id:String              - id:Number
 - name:String             - changes:String         - type:String
 - type:String             - contents:String        - settings:Object
 - foreignId:String        - createdAt              - createdAt
 - settings:Object         - parent:Snapshot        - updatedAt
 - createdAt               - document:Document      - latestSnapshot:Snapshot
 - updatedAt               - author:User            - snapshots:Snapshot[]
 - snapshots:Snapshot[]                             - authors:User[]
 - documents:Document[]
```

## User

### id:Number
A simple id. Nothing special about this.

### name:String
The user's display name. Collisions may occur.

### type:String
The id of the authentication provider that authenticated this user.

### foreignId:String
The id, the authenticating (third-party) service assigned to the user.

### settings:Object
A *flat* object. Prepend the camelCased name of your component for creating your own settings key, e.g. `editorTextCodemirror:lineNumbers`.

## Snapshot
Each snapshot is authored by one user.

### id:String
This is a random string.

### changes:String
The serialized changeset that constitutes this snapshot. For the first snapshot, this will not be set.

### contents:String
The serialized contents of this snapshot. Note that this is not necessarily of the format you'd get when exporting a document (e.g. `text/html` documents store their contents as JSON.stringified virtual-dom trees).

### parent
The parent snapshot. This is not set for the first snapshot.

### document
The document this snapshot belongs to.

### author
The user that authored this snapshot.

## Document

### id:Number
A simple id. Nothing special about this.

### type:String
The id of the ot type necessary for creating and changing this document.

### settings:Object
A *flat* object. Prepend the camelCased name of your component for creating your own settings key, e.g. `editorTextCodemirror:lineNumbers`.