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
