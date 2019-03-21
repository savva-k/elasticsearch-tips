# Elasticsearch Tips

Assuming that we have our Elasticsearch available at http://localhost:9201

## Creating an index
The full guides on creating indices: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html,  
analyzers: https://www.elastic.co/guide/en/elasticsearch/reference/current/analyzer.html,  
and stemmer token filter: https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stemmer-tokenfilter.html

Create an index called "blog" with a custom analyzer:

```
curl -X PUT -H "Content-Type: application/json" localhost:9201/blog \
-d @- << EOF
{
    "settings": {
        "analysis": {
            "filter": {
                "my_stemmer": {
                    "type": "stemmer",
                    "name": "english"
                }
            },
            "analyzer": {
                "my_custom_analyzer": {
                    "type": "custom",
                    "tokenizer": "standard",
                    "filter": [
                        "trim",
                        "lowercase",
                        "my_stemmer"
                    ]
                }
            }
        }
    },
    "mappings": {
        "post": {
            "properties": {
                "title": {
                    "type": "text",
                    "analyzer": "my_custom_analyzer"
                },
                "content": {
                    "type": "text",
                    "analyzer": "my_custom_analyzer"
                },
                "tags": {
                    "type": "keyword"
                },
                "likes": {
                    "type": "long"
                }
            }
        }
    }
}
EOF
```
## Check the index and its settings
More on indices API: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html

```
curl -X GET localhost:9201/blog?pretty
```
```
curl -X GET localhost:9201/blog/_settings?pretty
```
(and stats)

```
curl -X GET localhost:9201/blog/_stats?pretty
```

## Add some data with the bulk upload operation

Let's add some data to continue with the search and aggregations functionality. The data is placed in the *test_data* file in this repository.

```
curl -s -H "Content-Type: application/x-ndjson" -X POST localhost:9201/_bulk --data-binary "@test_data"
```
and check what that everything is fine
```
curl localhost:9201/blog/post/1?pretty
```

## Check how our analyzer works
Read more on _analyze API: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-analyze.html

```
curl -X GET -H "Content-Type: application/json" localhost:9201/blog/_analyze?pretty -d '{ "analyzer": "my_custom_analyzer", "text": "The quick brown fox jumps over the lazy dog" }'
```
