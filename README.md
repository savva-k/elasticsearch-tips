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

