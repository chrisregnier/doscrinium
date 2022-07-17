# Architecture
Uses docker to deploy all services locally

## frontend
The frontend is react and using redux for state management. The frontend is served with an nginx proxy.

## backend
The backend is a java spring boot server with REST apis.

### queries 
  - getMe() \
      get the details of currently logged in user
  - getUserById(ids: \[ID!\]!) \[User\]! \
      get the details of the specified user
  - getFolderById(ids: \[ID!\]!) \[Folder\]! \
      get the folder and ids of children 
  - findFoldersByPath(path: String!) \[Folder\]! \
      search for a bunch of folders that match the search path. use '*' for wildcards
  - getFileById(ids: \[ID!\]!) \[File\]! \
      gets the details for specified file
  - getFileContentsById(id: ID!) Binary \
      gets the contents of the file

### mutations
  - createUser(...) User! \
      creates a new User
  - updateUser(userId: ID!, ...) User! \
      updates a user
  - deleteUser(ids: \[ID!\]!) \[Boolean\]! \
      deletes a user
  - addFolder(folderId: ID!, name: String!) Folder! \
      add a new folder to the parent folder
  - renameFolder(folderId: ID!, name: String!) Folder! \
      renames the specified folder
  - deleteFolder(ids: \[ID!\]!) \[Boolean\]! \
      deletes a folder and all its subfolders and files
  - addFile(folderId: ID!, name: String!, data: Binary) File! \
      add a new file to a folder
  - renameFile(fileId: ID!, name: String!) File! \
      rename the specified file
  - updateFileContents(fileId: ID!, data: Binary) \
      update the contents of the given file
  - deleteFile(ids: \[ID!\]!) \[Boolean\]! \
      deletes a file

### APIs

- GET `/me`
  - gets the details for the currently logged in user
- GET `/users/{ids}/`
  - gets the details for the specified user
- POST `/users/`
  - creates a new user
- POST `/users/{id}`
  - sets the details for the specified user
- DELETE `/users/{ids}`
  - deletes the specified user
- GET `/folders/{ids}/`
  - gets the details for the specified folder, includes child ids
- GET `/folders/?path={string}`
  - searches for folders that match the given path. path can use '*' for wildcard searches
- POST `/folders/{id}/folder`
  - adds a new folder to the given parent folder. use id `root` to add a root folder
- DELETE `/folders/{ids}`
  - deletes the specified folder and all its subfolders and files
- GET `/files/{ids}/`
  - gets the details of the specified file
- POST `/files/{id}/`
  - sets the details of the specified files
- GET `/files/{id}/content`
  - gets the actual file content for the specified file
- POST `/files/{id}/content`
  - sets the actual file content for the specified file
- POST `/files/?folder={id}?name={string}`
  - creates a new file in the specified folder
- DELETE `/files/{ids}`
  - deletes the specified files

## database
MariaDB is used to provide a relational DB to store file tree data.

The Folders table tracks the recursive tree paths for folders but we add a full path field to denormalize the folders and allow direct lookups instead of using recursively querying the next folder. The downside is the full folder path length is limited to the size of a string, and any renaming of folders causes a bunch of writes to all folders in the tree to update their denormalized path. Since renames don't happen as often as lookups of a folder, this should be ok, but the path length is definitely a limitation.

### Tables
```
Users {
    id: ID
    name: String
    email: String
    created_at: Timestamp
    updated_at: Timestamp
}
```
```
Folders {
    id: ID
    parent: Folders.ID
    name: String
    path: String
    owner: Users.id
    created_at: Timestamp
    updated_at: Timestamp
}
```
```
Files {
    id: ID
    guid: UUID
    name: String
    folder: Folders.ID
    owner: Users.ID
    created_at: Timestamp
    updated_at: Timestamp
}
```

## storage
Uses minio as a local S3 storage system.
Files are stored in a flat structure with a uuid for a name which matches the uuid in the database 'files' table.
