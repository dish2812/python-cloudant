# Migrating to the `cloudant-python-sdk` library 
This document is to assist in migrating from the legacy `python-cloudant` to the newly supported `cloudant-python-sdk`.

## Initializing the client connection
There are several ways to create a client connection in `cloudant-python-sdk`:
1. [Enviornment variables](https://github.com/IBM/cloudant-python-sdk#authenticate-with-environment-variables)
2. [External configuration file](https://github.com/IBM/cloudant-python-sdk#authenticate-with-external-configuration)
3. [Programmatically](https://github.com/IBM/cloudant-python-sdk#authenticate-programmatically)

[See the README](https://github.com/IBM/cloudant-python-sdk#1-retrieve-information-from-an-existing-database) for code examples on using enviornment variables.

## Dictionary cache
The `cloudant-python-sdk` does not support local dictionary caching of database and document objects.
For example, the `python-cloudant` code below would get and modify the `docid` document:
```python
service = Cloudant(...)
db = service['example_db']
doc = db['docid']
# get document from remote db
doc.fetch()
# add new field and value
doc['newfield'] = 'newvalue'
# update document in remote db and update latest revision in local cache
doc.save()
```
The same example for `cloudant-python-sdk` is shown below:
```python
service = CloudantV1.new_instance()
db_name = 'example_db'
doc_id = 'docid'
# get document from remote db
doc = service.get_document(
    db=db_name,
    doc_id=doc_id
).get_result()
# add new field and value
doc['newfield'] = 'newvalue'
# update document in remote db
service.post_document(
    db=db_name,
    document=doc
)
# get updated document with latest revision from remote db
doc = service.get_document(
    db=db_name,
    doc_id=doc_id
).get_result()
```
You'll notice that `cloudant-python-sdk` requires an additional `get_document` call to get the latest revision.

The [README in the repo contains examples of how to create a database and a document](https://github.com/IBM/cloudant-python-sdk#2-create-your-own-database-and-add-a-document).

## Other differences
1. There's no context manager feature.  You'll need to create the client, connect to the database, fetch and save
a document as separate commands.
1. Cloudant View, Search, and Query (aka `_find` endpoint) operations will contain raw JSON response content.  

## Request mapping
Here's a short list of which new functions to use for common operations:
| Original operation  | New SDK operation                 |
|---------------------|-----------------------------------|
| `db.exists()`       | `head_database`                   |
| `doc.exists()`      | `head_document`                   |
| `create_document`   | `put_document` OR `post_document` |
| `new_document`      | `put_document` OR `post_document` |
| `get_view_result`   | `post_view`                       |
| `get_search_result` | `post_search`                     |
| `get_query_result`  | `post_find`                       |

[We've provided a table]() that links `cloudant-python-sdk` operations with `python-cloudant` functions.

We've also introduced several endpoints that were not supported in `python-cloudant`.  Here's a few:
- WIP

## Known Issues
There's an [outline of known issues](https://github.com/IBM/cloudant-python-sdk/blob/master/KNOWN_ISSUES.md) in the `cloudant-python-sdk` repository.
