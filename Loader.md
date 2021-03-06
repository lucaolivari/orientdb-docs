# ETL - Loaders

**Loader** components are the last part of the ETL process responsible of the saving of records.

## Available Loaders

|  |  |  |  |
|-----|-----|-----|-----|
|[Output](Loader.md#output)|[OrientDB](Loader.md#orientdb)| | |

-----

### Output
It's the default Loader. It prints the transformation result to the console output.

- Component name: **output**
- Accepted input classes: [**Object**]

-----

### OrientDB
Loads record and vertices into a OrientDB database.

- Component name: **orientdb**
- Accepted input classes: [**ODocument**, **OrientVertex**]

#### Syntax
| Parameter | Description | Type | Mandatory | Default value |
|-----------|-------------|------|-----------|-----------|
|dbURL|Database URL|string|true|-|
|dbUser|User Name|string|false|admin|
|dbPassword|User Password|string|false|admin|
|dbAutoCreate|If the database not exists, create it automatically|boolean|false|true|
|dbAutoCreateProperties|Auto create properties in schema|boolean|false|false|
|dbAutoDropIfExists|Auto drop the database if already exists|boolean|false|false|
|tx|Use [transactions](Transactions.md) or not|boolean|false|false|
|txUseLog|Use log in transaction. If WAL is disabled you can still use not reliable transactions by setting txUseLog=true. This is useful to group many operations in batch, like create edges|boolean|false|-|
|wal|Use WAL (Write Ahead Logging). Disable WAL to achieve better performances|boolean|false|true|
|batchCommit|With [transactions](Transactions.md) enabled, commit every X entries. Use this to avoid having one huge transaction in memory|integer|false|0|
|dbType|Database type, between 'graph' or 'document'|string|false|document|
|class|Class name to use to store the new record|string|false|-|
|cluster|Cluster name where to store the new record|string|false|-|
|classes|Creates classes (if not already defined in database). Look at [classes](Loader.md#classes) syntax for more information|inner document|false|-|
|indexes|Contains the indexes used on ETL process. Before starting any declared index not present in database will be created automatically. Index configuration must have "type", "class" and "fields". Look at [indexes](Loader.md#indexes) syntax for more information|inner document|false|-|
|useLightweightEdges|Changes the default setting about using [Lightweight Edges](Lightweight-Edges.md)|boolean|false|false|
|standardElementConstraints|Changes the default setting about using the TinkerPop Blueprints constraints: values cannot be null and 'id'cannot be used as property name|boolean|false|true|

##### classes

| Parameter | Description | Type | Mandatory | Default value |
|-----------|-------------|------|-----------|-----------|
|name|Class name|string|true|-|
|extends|Super class name|string|false|-|
|clusters|Number of clusters to create under the class. Since 2.1|integer|false|1|

##### indexes

| Parameter | Description | Type | Mandatory | Default value |
|-----------|-------------|------|-----------|-----------|
|name|Index name|string|false|-|
|class|Class name where to create the index|string|true|-|
|type|Index type between the available ones|string|true|-|
|fields|Array of field names to index. To specify the field type use the syntax `<field-name>:<field-type>`|string|true|-|

#### Example
Below an example of configuration to load in a OrientDB database called "dbpedia" in directory "/temp/databases" open using "plocal" protocol and used as "graph". The loading is transactional and commits the transaction every 1,000 inserts. To lookup vertices has been create the index against the property string "URI" in base vertex "V" class. The index is unique.

```json
"orientdb": {
      "dbURL": "plocal:/temp/databases/dbpedia",
      "dbUser": "importer",
      "dbPassword": "IMP",
      "dbAutoCreate": true,
      "tx": false,
      "batchCommit": 1000,
      "wal" : false,
      "dbType": "graph",
      "classes": [
        {"name":"Person", "extends": "V" },
        {"name":"Customer", "extends": "Person", "clusters":8 }
      ],
      "indexes": [{"class":"V", "fields":["URI:string"], "type":"UNIQUE" }]
    }
```

-----
