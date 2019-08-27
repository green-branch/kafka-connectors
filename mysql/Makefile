default:
	exit 1
list:
	grep -v '\t' Makefile | sed 's/://'
ps:
	docker-compose ps
stop:
	docker-compose stop
rm:
	docker-compose rm
up:
	docker-compose up -d
mysql_connect:
	docker exec -it mysql mysql -uroot -proot_password
create_tbl_students:
	docker exec mysql mysql -uroot -proot_password -e "$(cat sql/students.sql)"
add_data_students:
	docker exec mysql mysql -uroot -proot_password -e "$(cat sql/add_students.sql)"
read_students:
	docker exec -it kafka kafka-console-consumer.sh --whitelist dbserver1.db.students --bootstrap-server kafka:9092 --from-beginning
read_ddl:
	docker exec -it kafka kafka-console-consumer.sh --whitelist schema_history --bootstrap-server kafka:9092 --from-beginning
show_topics:
	docker exec kafka kafka-topics.sh --list --zookeeper zookeeper:2181
connect_post:
	 curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d '{ "name": "db-connector", "config": { "connector.class": "io.debezium.connector.mysql.MySqlConnector", "tasks.max": "1", "database.hostname": "mysql", "database.port": "3306", "database.user": "root", "database.password": "root_password", "database.server.id": "6033", "database.server.name": "dbserver1", "database.whitelist": "db", "database.history.kafka.bootstrap.servers": "kafka:9092", "database.history.kafka.topic": "schema_history" } }'
connect_delete:
	curl -i -X DELETE -H "Accept:application/json" localhost:8083/connectors/db-connector
connect_get:
	curl -i -X GET -H "Accept:application/json" localhost:8083/connectors/db-connector
connect_get_all:
	curl -i -X GET -H "Accept:application/json" localhost:8083/connectors/
