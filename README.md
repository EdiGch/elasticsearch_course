# Elasticsearch

* Instalcja i uruchomienia Elasticsearch w tle.
```shell
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.17.0
```

* Uruchom polecenie w zadanym kontenerze
```shell
docker exec -it 7e56a3a576e2 bash 
```

* Sprawdzenie połączenia 
```shell
curl -XGET 127.0.0.1:9200
```

## Pobranie pliku do ciwiczeń:
* mapowanie typów danych 
```shell
wget http://media.sundog-soft.com/es7/shakes-mapping.json
```
* Przesłanie mapowania typów danych do ES
```shell
curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @/home/gcharkiewicz/Pobrane/shakes-mapping.json
```
* Pobranie tekstu który zostanie wgrany 
```shell
wget http://media.sundog-soft.com/es7/shakespeare_7.0.json
```
* Wgranie danych do ES
```shell
curl -H "Content-Type: application/json" -XPOST '127.0.0.1:9200/shakespeare/_bulk?pretty' --data-binary @/home/gcharkiewicz/Pobrane/shakespeare_7.0.json
```

```shell
curl -H "Content-Type: application/json" -XGET
'127.0.0.1:9200/shakespeare/_search?pretty' -d 
'
{
  "query": {
    "match_phrase": {
      "text_entry": "to be or not to be"
    }
  }
}
'
```