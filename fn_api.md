# Memoir API Function Detailed Design

The API Function will be called from the API Gateway. It will be responsible
for processing client requests.

For a local service this is ran under the local service. For AWS it would
represent a Lambda function.

## Inputs / Outputs

Inputs: Uses GraphQL requests. Refer to the [GraphQL Schema](graphql.schema).

Outputs: Produces a JSON response.

The API Gateway should handle access to thumbnail images.
This API will have s3 interface generate a presigned URL for downloading
data.

## Add Album / Media

```mermaid
sequenceDiagram
    actor C as Client
    participant A as API
    participant M as Metadata
    participant S as s3
    participant O as Other Functions

    C->>A: add_album(title: "Alb1")
    A->>M: insert new album
    M-->>A: Ok
    A-->>C: {id="1"}
    loop for each media item
        C->>A: add_media(parentId: "1", sha256: "234...", filename:"y.jpg", mimeType="image/jpeg")
        A->>M: insert new media entry and associates it with album
        M-->>A: Ok
        A->>S: gen presigned upload url
        S-->>A: presigned upload url w/Expiry
        A-->>C: {id: "2", upload_url: "<url>"}
        C-)S: uploads media using upload_url
        S-)O: triggers other post process functions...
        O-)M: update with analyzed attributes
    end
```

NOTE: _not showing security checks or error handling_

## Client Possible Starting Scenario

```mermaid
sequenceDiagram
    actor C as Client
    participant A as API
    participant M as Metadata
    participant S as s3

    note over C,S: Get Root Albums and Folders
    C->>A: collections(depth=0)
    A->>M: query for root collection that requestor is allowed to see
    M-->>A: [results]
    A-->>C: [results]
    C->>C: Shows Album/Folder UI

    note over C,S: Get Specific Folder Information
    C->>C: Navigates down to Folder "Y"
    C->>A: collections(inPath: "<ID for Y>")
    A->>M: query for folder/albums w/id in path and requestor is allowed to see
    M-->>A: [results]
    A-->>C: [results]
    C->>C: Shows Album/Folder Tree in UI

    note over C,S: Get Album Information
    C->>C: Selects Album "Q"
    C->>A: album_from_id(id:"<ID for Q>")
    A->>M: get album information if allowed
    M-->>A: results
    A->>A: Map thumbnails to API Gateway url's
    A-->>C: Results

    note over C,S: Get Image for "Z"
    C->>A: media_from_id(id: "<ID for Z>")
    A->>M: get media info for that ID if allowed
    M-->>A: results
    A->>S: create presigned download url w/Expiry
    S-->>A: download url
    A-->>C: Media info with download url and expiry
    C-)S: Downloads media
```
