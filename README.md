# Elasticsearch (ES)

## Installation
See the setup at the Elasticsearch (ES) website

## Start ES
To start ES execute this command with the heap size 256M
```
ES_HEAP_SIZE=256m; bin/elasticsearch
```
Check the status of the ES nodes

```
curl localhost:9200/_cat/nodes
```

Create a new document with its index called ```productSearchIndex```, 
document type called ```books```, and with document ID called ```firstID```.
An index is a seperated files into a disk and normally seprated into 5 shards by default.
```
curl -XPUT 'localhost:9200/productsSearchIndex/books/firstID -d '
{
"book_name": "John",
"autor_name": "Doe",
"book_category": ["romance","drama"],
"likes": 100,
"tags":["romance","drama","adult"],
"year": 2015
}'
```
To view the document
```
curl localhost:9200/productSearchIndex/books/firstID?pretty
```
To update the document

```
curl localhost:9200/productSearchIndex/books/firstID/_update  -d '
{
"doc" : {
	"published_by": " ABD Publischer"
}
}'
```
To delete the document with its ID
```
curl -XDELETE localhost:9200/productSearchIndex/books/firstID?pretty
```
To delete the whole index

```
curl -XDELETE localhost:9200/productSearchIndex
```

## Search
Search a document
```
curl  'localhost:9200/productSearchIndex/_search?q=name:tom&pretty' | more
```
Search a document with the ```OR``` operator
```
curl  'localhost:9200/productSearchIndex/_search?q=name:tom+OR+name:max&pretty' | more
```
Or alternativ

# Example for mapping and query data from multiple data model in Elasticsearch (Nested,Denormalizing)

```
curl -XPUT "http://localhost:9200/my_index/views/1" -d'
{
   "userkey": "dex"
}'
curl -XPUT "http://localhost:9200/my_index/views/2" -d'
{
   "userkey": "ted"
}'
curl -XPUT "http://localhost:9200/my_index/clicks/1" -d'
{
"userkey": "dex"
}'
curl -XPUT "http://localhost:9200/my_index/clicks/2" -d'
{
"userkey": "dg"
}'

# Denormalizing
curl -XPUT "http://localhost:9200/my_index/denom/1" -d'
{
"eid": "mmx",
"userkey":"xxo"

}'
curl -XPUT "http://localhost:9200/my_index/denom/2" -d'
{
"eid": "34",
"userkey":"34"

}'
# Query denomalizing with script
curl -XPOST "http://localhost:9200/my_index/_search" -d'
{
   "query": {
      "filtered": {
         "filter": {
            "script": {
               "script": "doc[\"userkey\"].value == doc[\"eid\"].value"
            }
         }
      }
   }
}'


# Mapping for nested data model
curl -XPUT "http://localhost:9200/my_index/userevents_nested/_mapping" -d'
{
   "userevents_nested": {
      "properties": {
         "crm": {
            "type": "nested"
         },
         "onsite": {
            "type": "nested"
         }
      }
   }
}'

# Index nested data model
curl -XPUT "http://localhost:9200/my_index/userevents_nested/1" -d'
{
   "crm": [
      {
         "eid": "xx"
      },
      {
         "eid": "cc"
      }
   ],
   "onsite": [
      {
         "userkey": "xx"
      },
      {
         "userkey": "bb"
      }
   ]
}'

# Query nested data
curl -XGET "http://localhost:9200/my_index/userevents_nested/_search" -d'
{
   "query": {
      "bool": {
         "must": [
            {
               "nested": {
                  "path": "crm",
                  "query": {
                     "bool": {
                        "must": [
                           {
                              "match": {
                                 "crm.eid": "xx"
                              }
                           }
                        ]
                     }
                  }
               }
            },
            {
               "nested": {
                  "path": "onsite",
                  "query": {
                     "bool": {
                        "must": [
                           {
                              "match": {
                                 "onsite.userkey": "xx"
                              }
                           }
                        ]
                     }
                  }
               }
            }
         ]
      }
   }
}'
# Query nested objects across multiple types with scripts
curl -XPOST "http://localhost:9200/my_index/_search" -d'
{  "size": 0, 
   "query": {
      "filtered": {
         "filter": {
            "script": {
               "script": "doc[\"onsite.userkey\"].value == doc[\"crm.eid\"].value"
            }
         }
      }
   }
}'

```




