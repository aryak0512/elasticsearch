# Elasticsearch

- Disable gatekeeper for embedded JDK in MacOS

```console
aryak@Aryaks-MacBook-Pro Desktop % xattr -d -r com.apple.quarantine /path/to/elastic
```

## Basic terminologies

### Document

A document is a record in JSON format, document can be compared to a tuple in RDBMS

### Index

- An index is a collection of related documents.
- Index can be compared to table in RDBMS.
- In elasticsearch, we always query on indices.

### Node and cluster

Every node in elasticsearch is a member of a cluster by default.

## REST APIs

- Elasticsearch exposes several APIs along with commands
- From Elastic version 8 and above, APIs are exposed over HTTPS ( with simple authentication - xpack) and not HTTP.
- APIs always begin with an underscore [ eg : /_cluster], followed by command [ eg : /health].

```bash
curl -X GET https://localhost:9200/_cluster/health
```

```bash
curl --cacert config/certs/http_ca.crt -u elastic:+mqJn+EXfC5hdcqZkB8z -X GET https://localhost:9200/_cluster/health
```

## Sharding

- Sharding is a concept used for achieving higher availablity and throughput
- Replica shards are never stored on same node
- Once can index is created, the no of shards is final, cannot be altered.
- Max limit of a shard is 2 billion documents.
- Default no of shards for an index is 1.
- Default no of replica per shard is 1.
- ES provides:

  - Split API : for altering number of shards of an index, which under the hood creates a new index with more shards and ES itself does the heavy lifting of migrating the documents to the new index.

  - Shrink API : for reducing the number of shards on an index

## Querying Elasticsearch

### Get all nodes

```bash
GET /_cat/nodes?v
```

### Get all indices

```bash
curl -X GET http://localhost:9200/_cluster/health
```

### Create an index

```bash
PUT /products
```

### Get structure of an index

```bash
GET /products
```

### Get all shards

```bash
GET /_cat/shards?v
```

### Adding a document

- Request

```bash
POST /products/_doc
{
  "name": "Iphone 15 plus",
  "price": 999,
  "in_stock": 20
}
```

- Response

```bash
{
  "_index": "products",
  "_id": "fZvbI48BTI3iOvwGbjKy",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 9,
  "_primary_term": 1
}
```

## Adding a document with ID=1000

- Request

```bash
PUT /products/_doc/1000
{
"name": "Iphone 15 pro",
"price": 899,
"in_stock": 10
}
```

- Response

```bash
{
  "_index": "products",
  "_id": "fJvaI48BTI3iOvwGajL5",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 8,
  "_primary_term": 1
}
```

## Updating a document

- Documents in elasticsearch are immutable.
- ES simply simply copies the data to a new doc, with same id which gives a feeling of update.

#### Adding a field to / updating the document with ID = 1000

```bash
POST /products/_update/1000
{
  "doc": {
    "in_stock": 19
  }
}
```

#### Decrement the in_stock field by 1

```bash
POST /products/_update/1000
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}
```

#### Increment the in_stock field by 10

```bash
POST /products/_update/1000
{
  "script": {
    "source": "ctx._source.in_stock+=10"
  }
}
```

#### Increment the in_stock by a value passed as parameter

```bash
POST /products/_update/1000
POST /products/_update/1000
{
  "script": {
    "source": "ctx._source.in_stock+=params.quantity",
    "params": {
      "quantity": 3
    }
  }
}
```
