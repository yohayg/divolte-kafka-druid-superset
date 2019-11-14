# A proof of concept using Divolte, Kafka, Druid and Superset

Please refer to the blogpost for more information: https://blog.godatadriven.com/divolte-kafka-druid-superset


# Getting started

What is more fun than to get an proof of concept running on your own machine? Using Docker it is easy to set up a local instance of the stack so we can give it a try and explore the possibilities.

To set up the system, we start by cloning the git repository:

```
git clone https://github.com/Fokko/divolte-kafka-druid-superset.git
cd divolte-kafka-druid-superset
docker-compose rm -f && docker-compose build && docker-compose up -d
docker-compose ps
```

Service	URL:

1. Divolte	http://localhost:8290/
2. Druid Unified Console	http://localhost:8888/
3. Druid Legacy Console	http://localhost:8081/
4. Superset	http://localhost:8088/
5. Sample application	http://localhost:8090/


Next we need to tell Druid to listen on the correct Kafka topic. This is done by posting a supervisor specification file in JSON format to the indexing service using curl:

```
curl -X POST -H 'Content-Type: application/json' -d @supervisor-spec.json http://localhost:8081/druid/indexer/v1/supervisor
{"id":"divolte-clickstream"}
```

Visit the app http://localhost:8090/ and click on all the technologies you like. Clicking generates events that we will visualize later on.


```
docker-compose exec superset superset-init
Username [admin]: test
User first name [admin]: test
User last name [user]: test
Email [admin@fab.org]: test@test.com
Password: test
Repeat for confirmation: test
```

Now that we have configured Divolte, Kafka and Druid, and emitted some events, it is time to configure Superset. 
Please go to http://localhost:8088/druidclustermodelview/add, as below we can fill all fields with druid as this is the alias in the supplied docker-compose:

Verbose Name: druid

Coordinator Host: druid

Coordinator Port: 8081

Broker Host: druid

Broker Port: 8082

Cluster: druid


Next we have to explicitly refresh the Druid data source by opening a specific url http://localhost:8088/druid/refresh_datasources/, which can also be found in the menu of Superset. This will contact the Druid coordinator and will ask for the available datasources and their corresponding schemas.


Now everything is loaded, we can start making our first slice. A slice in Superset is a chart or table which can be used in one or more dashboards:

Create new slice:

Choose a datasource: divolte-clickstream
Choose a visualization type: Pie Chart

After creation:

Choose Group by: technology

Click some more on the logo's to see your dashboard instantaneously change


Choose: pie chart