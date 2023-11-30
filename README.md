# PySpark-Streaming-App

This PySpark streaming application reads data from X (Twitter) and performs sentiment analysis on them and utilises an ELK (Elasticsearch, Logstash, Kibana) with Kakfa to visalise those sentiments. 

Twitter API -> Tweepy/TCP Socket -> Spark streaming (Sentiment Analysis) -> Kafka topic -> ELK

## Steps to implement:

Start the zookeeper server:    .\zookeeper-server-start.bat ....\config\zookeeper.properties inside kafka\bin\windows

Start the Kafka server:    .\kafka-server-start.bat ....\config\server.properties inside kafka\bin\windows

Create kafka topic: (inside kafka\bin\windows)    **.\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic twittersenti **

Configure Logstash (logstash.conf) to read from kafka topic i.e. source and write to elasticsearch index.

input {
kafka {
bootstrap_servers => "localhost:9092"
topics => "sentiments"
}
}

output {
elasticsearch {
hosts => ["http://localhost:9200"]
index => "sentiments"
}
}

Start elastic search by running the command elasticsearch inside the bin directory of elasticsearch

Start logstash by running the following command inside logstash folder   
bin\logstash.bat -f config\logstash.conf

Start Kibana by running the command .\kibana.bat inside the bin directory of kibana

To check if elastic search is working fine, type the following command and you should see the attached output
   http://localhost:9200/_cat/indices/twittersenti
This should display the health status as yellow denoting that index is set.

Run the read_tweets.py followed by write_tweets.ipynb

#covid tweets are filtered
Once the last cell of the write_tweets.ipynb is run, tweets gets classified based on sentiments and kibana displays real-time insights
Enter http://localhost:5601/

Create index pattern: Stack Management => Data Views => Create Data Views
In the ‘Discover’ section, choose the kafka topic twittersenti
Real-time statistics on tweets will then be displayed
To generate graphs, create them in the dashboard with required fields.
