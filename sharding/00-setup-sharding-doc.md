## Set up Sharding using Docker Containers

### Config servers
Start config servers (3 member replica set)
```
sudo docker compose -f config-server/docker-compose.yaml up -d
```
Initiate replica set
```

hostname -f

 mongosh mongodb://ip-10-224-120-218:40005
```
```
rs.initiate(
  {
    _id: "cfgrs",
    configsvr: true,
    members: [
      { _id : 0, host : "ip-10-224-120-218:40005" },
      { _id : 1, host : "ip-10-224-120-218:40006" },
      { _id : 2, host : "1ip-10-224-120-218:40007" }
    ]
  }
)

to reconfigure:

rs.reconfig( { _id: "cfgrs", configsvr: true, members: [ { _id: 0, host: "ip-10-224-120-218:40005" }, { _id: 1, host: "ip-10-224-120-218:40006" }, { _id: 2, host: "ip-10-224-120-218:40007" }] },{"force":true})

rs.status()
```

### Shard 1 servers
Start shard 1 servers (3 member replicas set)
```
sudo docker compose -f shard1/docker-compose.yaml up -d
```
Initiate replica set
```
mongosh mongodb://ip-10-224-120-218:50001
```
```
rs.initiate(
  {
    _id: "shard1rs",
    members: [
      { _id : 0, host : "ip-10-224-120-218:50001" },
      { _id : 1, host : "ip-10-224-120-218:50002" },
      { _id : 2, host : "ip-10-224-120-218:50003" }
    ]
  }
)

rs.status()
```

### Mongos Router
Start mongos query router
```
sudo docker compose -f mongos/docker-compose.yaml up -d
```

### Add shard to the cluster
Connect to mongos
```
mongosh mongodb://ip-10-224-120-218:60000
```
Add shard
```
mongos> sh.addShard("shard1rs/ip-10-224-120-218:50001,ip-10-224-120-218:50002,ip-10-224-120-218:50003")
mongos> sh.status()
```
## Adding another shard
### Shard 2 servers
Start shard 2 servers (3 member replicas set)
```
sudo docker compose -f shard2/docker-compose.yaml up -d
```
Initiate replica set
```
mongosh mongodb://ip-10-224-120-218:50004
```
```
rs.initiate(
  {
    _id: "shard2rs",
    members: [
      { _id : 0, host : "ip-10-224-120-218:50004" },
      { _id : 1, host : "ip-10-224-120-218:50005" },
      { _id : 2, host : "ip-10-224-120-218:50006" }
    ]
  }
)

rs.status()
```
### Add shard to the cluster
Connect to mongos
```
mongosh mongodb://ip-10-224-120-218:60000
```
Add shard
```
mongos> sh.addShard("shard2rs/ip-10-224-120-218:50004,ip-10-224-120-218:50005,ip-10-224-120-218:50006")
mongos> sh.status()
```
