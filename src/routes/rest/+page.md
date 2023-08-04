---
title: REST API
description: How do I use Appwrite without SDKs? How do I use Appwrite with REST API. How do I use Appwrite with HTTP.
---

Appwrite supports multiple protocols for accessing the server, including [REST](/docs/rest), [GraphQL](/docs/graphql), and [Realtime](/docs/realtime).

The REST API allows you to access your Appwrite server through HTTP requests without the needing an SDK. Each endpoint in the API represents a specific operation on a specific resource.

## [Headers](#headers)

Appwrite's REST APIs expect certain headers to be included with each request:

| Header                                                        |         | Description                                                                                                                   |
|--------------------------------------------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------|
| `X-Appwrite-Project: [PROJECT-ID]`                           | required | The ID of your Appwrite project                                                                                               |
| `Content-Type: application/json`                             | required | Content type of the HTTP request.                                                                                             |
| `X-Appwrite-Key: [API-KEY]`                                  | optional | API key used for server authentication. **Do not use API keys in client applications.**                                       |
| `X-Appwrite-JWT: [TOKEN]`                                    | optional | Token used for JWT authentication, tokens can be generated using the [Create JWT](/docs/client/account#accountCreateJWT) endpoint. |
| `X-Appwrite-Response-Format: [VERSION-NUMBER]`               | optional | Version number used for backward compatibility. The response will be formatted to be compatible with the provided version number. |
| `X-Fallback-Cookies: [FALLBACK-COOKIES]`                     | optional | Fallback cookies used in scenarios where browsers do not allow third-party cookies. Often used when there is no [Custom Domain](/docs/custom-domains). |


```
## [Using Appwrite Without Headers](#no-headers)

Some use cases do not allow custom headers, such as embedding images from Appwrite in HTML. In these cases, you can provide the Appwrite project ID using the query parameter `project`.

```html
<?php echo htmlentities('<img src="https://cloud.appwrite.io/v1/storage/buckets/[BUCKET_ID]/files/[FILE_ID]/preview?project=[PROJECT_ID]">'); ?>
```

## [Client Authentication](#client-auth)

You can create account sessions with POST requests to the [Account API](/docs/client/account). Sessions are persisted using secured cookies. You can learn more about session persistence in the [Authentication Guide](/docs/authentication#persistence).

The example below shows creating an account session with the [Create Account Session with Email](/docs/client/account#accountCreateEmailSession) endpoint.

```http
POST /v1/account/sessions/email HTTP/1.1
Content-Type: application/json
X-Appwrite-Project: [PROJECT_ID]

{
  "email": "example@email.com",
  "password": "password"
}
```

You can find the cookies used to persist the new session in the response headers.

```http
Set-Cookie: a_session_61e71ec784ab035f7259_legacy=eyJ0...aSJ9; expires=Tue, 19-Dec-2023 21:26:51 GMT; path=/; domain=.cloud.appwrite.io; secure; httponly
Set-Cookie: a_session_61e71ec784ab035f7259=eyJ0...aSJ9; expires=Tue, 19-Dec-2023 21:26:51 GMT; path=/; domain=.cloud.appwrite.io; secure; httponly; samesite=None
```

These cookies are used in subsequent requests to authenticate the user.

```http
GET /v1/account HTTP/1.1
Cookie: a_session_61e71ec784ab035f7259_legacy=eyJ0...aSJ9; a_session_61e71ec784ab035f7259=eyJ0...aSJ9
Content-Type: application/json
X-Appwrite-Project: [PROJECT_ID]
```

## [Server Authentication](#server-auth)

Server integrations use API keys to authenticate and are typically used for backend applications.

Server APIs are authenticated with API keys instead of account sessions. Simply pass an [API key](/docs/keys) in the `X-Appwrite-key: [API-KEY]` header with the appropriate scopes.

```http
GET /v1/databases/{databaseId}/collections/{collectionId}/documents HTTP/1.1
Content-Type: application/json
X-Appwrite-Project: [PROJECT_ID]
X-Appwrite-Key: [API_KEY]
```

## [JWT Authentication](#server-auth)

JWT authentication is frequently used by server applications to act on behalf of a user. Users generate tokens using the [Create JWT](/docs/client/account#accountCreateJWT) endpoint. When issuing requests authenticated with a JWT, Appwrite will treat the request like it is from the authenticated user.

```http
GET /v1/account HTTP/1.1
Content-Type: application/json
X-Appwrite-Project: [PROJECT_ID]
X-Appwrite-JWT: [TOKEN]
```

## [File Handling](#file-handling)

Appwrite implements resumable, chunked uploads for files larger than 5MB. Chunked uploads send files in chunks of 5MB to reduce memory footprint and increase resilience when handling large files. Appwrite SDKs will automatically handle chunked uploads, but it is possible to implement this with the REST API directly.

Upload endpoints in Appwrite, such as [Create File](/docs/client/storage#storageCreateFile) and [Create Deployment](/docs/server/functions#functionsCreateDeployment), are different from other endpoints. These endpoints take multipart form data instead of JSON data. To implement chunked uploads, you will need to implement the following headers:

| Header | Required | Description |
|--------|---------|-------------|
| `X-Appwrite-Project: [PROJECT-ID]` | required | Contains the ID of your Appwrite Project to the REST API. |
| `Content-Type: multipart/form-data; boundary=[FORM-BOUNDARY]` | required | Contains the content type of the HTTP request and provides a [boundary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST) that is used to parse the form data. |
| `Content-Range: bytes [BYTE-RANGE]` | required | Contains information about which bytes are being transmitted in this chunk, with the format `[FIRST-BYTE]-[LAST-BYTE]/[TOTAL-BYTES]`. |
| `X-Appwrite-ID: [FILE-ID]` | required | Contains ID of the file this chunk belongs to. |
| `X-Appwrite-Key: [API-KEY]` | optional | Used for authentication in server integrations. **Do not use API keys in client applications.** |

The multipart form data is structured as follows:

| Key | Required | Value | File Name | Description |
|-----|----------|-------|-----------|-------------|
| fileId | optional | `[FILE-ID]` | n/a | Contains the file ID of the new file. Only used by file chunks following the first chunk uploaded. |
| file | required | `[CHUNK-DATA]` | `[FILE-NAME]` | Contains file chunk data. |
| permissions | required | `[PERMISSION ARRAY]` | n/a | Contains an array of permission strings about who can access the new file. |


While cURL and fetch are great tools to explore other REST endpoints, it's impractical to use for chunked file uploads because you need to split files into chunks.

The multipart form data posted to file upload endpoints have the following format:

```http
POST /v1/storage/buckets/default/files HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarye0m6iNBQNHlzTpVM
X-Appwrite-Project: demo-project
Content-Range: bytes 10485760-12582912/12582912
X-Appwrite-ID: 6369b0bc1dcf4ff59051

------WebKitFormBoundarye0m6iNBQNHlzTpVM
Content-Disposition: form-data; name="fileId"

unique()
------WebKitFormBoundarye0m6iNBQNHlzTpVM
Content-Disposition: form-data; name="file"; filename="file.txt"
Content-Type: application/octet-stream

[CHUNKED-DATA]
------WebKitFormBoundarye0m6iNBQNHlzTpVM
Content-Disposition: form-data; name="permissions[]"

read("user:627a958ded6424a98a9f")
------WebKitFormBoundarye0m6iNBQNHlzTpVM--
```

## [Permissions](#permissions)

Appwrite SDKs have helpers to generate permission strings, but when using Appwrite without SDKs, you'd need to create the strings yourself.

### Permission Types

SDK | Permission String
----|------------------
`Permission.read()` | `read("[PERMISSION_ROLE]")`
`Permission.create()` | `create("[PERMISSION_ROLE]")`
`Permission.update()` | `update("[PERMISSION_ROLE]")`
`Permission.delete()` | `delete("[PERMISSION_ROLE]")`
`Permission.write()` | `write("[PERMISSION_ROLE]")`

### Permission Roles

SDK | Role String
----|-----------
`Role.any()` | `any`
`Role.guests()` | `guests`
`Role.users()` | `users`
`Role.users([STATUS])` | `users/[STATUS]`
`Role.user([USER_ID])` | `user:[USER_ID]`
`Role.user([USER_ID], [STATUS])` | `user:[USER_ID]/[STATUS]`
`Role.team([TEAM_ID])` | `team:[TEAM_ID]`
`Role.team([TEAM_ID], [ROLE])` | `team:[TEAM_ID]/[ROLE]`
`Role.member([MEMBERSHIP_ID])` | `member:[MEMBERSHIP_ID]`

* [Learn more about permissions](/docs/permissions)

## [Unique ID](#unique-id)

Appwrite's SDKs have a helper `ID.unique()` to generate unique IDs. When using Appwrite without an SDK, pass the string `"unique()"` into the ID parameter.

## [Query Methods](#query)

Appwrite's SDKs provide a `Query` class to generate query strings. When using Appwrite without an SDK, you can template your own strings with the format below.

Query strings are passed to Appwrite using the `queries` parameter. You can attach multiple query strings by including the array parameter multiple times in the query string: `queries[]="..."&queries[]="..."`

Query Method | Query String
-------------|-------------
select | `select([attribute])`
equal | `equal("attribute", [value])`
notEqual | `notEqual("attribute", [value])`
lessThan | `lessThan("attribute", [value])`
lessThanEqual | `lessThanEqual("attribute", [value])`
greaterThan | `greaterThan("attribute", [value])`
greaterThanEqual | `greaterThanEqual("attribute", [value])`
between | `between("attribute", lowerBound, upperBound)`
isNull | `isNull("attribute")`
isNotNull | `isNotNull("attribute")`
startsWith | `startsWith("attribute", [value])`
endsWith | `endsWith("attribute", [value])`
search | `search("attribute", [value])`
orderDesc | `orderDesc("attribute")`
orderAsc | `orderAsc("attribute")`
cursorAfter | `cursorAfter("documentId")`
cursorBefore | `cursorBefore("documentId")`
limit | `limit(0)`
offset | `offset(0)`

> **Best Practice**  
> When using greater than, greater than or equal to, less than, or less than or equal to, it is not recommended to pass in multiple values. While the API will accept multiple values and return results with **or logic**, it's best practice to pass in only one value for performance reasons.

## [Rate Limits](#rate-limits)

Appwrite's REST APIs are protected by the same rate limit policies, just like when using SDKs. Each API has a different rate limit, which is documented in the **References** section of each service in the Appwrite documentation.

* [Learn more about Rate Limits](https://appwrite.io/docs/rate-limits)

## [Stateless](#stateless)

Appwrite's REST APIs are stateless. Each time you make a request, Appwrite receives all the information it needs to perform the action, regardless of what requests you make before and after that request.

Since each requests is stateless, they can be handled in any given order when sent concurrently, as long as they don't make conflicting changes to the same resource.

## [OpenAPI and Swagger Specs](#specs)

Appwrite provides a full REST API specification in the OpenAPI 3 and Swagger 2 formats every release. These can be accessed through Appwrite's GitHub repository and rendered using a variety of parsers and tools.

* [Find the REST API specification for your Appwrite version](https://github.com/appwrite/appwrite/tree/master/app/config/specs)