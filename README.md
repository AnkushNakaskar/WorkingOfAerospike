### WorkingOfAerospike
 #### Installation of aerospike on local
- Setup a docker on local first
- ```
  docker run -tid --name aerospike-test -p 3000:3000 -p 3001:3001 -p 3002:3002 -p 3003:3003 aerospike/aerospike-server
  ```
- This will return docker container ID
  - Or you can use ```docker ps -a ``` to check running dockers
  - To get docker images LocalIPAddress : ```docker inspect <dockercontainerID>```
    - Check for ```"IPAddress"``` in json config return from inspect
    - It can be like this ```"IPAddress": "172.17.0.2"```
- Once you got localIPAddress , try below AQL command to connect to server.
  - ```docker run -ti --name aerospike-aql --rm aerospike/aerospike-tools aql -h <localIPAddress> aerospike/aerospike-server```
    - LocalIPAddress : 172.17.0.2 as per example
- Now since you are in AQL, you can Aerospike with below basic cmds
  - Concepts : 
    - Namespace : This is similar to database in RelationalDB
      - ```show namespace;```
    - SET : This is similar to Table in  RelationalDB , Since aerospike is non structure DB, you can directly start inserting with set like below
      - ```INSERT INTO test.testset (PK, a, b) VALUES ('xyz', 'abc', 123)```
      - This will create testset as SET in namespace test.
      - ```select * from test.testset;```
  - If you want to query data based on non-primary key attributes, you'll need to create secondary indexes on the desired bins first. After creating secondary indexes, you can use them in your queries to filter data efficiently. 
    - ```SELECT * FROM test.demo WHERE bin2 = 'value2'```
- To connect to aerospike admin(asdmn)
  - ```docker run -ti --name aerospike-asadm --rm aerospike/aerospike-tools asadm -h 172.17.0.2```
  - **To make changes in secondary index** , you need to enable it first
    - Type enable like below cmds
    - ```
      ERROR: asadm -e create index -n test -s users1 -b value -t NUMERIC my_index: command not found.
      Admin> manage sindex create numeric ix1 ns test set users1 bin age
      ERROR: User must be in privileged mode to issue "manage" commands.
      Type "enable" to enter privileged mode.
      Admin> enable
      Admin+> manage sindex create numeric ix1 ns test set users1 bin age
      Use 'show sindex' to confirm ix1 was created successfully.
      Admin+> show sindex
      ~~Secondary Indexes (2025-02-07 09:45:20 UTC)~~~
      Index|Namespace|   Set|Bin|    Bin|  Index|State
      Name|         |      |   |   Type|   Type|     
      ix1  |test     |users1|age|numeric|default|RW   
      Number of rows: 1
      Admin+>
      ```
    - **Range queries  and queries on secondary index** : Once its indexing is done, you can invoke below cmd
    - So the start and end is for the secondary index and is for the bin’s, not for primary index.
    - Read more : https://aerospike.com/docs/server/architecture/secondary-index.html
     ```
      aql> select * from test.users1 where age between 20 and 30
      +----+-----+--------+
      | PK | age | name   |
      +----+-----+--------+
      | 20 | 20  | "abc1" |
      +----+-----+--------+
      1 row in set (0.022 secs)
      aql>
     ```
