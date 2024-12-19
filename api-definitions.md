# FluxManager
## Flux
```ts
interface FluxModel {
    uid: string, //uuid
    name: string,
    owner: string, //group
    value: string, //json with configuration
    resourceDependencies: string[], //resources paths,
    lastEdited: Date //use a timestamp for http
}
```

### GET /flux?owner={group}
* Access: user

`--`
```ts
interface FluxListDTO {
    values: {uid:string, name:string, lastEdited:number}[]
}
```

### GET /flux/{uid}
* Access: user + worker manager

`--`
```ts
interface SingleFluxDTO {
    uid: string,
    name: string,
    owner: string, 
    value: string,
    resourceDependencies: string[],
    lastEdited: number
}
```

### POST /flux
* Access: user

```ts
interface CreateFluxDTO {
    owner: string,
    name: string
}
```
`--`
```ts
interface FluxCreatedDTO {
    uid: string
}
```


### PUT /flux/{uid}
* Access: user

```ts
interface EditFluxDTO {
    name: string,
    value: string,
    resourceDependencies: string[],
}
```
`--`

### DELETE /flux/{iud}
* Access: user

`--`

## Resource
A file (picture, audio, video, font, etc.)\
We may filter type\
-> Minio bucket, prefixed with `/res/{group}/`

### GET /res/{group}/{path}
* Access: user

`--`\
*Returns the file*
### PUT /res/{group}/{path}
* Access: user

*The body is the file*\
`--`
### DELETE /res/{group}/{path}
* Access: user

`--`

# WorkerManager
## Worker
```ts
interface WorkerModel {
    uid: string,
    owner: string,
    lastUpdate: Date, //last configuration update
    configurationValue: string,
    uri: string, //worker url
    apiKey?: string, //worker API Key
    previewUri?: string, //worker preview url
    previewKey?: string, //worker preview key
    packageKey?: string, //null if no resourceDependencies in flux
}
```

### GET /worker?owner={group}
* Access: user

`--`
```ts
interface WorkerListDTO{
    values: {uid:string, lastUpdate:number, uri:string}[]
}
```

### GET /worker/{uid}
* Access: user

`--`
```ts
interface SingleWorkerDTO{
    uid: string,
    owner: string,
    lastUpdate: number,
    configurationValue: string,
    previewUri?: string,
    previewKey?: string
}
```

### GET /worker/{uid}/package?key={key}
* Access: workerAPI (key should be equal to the worker's packageKey)

`--`\
*Returns the packaged resources (see `PUT /worker/{uid}/flux`)*

### POST /worker
* Access: user

```ts
interface CreateWorkerDTO{
    owner: string,
    uri: string,
    apiKey?: string,
    previewUri?: string,
    previewKey?: string,
}
```
`--`

### PUT /worker/{uid}
* Access: user
```ts
interface EditWorkerDTO{
    apiKey?: string,
    previewUri?: string,
    previewKey?: string,
}
```
`--`

### PUT /worker/{uid}/flux
* Access: user
* Special actions: 
  * changes configurationValue from the flux manager API
  * check all resourcesDependencies,\
    if one is older than lastUpdate, zip dependencies in MinIO\
    (not the same prefix as /res)
  * sends new configurationValue to the Worker, along with the package endpoint and key

```ts
interface CommitFluxDTO{
    fluxId: string
}
```
`--`

### DELETE /worker/{uid}
* Access: user

`--`

# User Manager
## User
```ts
interface UserModel {
    login: string,
    creationDate: Date,
    email: string,
    groupUid : uid
}
```

### GET /user/
```ts
interface UserListDTO{
    values: {login: string, creationDate: Date, email: string, groupUid : uid}[]
}
```

### GET /user/{login}
```ts
interface UserDTO{
    login: string,
    creationDate: Date,
    email: string,
    groupUid : uid
}
```

### POST /user/
```ts
interface UserDTO{
    login: string,
    email: string,
    password: string
}
```
### Delete /user/{login}


## Group

```ts
interface GroupModel {
    uid: string,
    name: string,
    users: User[]
}
```

### GET /group/
```ts
interface groupListDTO{
    values: {uid: string, name: string,users: string[]}[]
}
```

### GET /group/{uid}
```ts
interface GroupDTO{
    uid: string,
    name: string,
    users: string[]
}
```

### POST /group/
```ts
interface GroupDTO{
    uid: string,
    name: string
}
```

### POST /group/{uid}/addUser/{login}

### POST /group/{uid}/deleteUser/{login}

### Delete /group/{uid}


# Worker API (.NET)
### GET /health
`--`

*health info*

### PUT /update
```ts
interface UpdateWorkerDTO{
    configuration: string, //JSON
    packageUri: string, //URL of package to download
    packageKey: string //Key for getting the zip
}
```
`--`

### GET /previewInfos
`--`
```ts
interface PreviewInfosDTO{
    previewUri?: string,
    previewKey?: string
}
```
