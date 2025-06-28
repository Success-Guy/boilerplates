## Mongo replicaSet with docker containers

```
docker run -d -p 30001:27017 --name mongo-rep --net mongo-cluster mongo mongod --replSet my-mongo-set 
```

```
docker run -d -p 30001:27017 --name mongo-rep -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=rootuserroot --net mongo-cluster mongo mongod --replSet my-mongo-set
```
```
docker run -d -p 30002:27017 --name mongo-rep2 -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=rootuserroot --net mongo-cluster mongo mongod --replSet my-mongo-set
```

### Generate a Key File
This is compulsory for security purpose and of recent, it is a must.
```
openssl rand -base64 741 > mongokey
chmod 400 mongokey
```

### Entering the first mongo instance
```
 docker exec -it mongo-rep bash
```
### Login into the mongo service inside the container

```
mongosh [-u username -p paswword]

## Many replica set config

config = {"_id" : "rpl", "members": [{"_id" : 0, "host":"mongo-rep:27017"},{"_id" : 1, "host":"mongo-rep2:27017"}]}
```

## Single replica set config

```
config = {"_id" : "rpl", "members": [{"_id" : 0, "host":"207.180.197.163:27027"}]}
```
## Initiate the replica set with the new config
```
rs.initiate(config)

```
## Check the status of the new setup
```bash
rs.status()
```
##  create new user

```
use admin;
db.createUser({user: "root",pwd: "admin",roles: [ {role: "root", db: "admin" }]})
db.createUser({user: "domain",pwd: "domainmaster",roles: [ "readWriteAnyDatabase"]})
use database;
db.createUser(
  {
    user: "user",
    pwd: "pass",
    roles: [ { role: "readWrite", db: "database" } ]
  }
);

db.createUser({user: "presta",pwd: "presta",roles: ["root"]});
```
## Change user role

```
db.grantRolesToUser(
  "user",
  [
    {role: "readWriteAnyDatabase", db: "admin" }
  ]
);
```
### Reconfiguration of primary server
```
cfg = rs.conf();
cfg.members[0].priority = 2
cfg.members[1].priority = 0.5
cfg.members[2].priority = 1
rs.reconfig(cfg)
```

Then, create data on the database, and wow!!!
We are done.

Reconfiguring rpl
```
config = {"_id" : "rpl", "members": [{"_id" : 0, "host":"domain-api.domain.co:30001"}, {"_id" : 1, "host":"monitor.domain.co:30001"}, {"_id" : 2, "host":"build.domain.co:30001"} ]}
rs.reconfig(config, {"force": true})
```

## Change password:
```bash
use admin;
db.changeUserPassword("tom", "new_password")
```