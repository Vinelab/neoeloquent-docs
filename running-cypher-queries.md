## Running Cypher Queries

In order to run cypher queries, you will need to:

### Write your cypher query:

```php
$cypher = <<<CYPHER

            MATCH (persons:Person),
            (acreditations:Acreditation),
            (persons)-[:HAS]->(acreditiations)
            WHERE persons.is_eligable = true and persons.status = "is_active" and acreditations.validity = "is_valid"
            RETURN persons, acreditations
            ORDER BY acreditations.acredited_at DESC
            limit 30

CYPHER;
```

### Send cypher to execution

```php
/**
     * Run raw cypher query.
     *
     * @param string cypher
     *
     * @return NeoOxygen\NeoClient\Request\Response
     */
$result = $this->model->getConnection()->getClient()->sendCypherQuery($cypher);

$rows = $result->getRows();
```

The next step would be to map the result rows into node clases and finally `collect` these into a `Collection` class.
