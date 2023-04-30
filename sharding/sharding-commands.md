

sh.status()


use sharddemo

db.createCollection("movies")

db.createCollection("movies2")

db.movies2.getShardDistribution()

sh.shardCollection("sharddemo.movies2",{"title":"hashed"})


//one database can have multiple collections, sharding is enabled on database, 
//sharded and unsharded collection can co-exist in a sharded database

db.movies2.getShardDistribution()

db.movies.getShardDistribution()


for i in {1..50}; do echo -e "use sharddemo \n db.movies2.insertOne({\"title\":\"Sprider man $i\",\"language\": \"English\"})" | mongosh mongodb://ip-10-224-120-218:60000; done


db.movies2.getShardDistribution()

db.movies2.find({"title":"Spider man 41"}).explain("executionStatus")


//insert documents into movies collection

for i in {1..10}; do echo -e "use sharddemo \n db.movies.insertOne({\"title\":\"Sprider man $i\",\"language\": \"English\"})" | mongosh mongodb://ip-10-224-120-218:60000; done

db.movies.getShardDistribution()

// collection is not sharded, try to enable sharding on this collection

sh.shardCollection("sharddemo.movies",{"title":"hashed"})

// Please create an index that starts with the proposed shard key before sharding the collection
// it has to be hashed index or range index, whichever we choose to shard
db.movies.createIndex({"title":"hashed"})

sh.shardCollection("sharddemo.movies",{"title":"hashed"})

//command to drop index
db.movies.dropIndex("title_1")


//changing chunk size, 1 is in MB, default is 64MB
db.settings.updateOne( { _id: "chunksize" }, { $set: { _id: "chunksize", value: 1 } },{upsert:true})


