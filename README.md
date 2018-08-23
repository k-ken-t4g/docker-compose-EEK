# docker-compose-EEK
Sample Docker-compose to setup EEK(Embulk, Elasticsearch, Kibana) configuration

# Requirements
- docker : Version 18.06.0-ce, build 0ffa825
- docker-compose: Version 1.22.0, build f46880f

See https://docs.docker.com/install/ to check how to install `docker` and `docker-compose`

# Setup
```
$ cd ./docker-compose-EEK
$ docker-compose up -d
```

If it works, you can browse `localhost:5601` (`kibana`) .

# How to Use it

## Sample Data
There is sample data in `d0d0npa/docker-compose-EEK/log/nikkei225.csv`.
Sample data itself is acquired from FRED using `python`. 

``` python
import datetime
import pandas_datareader.data as web

start = datetime.datetime(2008, 1, 1)
nikkei225 = web.DataReader(["NIKKEI225","DJIA","DEXJPUS"], "fred", start) 
nikkei225.to_csv("nikkei225.csv")
```

## Set Elasticserach Mapping
Browse `localhost:5601`(`kibana`) then fo to `DEVTOOLS` and input map setting using Console.
Below is sample map setting for sample data.

```
PUT nikkei225
{
  "template": "nikkei225*",
  "mappings": {
    "nikkei_schema":{
      "properties": {
          "NIKKEI225": { "type": "double" },
          "DATE": {"format" : "YYYY-MM-dd", "type": "date" },
          "DJIA": { "type": "double" },
          "DEXJPUS": { "type": "double" }
      }
    }
  }
}
```

Check https://www.elastic.co/guide/en/elasticsearch/reference/6.x/mapping.html for more detail.

## Push data into elasticsearch via embulk
You need to run a command in a running container(`embulk` container) to push data into elasticsearch.
Below are commands to push data into elasticsearch

```
$ docker exec -it docker-compose-efk_embulk_1 bash
[root@5b7d05cac814:~# cd logs/
root@5b7d05cac814:~/logs# embulk run config.yml 
```

## Enjoy creating dashboard

# Future Work
- Hopefuly add `digdag`

