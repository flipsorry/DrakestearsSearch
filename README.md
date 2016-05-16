# DrakestearsSearch

Start up the service
* elasticsearch-2.3.2/bin/elasticsearch --cluster.name drakestears

First create the index 
  curl -XPUT "http://localhost:9200/videos/?pretty"

Now create the search index
  curl -XPUT "http://localhost:9200/videos/search/_mapping?pretty" -d'
  {
     "search": {
        "properties": {
           "name": {
              "type": "string"
           },
           "suggest": {
              "type": "completion",
              "analyzer": "simple",
              "search_analyzer": "simple",
              "payloads": true
           }
        }
     }
  }'

Put some data
  curl -XPUT "http://localhost:9200/videos/search/johnwick?pretty" -d'
  {
     "name": "John Wick",
     "suggest": {
        "input": [
           "john",
           "wick",
           "john wick"
        ],
        "output": "John Wick",
        "payload": {
          "location": "/home/flipsorry/Dos/Torrents/John.Wick.2014.720p.WEB-DL.x264[ETRG]/John.Wick.2014.720p.WEB-DL.x264[ETRG].mkv.mp4"
        }
     }
  }'

Search for the movie
  curl -XPOST "http://localhost:9200/videos/_suggest?pretty" -d'
  {
      "search-suggest":{
          "text":"john",
          "completion": {
              "field" : "suggest",
              "fuzzy" : {
                  "fuzziness" : 2
              }
          }
      }
  }'

Delete a record
   curl -XDELETE "http://localhost:9200/videos/search/johnwick?pretty"
