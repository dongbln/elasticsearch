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






