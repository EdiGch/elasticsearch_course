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

* Dodanie pojedynczego filmu 
```shell
curl -XPOST 127.0.0.1:9200/movies/_doc/109487 -d '
{
    "genre": [
        "IMAX", 
        "Sci-Fi"
    ],
    "title": "Interstellar",
    "year": 2014
}
'
```
odp:
```shell
{"_index":"movies","_type":"_doc","_id":"109487","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```
* Pobranie wprowadzonego filmu 
?pretty - wynik będzie sformatowany, łatwo czytelnym
```shell
curl -XGET 127.0.0.1:9200/movies/_doc/109487?pretty
```

* Dodanie wielu documentów
```shell
curl -XPUT 127.0.0.1:9200/_bulk?pretty --data-binary @/home/gcharkiewicz/myElasticsearch/elasticsearch_course/Movielens/movies.json
```

* Pokaż wszystkie dokumenty 
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty
```

* Update 
```shell
curl -XPUT 127.0.0.1:9200/movies/_doc/109487?pretty
```

* Szukanie
```shell
curl -XGET 127.0.0.1:9200/movies/_search?q=Dark
```

* Usunięcie 
```shell
curl -XDELETE 127.0.0.1:9200/movies/_doc/58559
```

* Współbieżność, unikanie problemów ze współbieżnością
  * _seq_no
  * _primary_term
  * retry_on_conflict=5 (automatycznie ponów próbę do 5 razy)
```shell
curl -XPUT  "127.0.0.1:9200/movies/_doc/109487?if_seq_no=8&if_primary_term=2" -d 
'
{
    "genre": [
        "IMAX", 
        "Sci-Fi"
    ],
    "title": "Interstellar - EDIT- Edit",
    "year": 2014
}
'
```
retry_on_conflict=5
```shell
curl -XPOST "127.0.0.1:9200/movies/_doc/109487/_update?retry_on_conflict=5"
'
{
    "doc": {
        "title": "Interstellar - EDIT- Edit AUTOMAT"
    }
}
```

## Szukanie 
Opcje wyszukiwanie są uzależnione od mapowania, raz stworzone mapowanie jest niezmienne, 
chcąc zmienić rodzaj pola należy usunąć index.

* Szykanie rozmyte

```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d
'
{
    "query": {
        "match_phrase": {
            "genre": "sci"
        }
    }
}
'
```

* Szukanie ściśle określone 
```shell

```

* Usunięcie całego indexu 
```shell
curl -XDELETE 127.0.0.1:9200/movies
```

* Mapowanie 
  * "type": "keyword" - oznacza że bedziemy dokonywać tylko dokładnych dopoasowań
  * "type": "text" - - zostanie zastosowany analizator, możemy szukać po częściowym dopasowaniu
```shell
'
{
  "mappings": {
    "properties": {
      "id": {
        "type": "integer"
      },
      "year": {
        "type": "date"
      },
      "genre": {
        "type": "keyword"
      },
      "title": {
        "type": "text"
      }
    }
  }
}
'
```