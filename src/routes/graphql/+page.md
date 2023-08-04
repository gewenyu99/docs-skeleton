Appwrite supports multiple protocols for accessing the server, including [REST](/docs/rest), [GraphQL](/docs/graphql), and [Realtime](/docs/realtime).

The GraphQL API allows you to query and mutate any resource type on your Appwrite server through the endpoint `/v1/graphql`. Every endpoint available through REST is available through GraphQL, except for OAuth.

## [Requests](/docs/graphql#requests)

Although every query executes through the same endpoint, there are multiple ways to make a GraphQL request. All requests, however, share a common structure.

```html
<table class="vertical full args">
    <thead>
    <tr>
        <td style="width: 140px">Name</td>
        <td style="width: 70px"></td>
        <td style="width: 120px">Type</td>
        <td>Description</td>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td data-title="Name: ">
            query
        </td>
        <td data-title="">
            <span class="tag red">required</span>
        </td>
        <td data-title="Type: ">
            string
        </td>
        <td data-title="Description: ">
            The GraphQL query to execute.
        </td>
    </tr>
    <tr>
        <td data-title="Name: ">
            operationName
        </td>
        <td data-title="">
            <span class="tag">optional</span>
        </td>
        <td data-title="Type: ">
            string
        </td>
        <td data-title="Description: ">
            If the query contains several named operations, controls which one to execute.
        </td>
    </tr>
    <tr>
        <td data-title="Name: ">
            variables
        </td>
        <td data-title="">
            <span class="tag">optional</span>
        </td>
        <td data-title="Type: ">
            object
        </td>
        <td data-title="Description: ">
            An object containing variable names and values for the query. Variables are made available to your query with the <code>$</code> prefix.
        </td>
    </tr>
    </tbody>
</table>
```

```html
<div class="notice">
  <h2>GraphQL Model Parameters</h2>

  <p>In Appwrite's GraphQL API, all internal model parameters are prefixed with <code>_</code> instead of <code>$</code> because <code>$</code> is reserved by GraphQL.</p>

  <p>For example, <code>$collectionId</code> in the REST API would be referenced as <code>_collectionId</code> in the GraphQL API.</p>

</div>
```

### [GET Requests](/docs/graphql#get-requests)

You can execute a GraphQL query via a GET request, passing a `query` and optionally `operationName` and `variables` as query parameters.

### [POST Requests](/docs/graphql#post-requests)

There are multiple ways to make a GraphQL POST request, differentiated by content type.

#### JSON

There are two ways to make requests with the `application/json` content type. You can send a JSON object containing a `query` and optionally `operationName` and `variables`, or an array of objects with the same structure.

```json
{
    "query": "",
    "operationName": "",
    "variables": {}
}
```

```json
[
    {
        "query": "",
        "operationName": "",
        "variables": {}
    }
]
```

#### GraphQL

The `application/graphql` content type can be used to send a query as the raw POST body.

```graphql
query GetAccount {
    accountGet {
        _id
        email
    }
}
```

#### Multipart Form Data

The `multipart/form-data` content type can be used to upload files via GraphQL. In this case, the form data must include the following parts in addition to the files to upload:

```html
<table class="vertical full args">
    <thead>
    <tr>
        <td style="width: 140px">Name</td>
        <td style="width: 70px"></td>
        <td style="width: 120px">Type</td>
        <td>Description</td>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td data-title="Name: ">
            operations
        </td>
        <td data-title="">
            <span class="tag red">required</span>
        </td>
        <td data-title="Type: ">
            string
        </td>
        <td data-title="Description: ">
            JSON encoded GraphQL query and optionally operation name and variables. File variables should contain null values.
        </td>
    </tr>
    <tr>
        <td data-title="Name: ">
            map
        </td>
        <td data-title="">
            <span class="tag red">required</span>
        </td>
        <td data-title="Type: ">
            string
        </td>
        <td data-title="Description: ">
            JSON encoded map of form-data filenames to the operations dot-path to inject the file to, e.g. <code>variables.file</code>.
        </td>
    </tr>
    </tbody>
</table>
```

## [Responses](/docs/graphql#responses)

A response to a GraphQL request will have the following structure:

```html
<table class="vertical full args">
    <thead>
    <tr>
        <td style="width: 140px">Name</td>
        <td style="width: 120px">Type</td>
        <td>Description</td>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td data-title="Name: ">
            data
        </td>
        <td data-title="Type: ">
            object
        </td>
        <td data-title="Description: ">
            The data returned by the query, maps requested field names to their results.
        </td>
    </tr>
    <tr>
        <td data-title="Name: ">
            errors
        </td>
        <td data-title="Type: ">
            object[]
        </td>
        <td data-title="Description: ">
            An array of errors that occurred during the request.
        </td>
    </tr>
    </tbody>
</table>
```

The data object will contain a map of requested field names to their results. If no data is returned, the object will not be present in the response.

The errors array will contain error objects, each with their own **message** and **path**. The path will contain the field key that is null due to the error. If no errors occur, the array will not be present in the response.

## [Authentication](

/docs/graphql#authentication)

Just like with the REST API, you can authenticate GraphQL requests with Appwrite accounts and sessions.

## [Advantages of GraphQL](/docs/graphql#advantages)

GraphQL provides a number of advantages over a REST API, including:

- **Selection Sets**: GraphQL queries contain selection sets, which are a list of fields that you want to retrieve. With selection sets, you ensure that you are only sent the data you request.

- **Query Batching**: You can send multiple queries to the server in a single request.

An example of a GraphQL query is given below:

```graphql
query GetAccount {
    accountGet {
        _id
        email
    }
}
```

This can be a useful feature for performance, network efficiency, and app responsiveness. As the processing happens on the server, the bandwidth consumed for the request can be dramatically reduced.

### [Query Batching](/docs/graphql#query-batching)

GraphQL allows sending multiple queries or mutations in the same request. There are two different ways to batch queries. The simplest way is to include multiple fields in a single query **or** mutation.

```graphql
query GetAccountAndLocale {
    accountGet {
        _id
        email
    }
    localeGet {
        ip
    }
}
```

If both field executions succeed, the response will contain a data key for each field, containing the values of the selected fields.

```json
{
    "data": {
        "accountGet": {
            "_id": "...",
            "email": "..."
        },
        "localeGet": {
            "ip": "..."
        }
    }
}
```

If there was no authenticated user, the `accountGet` field would fail to resolve. In such a case the value of the data key for that field will be null, and an object will be added to the errors array instead.

```json
{
    "data": {
        "accountGet": null,
        "localeGet": {
            "ip": "...",
            "country": "..."
        }
    },
    "errors": [
        {
            "message": "User (role: guest) missing scope (account)",
            "path": ["accountGet"]
        }
    ]
}
```

Batching with a single query or mutation has some down-sides. You can not mix and match queries and mutations within the same request unless you provide an operationName, in which case you can only execute one query per request.

Additionally, all **variables** must be passed in the same object, which can be cumbersome and hard to maintain.

The second way to batch is to pass an array of queries or mutations in the request. In this way, you can execute queries **and** mutations and keep variables separated for each.

```json
[
    {
        "query": "query GetAccount { accountGet{ email } }",
    },
    {
        "query": "query GetLocale { localeGet { ip } }"
    }
]
```

This allows you to execute complex actions in a single network request.

### [SDK Usage](/docs/graphql#sdk-usage)

Appwrite SDKs also support GraphQL in addition to the REST services.

- **Web**

```javascript
import { Client, Graphql } from "appwrite";

const client = new Client()
    .setEndpoint('https://cloud.appwrite.io/v1') // Your Appwrite Endpoint
    .setProject('[PROJECT_ID]');                // Your project ID

const graphql = new Graphql(client);

const mutation = graphql.mutation({
    query: `mutation CreateAccount(
        $email: String!,
        $password: String!,
        $name: String
    ) {
        accountCreate(
            email: $email,
            password: $password,
            name: $name,
            userId: "unique()"
        ) {
            _id
        }
    }`,
    variables: {
        email: '...',
        password: '...',
        name: '...'
    }
});

mutation.then(response => {
    console.log(response);
}).catch(error => {
    console.log(error);
});
```

- **Flutter**

```dart
import 'package:appwrite/appwrite.dart';

final client = Client()
    .setEndpoint('https://cloud.appwrite.io/v1') // Your Appwrite Endpoint
    .setProject('[PROJECT_ID]');               // Your project ID

final graphql = Graphql(client);

Future mutation = graphql.mutation({
    'query': '''mutation CreateAccount(
        \$email: String!,
        \$password: String!,
        \$name: String
    ) {
        accountCreate(
            email: \$email,
            password: \$password,
            name: \$name,
            userId: "unique()"
        ) {
            _id
        }
    }''',
    'variables': {
        'email': '...',
        'password': '...',
        'name': '...'
    }
});

mutation.then((response) {
    print(response);
}).catchError((error) {
    print(error.message);
});
```

- **Android**

```kotlin
import io.appwrite.Client
import io.appwrite.services.Graphql

val client = Client(context)
    .setEndpoint("https://cloud.appwrite.io/v1") // Your API Endpoint
    .setProject("[PROJECT_ID]")                // Your project ID

val graphql = Graphql(client)

try {
    val response = graphql.mutation(mapOf(
        "query" to """mutation CreateAccount(
            ${'$'}email: String!,
            ${'$'}password: String!,
            ${'$'}name: String
        ) {
            accountCreate(
                email: ${'$'}email,
                password: ${'$'}password,
                name: ${'$'}name,
                userId: "unique()"
            ) {
                _id
            }
        }""",
        "variables" to mapOf(
            "email" to "...",
            "password" to "...",
            "name" to "..."
        )
    ))

    Log.d(javaClass.name, response)
} catch (ex: AppwriteException) {
    ex.printStackTrace()
}
```

- **Apple**

```swift
import Appwrite

let client = Client()
    .setEndpoint("https://cloud.appwrite.io/v1") // Your API Endpoint
    .setProject("[PROJECT_ID]")                // Your project ID

let graphql = Graphql(client)

do {
    let response = try await graphql.mutation([
        "query": """
            mutation CreateAccount(
                $email: String!,
                $password: String!,
                $name: String
            ) {
                accountCreate(
                    email: $email,
                    password: $password,
                    name: $name,
                    userId: "unique()"
                ) {
                    _id
                }
            }
        """,
        "variables": [
            "email": "...",
            "password": "...",
            "name": "..."
        ]
    ])

    print(String(describing: response))
} catch {
    print(error.localizedDescription)
}
```