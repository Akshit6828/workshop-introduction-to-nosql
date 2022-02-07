## 🎓🔥 Introduction to NotOnly SQL (NoSQL) Databases

[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

![image](images/splash.jpeg?raw=true)

These instructions will lead you step by step for the workshop on introducing the NoSQL Databases technologies.

## Materials for the Session

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

- [Workshop video](#)
- [Slide deck](./slides.pdf)
- [Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)

## Participation Badge / Homework

To get the verified badge, you have to complete the following steps:

1. Complete the practice steps of this workshop as explained below, steps I-IV are mandatory, step V is optional. Take a screenshot of the last completed step for  steps II, III and IV. _NOTE: When taking screenshots ensure NOT to copy your Astra DB key!_
2. Complete [try-it-out scenario](https://www.datastax.com/try-it-out) and make a screenshot of the "scenario completed" screen
3. Submit the practice [here](https://dtsx.io/nosql-ws-hw), answering test questions and attaching the screenshots.

## Practice

1. [Login or Register to AstraDB and create database](#1-login-or-register-to-astradb-and-create-database)
2. [Tabular Databases](#2-tabular-databases)
3. [Document Databases](#3-document-databases)
4. [Key-Value Databases](#4-keyvalue-databases)
5. [Graph Databases](#5-graph-databases)

## 1. Login or Register to AstraDB and create database
**`ASTRADB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, roughly 20M reads/writes and 80GB storage per month for FREE - sufficient to run small production workloads.  

✅ **Step 1a:** Click the button to login or register with Datastax. You can use your `Github`, `Google` accounts or register with an `email`.

_Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character_

<a href="https://astra.dev/2-9"><img src="https://github.com/datastaxdevs/workshop-graphql-netflix/raw/master/img/create_astra_db.png?raw=true" /></a>

**Use the following values when creating the database**

|Field| Value|
|---|---|
|**database name**| `workshops` |
|**keyspace**| `nosql1` |
|**Cloud Provider**| *Use the one you like, click a cloud provider logo, pick an Area in the list and finally pick a region.* |


_You can technically use whatever you want and update the code to reflect the keyspace. This is really to get you on a happy path for the first run._

#### You will see your new database in `pending` or `initailizing` on the Dashboard.

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

## 2. Tabular databases

In a tabular database we will store ... tables! The Astra DB Service is built on Apache Cassandra™, which is tabular. Let's start with this.

> **Tabular databases** organize data in rows and columns, but with a twist from the traditional RDBMS. Also known as wide-column stores or partitioned row stores, they provide the option to organize related rows in partitions that are stored together on the same replicas to allow fast queries. Unlike RDBMSs, the tabular format is not necessarily strict. For example, Apache Cassandra™ does not require all rows to contain values for all columns in the table. Like Key/Value and Document databases, Tabular databases use hashing to retrieve rows from the table. Examples include: Cassandra, HBase, and Google Bigtable.

**✅ 2a. Describe your Keyspace**

At Database creation you provided a keyspace, a logical grouping for tables.
Let's visualize it.
In Astra DB go to CQL Console to enter the following commands

- *Select your db*
![image](images/01.png?raw=true)

- *Select CqlConsole*
![image](images/02.png?raw=true)

- *Enter the command*
```sql
describe keyspaces;
```

![image](images/03.png?raw=true)

**✅ 2b. Create tables**

- *Execute the following Cassandra Query Language* 

```sql
use nosql1;

CREATE TABLE IF NOT EXISTS videos (
 videoid   uuid,
 email     text,
 title     text,
 upload    timestamp,
 url       text,
 tags      set <text>,
 frames    list<int>,
 PRIMARY KEY (videoid)
);
```

- *Visualize structure*

```sql
describe keyspace nosql1;
```
**👁️ Expected output**

```
CREATE KEYSPACE nosql1 WITH replication = {'class': 'NetworkTopologyStrategy', 'us-east1': '3'}  AND durable_writes = true;

CREATE TABLE nosql1.videos (
    videoid uuid PRIMARY KEY,
    email text,
    frames list<int>,
    tags set<text>,
    title text,
    upload timestamp,
    url text
) WITH additional_write_policy = '99PERCENTILE'
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.UnifiedCompactionStrategy'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair = 'BLOCKING'
    AND speculative_retry = '99PERCENTILE';
```

**✅ 2c. Working with DATA** :

- *Insert some entries on first table*

```sql
INSERT INTO videos(videoid, email, title, upload, url, tags, frames)
VALUES(uuid(), 
     'clu@sample.com', 
     'Introduction to Nosql Databases', 
     toTimeStamp(now()), 
     'https://www.youtube.com/watch?v=cMDHxsGbmC8',
     { 'nosql','workshop','2021'}, 
     [ 1, 2, 3, 4]);
     
INSERT INTO videos(videoid, email, title, upload, url)
VALUES(uuid(), 
      'clu@sample.com', 
      'Demo video Y', 
      toTimeStamp(now()), 
      'https://www.youtube.com/watch?v=XXXX');

INSERT INTO videos(videoid, email, title, upload, url)
VALUES(e466f561-4ea4-4eb7-8dcc-126e0fbfd573, 
      'clu@sample.com', 
      'Yet another video', 
      toTimeStamp(now()), 
      'https://www.youtube.com/watch?v=ABCDE');
```

- *Read values*

```sql
select * from videos;
```

**👁️ Expected output**

```
 videoid                              | email          | frames       | tags                          | title                           | upload                          | url
--------------------------------------+----------------+--------------+-------------------------------+---------------------------------+---------------------------------+---------------------------------------------
 a5ab4896-7caa-4d85-ae2b-b0bbfd7e074f | clu@sample.com |         null |                          null |                    Demo video Y | 2022-02-07 21:51:43.469000+0000 |        https://www.youtube.com/watch?v=XXXX
 e466f561-4ea4-4eb7-8dcc-126e0fbfd573 | clu@sample.com |         null |                          null |               Yet another video | 2022-02-07 21:52:06.549000+0000 |       https://www.youtube.com/watch?v=ABCDE
 f22076f3-c920-4bad-8eb8-e730f68099b1 | clu@sample.com | [1, 2, 3, 4] | {'2021', 'nosql', 'workshop'} | Introduction to Nosql Databases | 2022-02-07 21:52:06.534000+0000 | https://www.youtube.com/watch?v=cMDHxsGbmC8
 b6a4e555-d2c0-4913-adc5-70fc816ef9c9 | clu@sample.com |         null |                          null |                    Demo video Y | 2022-02-07 21:52:06.544000+0000 |        https://www.youtube.com/watch?v=XXXX

(4 rows)
```

- *Read by id*

```sql
select * from videos 
where videoid=e466f561-4ea4-4eb7-8dcc-126e0fbfd573;
```

**👁️ Expected output**

```
 videoid                              | email          | frames | tags | title             | upload                          | url
--------------------------------------+----------------+--------+------+-------------------+---------------------------------+---------------------------------------
 e466f561-4ea4-4eb7-8dcc-126e0fbfd573 | clu@sample.com |   null | null | Yet another video | 2022-02-07 21:52:06.549000+0000 | https://www.youtube.com/watch?v=ABCDE

(1 rows)
```


**✅ 2d. Working with PARTITIONS** :

But data can be grouped, we stored together what should be retrieved together.

- *Create this new table*

```sql
CREATE TABLE IF NOT EXISTS users_by_city (
 city      text,
 firstname text,
 lastname  text,
 email     text,
 PRIMARY KEY ((city), lastname, email)
) WITH CLUSTERING ORDER BY(lastname ASC, email ASC);

```
- *Insert some entries*

```sql
INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('PARIS', 'Lunven', 'Cedrick', 'clu@sample.com');

INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('PARIS', 'Yellow', 'Jackets', 'yj@sample.com');

INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('ORLANDO', 'David', 'Gilardi', 'dgi@sample.com');
```

- *List values with partitions keys*

```sql
SELECT * from users_by_city WHERE city='PARIS';
```

<!--

- *List values without partitions keys*

```sql
SELECT * from users_by_city WHERE lastname='Gilardi';
```

**`Yes we know`** and we will explain why.

- *Same query with debugging enabled*

```sql
tracing on;
SELECT * from users_by_city WHERE city='PARIS';
```

- *Forcing Full Scan = SLOW*

```sql
SELECT * from users_by_city WHERE lastname='Gilardi' ALLOW FILTERING;
```

- *Stop debugging*

```sql
tracing off;
```
-->

[🏠 Back to Table of Contents](#table-of-content)

## 3. Document Databases

Let's do some hands-on with document database queries.

> **Document databases** expand on the basic idea of key-value stores where “documents” are more complex, in that they contain data and each document is assigned a unique key, which is used to retrieve the document. These are designed for storing, retrieving, and managing document-oriented information, often stored as JSON. Since the Document database can inspect the document contents, the database can perform some additional retrieval processing. Unlike RDBMSs which require a static schema, Document databases have a flexible schema as defined by the document contents. Examples include: MongoDB and CouchDB. Note that some RDBMS and NoSQL databases outside of pure document stores are able to store and query JSON documents, including Cassandra.

**✅ 3a. Cassandra knows JSON** :

It is not a known fact but Cassandra accepts JSON query out of the box. You can find more information [here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useInsertJSON.html).

- *Insert data into Cassandra with JSON*

```sql
INSERT INTO videos JSON '{
   "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd578",
     "email":"clunven@sample.com",
     "title":"A JSON videos",
     "upload":"2020-02-26 15:09:22 +00:00",
     "url": "http://google.fr",
     "frames": [1,2,3,4],
     "tags":   [ "cassandra","accelerate", "2020"]
}';
```

In the same way you can retrieve JSON out of Cassandra ([more info here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useQueryJSON.html)).

- *Retrieve data from Cassandra as JSON*

```sql
select json title,url,tags from videos;
```

![image](images/04.png?raw=true)

**✅ 3b. Create your Application token** :

You need to create an Astra DB token, which will be used to interact with the
database through the Swagger UI. Once created, store its value in a safe place
(the Astra console won't show that to you again!) and do not post it in public.

To create the token from your main dashboard (where your databases are listed)
simply click on the "..." menu next to your database and choose "Generate a token".
Choose the role "Database Administrator" and click on "Generate Token".

_Alternatively, go to the Astra dashboard, open the Organization menu on the
top left and choose "Organization settings", then "Token Management" and finally
you will be able to generate a new token. Choose the role "Database Administrator"._

Download the token in CSV format and/or copy
its value to a handy place such as a text editor: we will use it immediately!

_See this [documentation](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html) to create your application token._ 

*The Astra DB Token looks like the following (do not copy, it's just an example)*
```
AstraCS:KDfdKeNREyWQvDpDrBqwBsUB:ec80667c....
```

**👁️ Walkthrough**

Quick way:

![image](images/tutorials/generate_token.png?raw=true))

This is what the token page looks like:

![image](images/token_hl.png?raw=true)

**Swagger UI**

Next go the "Connect" page, locate the SWAGGER URL

![image](images/connect.png?raw=true)

Locate the "documents" section in the Swagger UI.

![image](images/05.png?raw=true)

**✅ 3c Create a new empty collection** :

![Swagger 3c](images/swagger/swagger_3c.png)

- Access ***Create a new empty collection in a namespace***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `body` use 

```json
{ "name": "col1" }
```
- Click `Execute` button

You will get an _"HTTP 201 - Created"_ return code

**✅ 3d. Create a new document** :

![Swagger 3d](images/swagger/swagger_3d.png)

- Access ***Create a new document***
- Click `Try it out` button
- Fill with Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `body` use 

```json
{
    "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573",
    "email":"clunven@sample.com",
    "title":"A Second video",
    "upload":"2020-02-26 15:09:22 +00:00",
    "url": "http://google.fr",
    "frames": [1,2,3,4],
    "tags":   [ "cassandra","accelerate", "2020"],
    "formats": { 
       "mp4": {"width":1,"height":1},
       "ogg": {"width":1,"height":1}
    }
}
```
- Click `Execute` button

*👁️ Expected output*
```json
{
  "documentId":"5d746e40-97cf-490b-ab0d-68cfbc5d2ef3"
}
```

You can add a couple of documents changing values, new documents with new ids will be generated

**✅ 3e Find all documents of a collection** :

![Swagger 3e](images/swagger/swagger_3e.png)

- Access ***Search documents in a collection***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`

Let other fields blank every query is paged in Cassandra.

- Click `Execute` button

**👁️ Expected output**:
```json
{
  "data": {
    "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b": {
      "email": "clunven@sample.com",
      "formats": {
        "mp4": {
          "height": 1,
          "width": 1
        },
        "ogg": {
          "height": 1,
          "width": 1
        }
      },
      "frames": [
        1,
        2,
        3,
        4
        ...
```

**✅ 3f Retrieve a document from its id** :

![Swagger 3f](images/swagger/swagger_3f.png)

- Access ***Get a document***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `document-id` use `<doc_id_in_previous_call>`
- Click `Execute` button

**👁️ Expected output**:
```json
{
  "documentId": "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b",
  "data": {
    "email": "clunven@sample.com",
    "formats": {
      "mp4": {
        "height": 1,
        "width": 1
      },
      "ogg": {
        "height": 1,
        "width": 1
      }
    },
    "frames": [
      1,
      2,
      3,
      4
    ],
    "tags": [
      "cassandra",
      "accelerate",
      "2020"
    ],
    "title": "A Second videos",
    "upload": "2020-02-26 15:09:22 +00:00",
    "url": "http://google.fr",
    "videoid": "e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
  }
}
```

**✅ 3g Search document from a where clause** :

![Swagger 3g](images/swagger/swagger_3g.png)

- Access ***Search documents in a collection***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `where` use `{"email":    { "$eq":"clunven@sample.com" } } `
- Click `Execute` button

*👁️ Expected output*
```json
{
  "data": {
    "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b": {
      "email": "clunven@sample.com",
      "formats": {
        "mp4": {
          "height": 1,
          "width": 1
        },
        "ogg": {
          "height": 1,
          "width": 1
        }
      },
      "frames": [
        1,
        2,
        3,
        4
      ],
      "tags": [
        "cassandra",
        "accelerate",
        "2020"
      ],
      "title": "A Second videos",
      "upload": "2020-02-26 15:09:22 +00:00",
      "url": "http://google.fr",
      "videoid": "e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
    }
  }
}
```

[🏠 Back to Table of Contents](#table-of-content)

## 4. Key/Value Databases

> **Key/Value databases** are some of the simplest and yet powerful as all of the data within consists of an indexed key and a value. Key-value databases use a hashing mechanism such that given a key, the database can quickly retrieve an associated value. Hashing mechanisms provide constant time access, which means they maintain high performance even at large scale. The keys can be any type of object, but are typically a string. The values are generally opaque blobs (i.e., a sequence of bytes that the database does not interpret). Examples include: Redis, Amazon DynamoDB, Riak, and Oracle NoSQL database. Some tabular NoSQL databases, like Cassandra, can also service key/value needs.

**✅ 4b. Create a table with GraphQL**

Navigate to the GraphQL playground, by clicking `Connect`, then `GraphQL API`, and finally you should see the URL under `Launching GraphQL Playground`. Ctrl-click or right-click the link to launch into a new tab.

![Graph connect](images/graph_connect.png)

Then you need to enter in your Astra DB token to authenticate and talk to the database. Notice the "server cannot be reached" message. This is simply telling you it cannot make the connection because you are not authenticated. Click on `HTTP Headers` at the bottom and paste in your token.

![Screen Shot 2021-05-20 at 8 49 48 AM](https://user-images.githubusercontent.com/23346205/118981634-88de8380-b948-11eb-9ece-5f75f153020e.png)

Paste in your token.

![Screen Shot 2021-05-20 at 8 53 07 AM](https://user-images.githubusercontent.com/23346205/118982115-18843200-b949-11eb-840c-acce0a0c562a.png)

Now you are ready to go.

Use this query. Since we are creating a table we want to use the `graphql-schema` tab

```
mutation {
  kv: createTable(
    keyspaceName:"nosql1",
    tableName:"key_value",
    partitionKeys: [ # The keys required to access your data
      { name: "key", type: {basic: TEXT} }
    ]
    values: [ # The values associated with the keys
      { name: "value", type: {basic: TEXT} }
    ]
  )
}
```

![Screen Shot 2021-05-20 at 8 58 13 AM](https://user-images.githubusercontent.com/23346205/118982905-df988d00-b949-11eb-8584-9407c9efa80e.png)

You can check in the CQL Console as well;

```sql
use nosql1;

describe table key_value;
```

*Expected output*

![image](images/cqlconsole1.png?raw=true)

**✅ 4c. Populate the table**

Any of the created APIs can be used to interact with the GraphQL data, to write or read data. Since we are working with data now and not creating schema you will need to **swtich to the `graphql` tab**. Also, make sure you **fill in your Astra DB token again**.

- *Fill the header token again*

```json
{
  "x-cassandra-token":"AstraCS:KDfdKeNREyWQvDpDrBqwBsUB:ec80667c...."
}
```

![Screen Shot 2021-05-20 at 9 04 47 AM](https://user-images.githubusercontent.com/23346205/118983681-af9db980-b94a-11eb-82b7-e0c852701265.png)

Now, let’s navigate to your new keyspace `nosql1` inside the playground. **Change tab to `graphql`** and pick url `/graphql/nosql1`. Your URL should end with something like `/api/graphql/nosql1`. If you have trouble ensuring this, switch back to the `graphql-schema` tab to grab the URL and applying some surgery on it to use the end point that includes `graphql` aand the keyspace `nosql1` so that it ends with `/api/graphql/nosql1` as explained earlier.

![Screen Shot 2021-05-20 at 9 13 26 AM](https://user-images.githubusercontent.com/23346205/118985049-1d96b080-b94c-11eb-87c4-0340e941d37c.png)


Then paste in the following query and click "play". 
![Screen Shot 2021-05-20 at 9 17 35 AM](https://user-images.githubusercontent.com/23346205/118985407-6c444a80-b94c-11eb-9b7d-d83e35e40bf1.png)

- *Execute this query*

```
mutation insert2KV {
  key1: insertkey_value(value: {key:"key1", value:"bbbb"}) {
    value {
      key,value
    }
  }
  key2: insertkey_value(value: {key:"key2", value:"bbbb"}) {
    value {
      key,value
    }
  }
}
```

- Check with CQL Console

```sql
select * from key_value;
```

*Expected output:*

![image](images/graphql2.png?raw=true)

- *Execute this query*

```
mutation insert2KV {
  key1: insertkey_value(value: {key:"key1", value:"cccc"}) {
    value {
      key,value
    }
  }
}
```

- Check with CQL Console the values should have been updated. Indeed with Cassandra there are no integrity constraints, so you can de-duplicate values easily.

[🏠 Back to Table of Contents](#table-of-content)

## 5. Graph Databases

> **Graph databases** store their data using a graph metaphor to exploit the relationships between data. Nodes in the graph represent data items, and edges represent the relationships between the data items. Graph databases are designed for highly complex and connected data, which outpaces the relationship and JOIN capabilities of an RDBMS. Graph databases are often exceptionally good at finding commonalities and anomalies among large data sets. Examples of Graph databases include DataStax Graph, Neo4J, JanusGraph, and Amazon Neptune.

Astra DB does not contain yet a way to implement Graph Databases use cases. But at Datastax we do have a product called [DataStax Graph](https://www.datastax.com/products/datastax-graph) that you can use for free when not in production.

Today it will be a demo to be quick but you can as well do and start the demo with the following steps

**✅ 5a. Prerequisites**

**Minimal Configuration**: You need to have a computer with this minimal configuration requirements
- At least 2CPU
- At least 6GB or RAM

**Install Docker and Docker Compose**

You need to install Docker and Docker-compose on your machine
- [Install **Docker** for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#1-install-docker)
- [Install **Docker-Compose**  for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#2-install-docker-compose)

**✅ 5b. Create a docker network named 'graph'**

```bash
docker network create graph
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker network create graph

64f8bcc2dda416d6dc80ef3c1ac97902b9d90007842808308e9d741d179d9344
```

**✅ 5c.Clone this repository (or download ZIP from the github UI)**

```bash
git clone https://github.com/datastaxdevs/workshop-introduction-to-nosql.git

cd workshop-introduction-to-nosql
```

**✅ 5d.Start the containers**

:warning: *Linux users:*
  >Folder `datastax-studio-config` is mapped to docker container (see: `docker-compose.yaml` file) and dse studio runs as user `studio` with `uid=997` and         >`gui=997` which needs RW access to that folder.
  >
  >Run this command if you are on a linux system:
  >```bash
  >sudo chown -R 997:997 ./datastax-studio-config
  >```
:warning: *Linux users:*

:📝 Note for *Windows users:*
  >Start the *studio image* `without a volume`. Remove these 2 lines above `networks` in *studio* (see: `docker-compose.yaml` file) 
  >```yaml
  >volumes:
  >    - "./datastax-studio-config:/var/lib/datastax-studio"

:📝 Note for *Windows users:*

Start containers:
```bash
docker-compose up -d
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose up -d

Creating dse ... done
Creating workshop-introduction-to-nosql_studio_1 ... done
```
Wait for the application to start (30s) and open [http://localhost:9091](http://localhost:9091)


![image](images/studio_home.png?raw=true)

**✅ 5e.Check database connection**

Open the ellipsis and click `Connections`

![image](images/studio_test_connection1.png?raw=true)

Select the `default localhost` connection

![image](images/studio_test_connection2.png?raw=true)

Check that `dse` is set for the host (pointing to a local cassandra)

![image](images/studio_test_connection3.png?raw=true)

Click the button `Test` and expect the output `Connected Successfully`

![image](images/studio_test_connection4.png?raw=true)

**✅ 5f. Open the notebook Work**

Use the ellipsis to now select `Notebooks`

![image](images/studio_home.png?raw=true)

Once the notebook opens it asks you to create the graph: click the `Create Graph` button (and leave all settings to default)

![image](images/studio_create_graph.png?raw=true)

Execute cell after cell spotting the `Real Time >` button in each cell (top right) 

![image](images/studio_notebook_1.png?raw=true)

Voila ! 

![image](images/studio_notebook_2.png?raw=true)

**✅ 5g. Close Notebook**

To close open notebooks you can now use

```bash
docker-compose down
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose down
Stopping workshop-introduction-to-nosql_studio_1 ... done
Stopping dse                                     ... 
```

[🏠 Back to Table of Contents](#table-of-content)

## THE END

Congratulations! You made it to the END.

See you next time!



