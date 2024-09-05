### 1. Установка MongoDB через Docker
MongoDB был установлен и запущен в контейнере Docker. Доступ к MongoDB был обеспечен через Mongo Shell.
```sql
~/otus-mysql-docker$ docker run --name mongodb -d -p 27017:27017 mongo:latest
Unable to find image 'mongo:latest' locally
latest: Pulling from library/mongo
857cc8cb19c0: Pull complete
a54f12bd5819: Pull complete
f95b02a6236d: Pull complete
0d20d29fe9ca: Pull complete
2382733f40de: Pull complete
c1458145b657: Pull complete
fee77be41765: Pull complete
da4a4cbb623f: Pull complete
Digest: sha256:1a7b344b3ee8b07190fa15555726333e38f5db0a3bfb38b2ce9a1d3973b060be
Status: Downloaded newer image for mongo:latest
85f3d8c57ff2117e4f148ae4e1db41ba1cb5e5bcddccc28e613f118a357a0075
#:~/otus-mysql-docker$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                      PORTS                                           NAMES
24a2ff8e9922   mongo:latest   "docker-entrypoint.s…"   14 seconds ago   Up 13 seconds               0.0.0.0:27017->27017/tcp, :::27017->27017/tcp   mongodb
35e6a5c4c6e4   mysql:8.0.15   "docker-entrypoint.s…"   9 days ago       Created                                                                     mysql_custom
4df2a4e8255c   mysql:8.0.15   "docker-entrypoint.s…"   9 days ago       Exited (0) 5 minutes ago                                                    otus-mysql-docker_otusdb_1
dc8327592cde   hello-world    "/hello"                 9 days ago       Exited (0) 9 days ago                                                       suspicious_wescoff
1e653d917f04   postgres       "docker-entrypoint.s…"   9 days ago       Exited (255) 34 hours ago   5432/tcp                                        postgres
f7908dfbd093   postgres:16    "docker-entrypoint.s…"   9 days ago       Exited (255) 34 hours ago   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp       postgres-container
#:~/otus-mysql-docker$ docker exec -it 24a2ff8e9922 bash
root@24a2ff8e9922:/# mongo --host localhost --port 27017
bash: mongo: command not found
root@24a2ff8e9922:/# mongosh --host localhost --port 27017
Current Mongosh Log ID: 66d8f6c6df5ce685ad5e739b
Connecting to:          mongodb://localhost:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.3.0
Using MongoDB:          7.0.14
Using Mongosh:          2.3.0

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2024-09-05T00:07:36.223+00:00: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine. See http://dochub.mongodb.org/core/prodnotes-filesystem
   2024-09-05T00:07:36.736+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2024-09-05T00:07:36.736+00:00: vm.max_map_count is too low
------
```
### 2. Заполнение данных:

Были созданы коллекции customers и addresses и заполнены данными о клиентах и их адресах
```sql
mydatabase> db.createCollection("customers");
{ ok: 1 }
mydatabase> db.createCollection("addresses");
{ ok: 1 }
mydatabase> db.customers.insertMany([
...   { "first_name": "Danilo", "last_name": "Ambrosini", "title": "Dr.", "birth_date": "1900-01-01", "gender": "Unknown", "correspondence_language": "IT" },
...   { "first_name": "Deborah", "last_name": "Thomas", "title": "Mrs", "birth_date": null, "gender": "Female", "correspondence_language": null },
... ]);
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('66d8f6eedf5ce685ad5e739c'),
    '1': ObjectId('66d8f6eedf5ce685ad5e739d')
  }
}
mydatabase> db.addresses.insertMany([
...   { "customer_id": 1, "country": "IT", "postal_code": "21100", "city": "Varese", "street": "Via dolomiti", "building_number": 13 },
...   { "customer_id": 2, "country": "GB", "postal_code": "RH16 3TQ", "city": "Haywards Heath", "street": "Cobbetts Mead", "building_number": 31 },
...
... ]);
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('66d8f6fddf5ce685ad5e739e'),
    '1': ObjectId('66d8f6fddf5ce685ad5e739f')
  }
}
```
### 3. Запросы на выборку и обновление:

Выполнены запросы на выборку и обновление данных в MongoDB. Были найдены записи по фамилии и обновлены данные клиента и адреса.
```sql
mydatabase> db.customers.find().pretty();
[
  {
    _id: ObjectId('66d8f6eedf5ce685ad5e739c'),
    first_name: 'Danilo',
    last_name: 'Ambrosini',
    title: 'Dr.',
    birth_date: '1900-01-01',
    gender: 'Unknown',
    correspondence_language: 'IT'
  },
  {
    _id: ObjectId('66d8f6eedf5ce685ad5e739d'),
    first_name: 'Deborah',
    last_name: 'Thomas',
    title: 'Mrs',
    birth_date: null,
    gender: 'Female',
    correspondence_language: null
  }
]
mydatabase> db.customers.find({ "last_name": "Thomas" }).pretty();
[
  {
    _id: ObjectId('66d8f6eedf5ce685ad5e739d'),
    first_name: 'Deborah',
    last_name: 'Thomas',
    title: 'Mrs',
    birth_date: null,
    gender: 'Female',
    correspondence_language: null
  }
]
mydatabase> db.addresses.find({ "customer_id": 1 }).pretty();
[
  {
    _id: ObjectId('66d8f6fddf5ce685ad5e739e'),
    customer_id: 1,
    country: 'IT',
    postal_code: '21100',
    city: 'Varese',
    street: 'Via dolomiti',
    building_number: 13
  }
]
```
### 4. Задание повышенной сложности:

Созданы индексы для ускорения запросов. Проведено сравнение производительности запросов до и после создания индексов с использованием команды explain().

```sql
mydatabase> db.customers.createIndex({ "last_name": 1 });
last_name_1
mydatabase> db.addresses.createIndex({ "postal_code": 1 });
postal_code_1
mydatabase> db.customers.find({ "last_name": "Thomas" }).explain("executionStats");
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'mydatabase.customers',
    indexFilterSet: false,
    parsedQuery: { last_name: { '$eq': 'Thomas' } },
    queryHash: '3E669866',
    planCacheKey: '486D783F',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'FETCH',
      inputStage: {
        stage: 'IXSCAN',
        keyPattern: { last_name: 1 },
        indexName: 'last_name_1',
        isMultiKey: false,
        multiKeyPaths: { last_name: [] },
        isUnique: false,
        isSparse: false,
        isPartial: false,
        indexVersion: 2,
        direction: 'forward',
        indexBounds: { last_name: [ '["Thomas", "Thomas"]' ] }
      }
    },
    rejectedPlans: []
  },
  executionStats: {
    executionSuccess: true,
    nReturned: 1,
    executionTimeMillis: 0,
    totalKeysExamined: 1,
    totalDocsExamined: 1,
    executionStages: {
      stage: 'FETCH',
      nReturned: 1,
      executionTimeMillisEstimate: 0,
      works: 2,
      advanced: 1,
      needTime: 0,
      needYield: 0,
      saveState: 0,
      restoreState: 0,
      isEOF: 1,
      docsExamined: 1,
      alreadyHasObj: 0,
      inputStage: {
        stage: 'IXSCAN',
        nReturned: 1,
        executionTimeMillisEstimate: 0,
        works: 2,
        advanced: 1,
        needTime: 0,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        keyPattern: { last_name: 1 },
        indexName: 'last_name_1',
        isMultiKey: false,
        multiKeyPaths: { last_name: [] },
        isUnique: false,
        isSparse: false,
        isPartial: false,
        indexVersion: 2,
        direction: 'forward',
        indexBounds: { last_name: [ '["Thomas", "Thomas"]' ] },
        keysExamined: 1,
        seeks: 1,
        dupsTested: 0,
        dupsDropped: 0
      }
    }
  },
  command: {
    find: 'customers',
    filter: { last_name: 'Thomas' },
    '$db': 'mydatabase'
  },
  serverInfo: {
    host: '24a2ff8e9922',
    port: 27017,
    version: '7.0.14',
    gitVersion: 'ce59cfc6a3c5e5c067dca0d30697edd68d4f5188'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600,
    internalQueryFrameworkControl: 'trySbeRestricted'
  },
  ok: 1
}
mydatabase> db.customers.find({ "last_name": "Thomas" }).explain("executionStats");
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'mydatabase.customers',
    indexFilterSet: false,
    parsedQuery: { last_name: { '$eq': 'Thomas' } },
    queryHash: '3E669866',
    planCacheKey: '486D783F',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'FETCH',
      inputStage: {
        stage: 'IXSCAN',
        keyPattern: { last_name: 1 },
        indexName: 'last_name_1',
        isMultiKey: false,
        multiKeyPaths: { last_name: [] },
        isUnique: false,
        isSparse: false,
        isPartial: false,
        indexVersion: 2,
        direction: 'forward',
        indexBounds: { last_name: [ '["Thomas", "Thomas"]' ] }
      }
    },
    rejectedPlans: []
  },
  executionStats: {
    executionSuccess: true,
    nReturned: 1,
    executionTimeMillis: 0,
    totalKeysExamined: 1,
    totalDocsExamined: 1,
    executionStages: {
      stage: 'FETCH',
      nReturned: 1,
      executionTimeMillisEstimate: 0,
      works: 2,
      advanced: 1,
      needTime: 0,
      needYield: 0,
      saveState: 0,
      restoreState: 0,
      isEOF: 1,
      docsExamined: 1,
      alreadyHasObj: 0,
      inputStage: {
        stage: 'IXSCAN',
        nReturned: 1,
        executionTimeMillisEstimate: 0,
        works: 2,
        advanced: 1,
        needTime: 0,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        keyPattern: { last_name: 1 },
        indexName: 'last_name_1',
        isMultiKey: false,
        multiKeyPaths: { last_name: [] },
        isUnique: false,
        isSparse: false,
        isPartial: false,
        indexVersion: 2,
        direction: 'forward',
        indexBounds: { last_name: [ '["Thomas", "Thomas"]' ] },
        keysExamined: 1,
        seeks: 1,
        dupsTested: 0,
        dupsDropped: 0
      }
    }
  },
  command: {
    find: 'customers',
    filter: { last_name: 'Thomas' },
    '$db': 'mydatabase'
  },
  serverInfo: {
    host: '24a2ff8e9922',
    port: 27017,
    version: '7.0.14',
    gitVersion: 'ce59cfc6a3c5e5c067dca0d30697edd68d4f5188'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600,
    internalQueryFrameworkControl: 'trySbeRestricted'
  },
  ok: 1
}
```
