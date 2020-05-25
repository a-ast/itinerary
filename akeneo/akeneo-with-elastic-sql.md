# [WIP] Elasticsearch SQL 

Install

docker-compose.yml

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.4.2
    
Reset index (for EE needs to comment EE workflow types)
Reindex products


Requests

https://www.elastic.co/guide/en/elasticsearch/reference/6.x/sql-syntax-select.html

SELECT select_expr [, ...]
[ FROM table_name ]
[ WHERE condition ]
[ GROUP BY grouping_element [, ...] ]
[ HAVING condition]
[ ORDER BY expression [ ASC | DESC ] [, ...] ]
[ LIMIT [ count ] ]



```json
{
    "query": "SELECT * FROM akeneo_pim_product LIMIT 5"
}
``` 
