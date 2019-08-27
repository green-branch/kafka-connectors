Kafka Connectors
----------------
This repo demonstrates [Kafka Connect](https://kafka.apache.org/documentation/#connect) capabilities for *Change Data Capture* (CDC). These easy to setup connectors can stream data in or out of Kafka. Most popular databases can be setup as a source or sink while Kafka acts as a sink or source respectively. 


Prerequisites
-------------
To run the code in this repo on needs `docker`, `docker-compose` and `make`


Streaming data from MySQL to Kafka
----------------------------------
Start required services to facilitate data replication from a MySQL source to a Kafka sink:
```shell
cd mysql
make up # to start connect, kafka, mysql, zookeeper in deamon mode
make ps # to verify if all four pieces started as expected
make mysql_connect # to get to mysql prompt
```

Start reading Kafka topic for *Data Definition Language* (DDL, any changes to SQL Schema)
```shell
make read_ddl
```

Start reading Kafka topic for new records:
```shell
make read_students # to show new records sync'ed from MySQL bin logs to Kafka
```

In another console create a MySQL table:
```shell
make create_tbl_students
```

This should show DDL(s) on console running ```make read_ddl``` as follows:
```shell
docker exec -it kafka kafka-console-consumer.sh --whitelist schema_history --bootstrap-server kafka:9092 --from-beginning
{
  "source" : {
    "server" : "dbserver1"
  },
  "position" : {
    "ts_sec" : 1566791393,
    "file" : "log_bin_dir.000003",
    "pos" : 725,
    "server_id" : 6033
  },
  "databaseName" : "db",
  "ddl" : "CREATE TABLE IF NOT EXISTS `students` (\n  `id` int(11) NOT NULL AUTO_INCREMENT,\n  `name` varchar(50) NOT NULL,\n  `age` INTEGER,\n   PRIMARY KEY (id)\n)"
}
```

Add rows to MySQL table:
```shell
make add_data_students
```
Data added to `students` table with above command should show up in console running ```make read_students``` as follows:
```shell
docker exec -it kafka kafka-console-consumer.sh --whitelist dbserver1.db.students --bootstrap-server kafka:9092 --from-beginning
{"schema":{"type":"struct","fields":[{"type":"struct","fields":[{"type":"int32","optional":false,"field":"id"},{"type":"string","optional":false,"field":"name"},{"type":"int32","optional":true,"field":"age"}],"optional":true,"name":"dbserver1.db.students.Value","field":"before"},{"type":"struct","fields":[{"type":"int32","optional":false,"field":"id"},{"type":"string","optional":false,"field":"name"},{"type":"int32","optional":true,"field":"age"}],"optional":true,"name":"dbserver1.db.students.Value","field":"after"},{"type":"struct","fields":[{"type":"string","optional":true,"field":"version"},{"type":"string","optional":true,"field":"connector"},{"type":"string","optional":false,"field":"name"},{"type":"int64","optional":false,"field":"server_id"},{"type":"int64","optional":false,"field":"ts_sec"},{"type":"string","optional":true,"field":"gtid"},{"type":"string","optional":false,"field":"file"},{"type":"int64","optional":false,"field":"pos"},{"type":"int32","optional":false,"field":"row"},{"type":"boolean","optional":true,"default":false,"field":"snapshot"},{"type":"int64","optional":true,"field":"thread"},{"type":"string","optional":true,"field":"db"},{"type":"string","optional":true,"field":"table"},{"type":"string","optional":true,"field":"query"}],"optional":false,"name":"io.debezium.connector.mysql.Source","field":"source"},{"type":"string","optional":false,"field":"op"},{"type":"int64","optional":true,"field":"ts_ms"}],"optional":false,"name":"dbserver1.db.students.Envelope"},"payload":{"before":null,"after":{"id":1,"name":"Chris","age":37},"source":{"version":"0.9.5.Final","connector":"mysql","name":"dbserver1","server_id":6033,"ts_sec":1566923682,"gtid":null,"file":"log_bin_dir.000003","pos":1197,"row":0,"snapshot":false,"thread":69,"db":"db","table":"students","query":null},"op":"c","ts_ms":1566923682075}}
{"schema":{"type":"struct","fields":[{"type":"struct","fields":[{"type":"int32","optional":false,"field":"id"},{"type":"string","optional":false,"field":"name"},{"type":"int32","optional":true,"field":"age"}],"optional":true,"name":"dbserver1.db.students.Value","field":"before"},{"type":"struct","fields":[{"type":"int32","optional":false,"field":"id"},{"type":"string","optional":false,"field":"name"},{"type":"int32","optional":true,"field":"age"}],"optional":true,"name":"dbserver1.db.students.Value","field":"after"},{"type":"struct","fields":[{"type":"string","optional":true,"field":"version"},{"type":"string","optional":true,"field":"connector"},{"type":"string","optional":false,"field":"name"},{"type":"int64","optional":false,"field":"server_id"},{"type":"int64","optional":false,"field":"ts_sec"},{"type":"string","optional":true,"field":"gtid"},{"type":"string","optional":false,"field":"file"},{"type":"int64","optional":false,"field":"pos"},{"type":"int32","optional":false,"field":"row"},{"type":"boolean","optional":true,"default":false,"field":"snapshot"},{"type":"int64","optional":true,"field":"thread"},{"type":"string","optional":true,"field":"db"},{"type":"string","optional":true,"field":"table"},{"type":"string","optional":true,"field":"query"}],"optional":false,"name":"io.debezium.connector.mysql.Source","field":"source"},{"type":"string","optional":false,"field":"op"},{"type":"int64","optional":true,"field":"ts_ms"}],"optional":false,"name":"dbserver1.db.students.Envelope"},"payload":{"before":null,"after":{"id":2,"name":"Christine","age":52},"source":{"version":"0.9.5.Final","connector":"mysql","name":"dbserver1","server_id":6033,"ts_sec":1566923682,"gtid":null,"file":"log_bin_dir.000003","pos":1464,"row":0,"snapshot":false,"thread":69,"db":"db","table":"students","query":null},"op":"c","ts_ms":1566923682105}}
```

## Streaming data from InfluxDB to Kafka
----------------------------------------
TODO
