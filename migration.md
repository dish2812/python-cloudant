# Migrating to the `cloudant-python-sdk` library 
This document is to assist in migrating from the legacy `python-cloudant` to the newly supported `cloudant-python-sdk`.

## Differences in the new library

### Initializing the client connection
Creating a client in `cloudant-python-sdk` requires service credentials similar to this library.  You can provide these credentials
using the following methods:
1. [Enviornment variables](https://github.com/IBM/cloudant-python-sdk#authenticate-with-environment-variables)
2. [External configuration file](https://github.com/IBM/cloudant-python-sdk#authenticate-with-external-configuration)
3. [Programmatically](https://github.com/IBM/cloudant-python-sdk#authenticate-programmatically)

[See the README](https://github.com/IBM/cloudant-python-sdk#1-retrieve-information-from-an-existing-database) for an example using enviornment variables.

### Dictionary cache
The `cloudant-python-sdk` does not support locally cached database and documents.  For example, you'll need to manually update
the `_rev` field in any shared local document objects that are updated in the remote database. The [README in the repo contains examples of how to create a database and a document](https://github.com/IBM/cloudant-python-sdk#2-create-your-own-database-and-add-a-document).

### Other differences
1. There's no context manager feature.  You'll need to create the client, connect to the database, fetch and save
a document as separate commands.
1. The `exists` call is not supported.  Use `head_database` to check existance of database or `head_document` to check existance of a document.
1. Cloudant View, Search, and Query (aka `_find` endpoint) operations will contain raw JSON response content.  

### Request mapping
Several functions are either named by their HTTP method or not supported in the `cloudant-python-sdk` library.  Here's a partial list of which new operations to use:
| Original operation  | New SDK operation                 |
|---------------------|-----------------------------------|
| `create_document`   | `put_document` OR `post_document` |
| `new_document`      | `put_document` OR `post_document` |
| `get_view_result`   | `post_view`                       |
| `get_search_result` | `post_search`                     |
| `get_query_result`  | `post_find`                       |


## Known Issues
There's an [outline of known issues](https://github.com/IBM/cloudant-python-sdk/blob/master/KNOWN_ISSUES.md) in the cloudant-python-sdk repository.