kafka.service
```
[Unit]
Description=Apache Kafka service
Documentation=https://kafka.apache.org/documentation.html
After=network.target zookeeper.service

[Service]
User=kafka
Group=kafka
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target

```

kafka-ui.service
```
[Unit]
Description=Kafka UI Service
After=network.target

[Service]
User=kafka
Group=kafka
WorkingDirectory=/opt/kafka/kafka-ui
ExecStart=/usr/bin/java -Dspring.config.additional-location=/opt/kafka/kafka-ui/config.yml --add-opens java.rmi/javax.rmi.ssl=ALL-UNNAMED -jar /opt/kafka/kafka-ui/kafka-ui-api.jar 

Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

![[Pasted image 20241128085410.png]]