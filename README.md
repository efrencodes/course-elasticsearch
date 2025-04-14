# Elasticsearch

### How run elasticsearch on Docker

```bash
$ docker-compose up
```

### Save user with specific id

```bash
curl --location --request PUT 'http://localhost:9200/usuarios/_doc/1' \
--header 'Content-Type: application/json' \
--data '{
    "nombre": "efren",
    "apellido": "martinez"
}'
```

### Save user without specific id

```bash
curl --location 'http://localhost:9200/usuarios/_doc?refresh=null' \
--header 'Content-Type: application/json' \
--data '{
    "nombre": "Hello world"
}'
```

With refresh flag is for get file/document immediately

### How save multiples users

Create a file .json with information (Siempre dejar un espacio al final del documento)

```json
{ "index": { "_id": "3" } }
{ "nombre": "Beth", "apellido": "Smith" }
{ "index": { "_id": "4" } }
{ "nombre": "Jerry", "apellido": "Smette" }

```

```bash
curl --location 'http://localhost:9200/usuarios/_bulk' \
--header 'Content-Type: application/json' \
--data-binary '@/C:/Users/emartinez/Documents/workspace/course-elasticsearch/users.json'
```

## Verbs HTTP

### Get all users

```bash
curl --location 'http://localhost:9200/usuarios/_search'
```

### Get by specific id

Return with metadata
```bash
curl --location 'http://localhost:9200/usuarios/_doc/5KMsJpYB9TRvBYML0KN7'
```

Return without metadata, only the information
```bash
curl --location 'http://localhost:9200/usuarios/_source/5KMsJpYB9TRvBYML0KN7'
```

Mapping
```bash
curl --location 'http://localhost:9200/usuarios/_mapping'
```

Si el recurso no existe:
    PUT: Crea el recurso
    POST: Te arrojará un error ya que estás intentando actualizar algo (De ahí la palabra reservada _update)

Si el recurso existe:
    PUT: Borrará lo que tengas actualmente y agregará lo que indiques en el body
    POST: Agregará los cambios que hiciste en el body pero no borrará aquellos valores que no actualizes

### Update user

```bash
curl --location --request PUT 'http://localhost:9200/usuarios/_doc/1' \
--header 'Content-Type: application/json' \
--data '{
    "edad": 32
}'
```

### Add user

```bash
curl --location 'http://localhost:9200/usuarios/_update/1' \
--header 'Content-Type: application/json' \
--data '{
    "doc": {
        "nombre": "Rick",
        "apellido": "sanches"
    }
}'
```

## Mapping data

El mapeo de datos permite optimizar el rendimiento y precisión de las búsquedas. Es vital que especifiques un mapeo explicito.

### Create mapping data (platos)

```bash
curl --location --request PUT 'http://localhost:9200/platos' \
--header 'Content-Type: application/json' \
--data '{
    "mappings": {
        "properties": {
            "nombre": {"type": "text"},
            "descripción": {"type": "text"},
            "pedidosUltimaHora": {"type": "integer"},
            "ultimaModificacion": {
                "properties": {
                    "usuario": {"type": "text"},
                    "fecha": {"type": "date"}
                }
            }
        }
    }
}'
```

### Update mapping data

```bash
curl --location --request PUT 'http://localhost:9200/platos/_mapping' \
--header 'Content-Type: application/json' \
--data '{
  "properties":{
    "estado":{ "type":"keyword" }
  }
}'
```

### Query simple

```bash
curl --location --request GET 'http://localhost:9200/platos/_search' \
--header 'Content-Type: application/json' \
--data '{
  "query": {
    "simple_query_string": {
      "query": "bowl de pollo saludable"
    }
  }
}
'
```

### Query with weight

```bash
curl --location --request GET 'http://localhost:9200/platos/_search' \
--header 'Content-Type: application/json' \
--data '{
  "query": {
    "simple_query_string": {
      "query": "guacamole picante",
      "fields": ["nombre", "descripcion^2"]
    }
  }
}

'
```

## Types of clausulas

* Para una sola consulta se utilizan {}
* Para más de una consulta se utiliza [] must, como un AND lógico, la consulta debe aparecer en los documentos retornados, influye en el puntaje.
* filter, como un AND lógico, la consulta debe aparecer en los documentos retornados, no influye en el puntaje, se puede usar cache.
* should, como un OR lógico, la consultas puede aparecer en los documentos retornados, no influye en el puntaje, se puede usar cache.
        Con minimum_should_match, indica cuantas consultas deben coincidir en los documentos. por defecto es 1. Si must o filter acompañan al should, su valor por defecto es 0
* must_not, como un NOT lógico, la consulta no debe aparecer en los documentos retornados, no influye en el puntaje, se puede usar cache.

