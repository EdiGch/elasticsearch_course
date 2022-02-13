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

## HTTP i RESTful API
```shell
curl -H "Content-Type: application/json" <URL> -d '<BODY>'
```
* Wysyłanie danych
```shell
curl -H "Content-Type: application/json" -XPUT '127.0.0.1:9200/movies/movie/109487' -d

```

## Tip ubuntu dla skrócenia wprowadzanych adresów

```shell
cd ~
pwd => 
mkdir bin
cd bin
nano curl

#!/bin/bash
/usr/bin/curl -H "Content-Type: application/json" "$@"

sudo chmod a+x curl
cd ~
pwd =>
source .profile
which curl
```

## Field types
1. String
2. Byte
3. Short
4. Intiger
5. Long
6. Float
7. Double
8. Boolean
9. Date

## Field Index 
1. full-text search
2. not-analyzed 

## Field Analyzer 
1. Filtry znaków 
2. Tokenizer 
3. Token Filter 

### Podział analizatorów 
1. Standard 
2. Simple
3. Whitespace
4. Language


## Movielens
Movielens id a free dataset of movie. 

* Pobranie bazy do ćwiczeń z strony
```shell
https://grouplens.org/datasets/movielens/
```

* Dodanie pojedynczego filmu 
```shell
```

* Mapowanie danych 
Mapowanie jako definicja schematu. Dedinicja w jakim formacie przechowywać dane, jak je indeksować i jak analizować.

```shell
curl -H "Content-Type: application/json" -XPUT
'127.0.0.1:9200/movies' -d 
'
{
  "mappings": {
    "properties": {
      "year": {
        "type": "date"
      }
    }
  }
}
'
```
* Sprawdzenie czy mapowanie się powiodło 
```shell
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_mapping
```
odp:
```shell
127.0.0.1:9200/movies/_mapping
```