README

Dataset:
- Please Create a developer account in Twitter. 
- After creating developer account create an App in Twitter developer account and set up dev environment to retrieve the tweets. 
- Using the Access key, Secret key, Token Access key and Token Secret key the tweets are retrieved with the help of TwitterUtils.createStream() function.

File Structure:
Assign3part1
	build.sbt
	src
	     main
		   scala
			  GraphX.scala 
			  KafkaExample.scala 
			  SentimentAnalyzer.scala
			  Sentiment.scala 
	project
	    assembly.sbt
Report.docx
README.txt

Files Description:
KafkaExample.scala - Scala object with *main* method for the first part of assignment 3
SentimentAnalyzer.scala - This instantiates the StanfordCoreNLP class and does the NLP tasks like annotation, tokenization, parsing and sentiment analysis.
Sentiment.scala - This is a Scala enum which converts it to Positive, negative and neutral sentiments
GraphX.scala - Scala object with *main* method for the second part of assignment 3
README.txt - It contains the description of the deliverables
Report.docx- Report of the implementation

Development Environment:
	java version 1.8.0_261
	scala 2.11.8
	Spark 2.4.0 built for Hadoop 2.7.3
	kafka_2.12-2.3.0
	elasticsearch-7.4.2
	kibana-7.4.2-darwin-x86_64
	logstash-7.4.2
	graphframes-0.7.0-spark2.4-s_2.11
	stanford-corenlp_3.5.2

Execution Steps:
1.	Go to kafka_2.12-2.3.0 directory
2.	Run the zookeeper 
		Windows => bin\windows\zookeeper-server-start.bat config\zookeeper.properties
		OR
		Mac/Unix => bin/zookeeper-server-start.sh config/zookeeper.properties
3.	Start the Kafka Server
		Windows => bin\windows\kafka-server-start.bat config\server.properties
		OR
		Mac/Unix => bin/kafka-server-start.sh config/server.properties
4.	Create topic name topicA
		Windows => bin\windows\kafka-topics.bat  --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic topicA
		OR
		Mac/Unix => bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic topicA
5.	Run the producer
		Windows => bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic topicA
		OR
		Mac/Unix => bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topicA
6.	Start the consumer to see producer records
		Windows => bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topicA --from-beginning
		OR
		Mac/Unix => bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topicA --from-beginning
7.	Run the elasticsearch server. Go to the elasticsearch-7.4.2/bin directory
8.	Execute the command -> 
		Windows => elasticsearch
		OR
		Mac/Unix => ./elasticsearch 
9.	Now run the kibana server. Go to the directory kibana-7.4.2-darwin-x86_64/bin 
10.	Execute the command -> 
		Windows => kibana
		OR
		Mac/Unix => ./kibana 
11.	Go to the directory logstash-7.4.2. 
	Configure the elasticsearch and kibana properties. 
	Create/Open the file logstash-simple.conf and add following configurations:


input { 
	kafka {
		bootstrap_servers => "localhost:9092" topics => ["topicA"] 
	} 
} 

filter {
  grok {
    match => { "message" => "%{WORD:sentiment} %{NUMBER:count} %{GREEDYDATA:sentence}" }
  }
  mutate {
    convert => {
      "count" => "integer"
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "mytopicindex"
  }
}

12.	Run the LogStash server.
13.	Execute the command -> 
		Windows => bin\logstash -f logstash-simple.conf 
		OR
		Mac/Unix => logstash -f logstash-simple.conf 
14.	Go to the project folder and execute this to create fat jar file  
		sbt 
		> assembly

15.	Go to the spark shell and spark-submit with topic and credentials as arguments
	Go to : target\scala-2.11\ folder in your application
		Windows => spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.4.0  --master local[3] --class KafkaExample Assignment3-Part1-assembly-0.1.jar topicA LyauufMppOp5UM3JQYCr9NUpY 1GjQh5xOmeW1gXZXUYqsOwdthlQf8258Jr5wXzRklmcFSVR178 593741673-Jpx1B8ziELlEiZ7TtlqVQ6YO1CXMZspRH3J7bzWt QjjN0vt1FlGusDhakRcsIO7XSKDRl7UvI9LPAENfuIKBd
		Mac/Unix => spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.4.0  --master local[3] --class KafkaExample Assignment3-Part1-assembly-0.1.jar topicA TwitterconsumerKey  consumerSecret accessToken accessTokenSecret
16.	Then we can see live tweets in Kafka Consumer.
17.	Data is visualized in Kibana. Copy and paste the below URIs to visualize the data from different perspectives:

http://localhost:5601/app/kibana#/visualize/create?type=horizontal_bar&indexPattern=faa425e0-d089-11ea-9d8c-6f044db2969a&_g=(filters:!(),refreshInterval:(pause:!t,value:0),time:(from:now-5h,to:now))&_a=(filters:!(),linked:!f,query:(language:kuery,query:''),uiState:(),vis:(aggs:!((enabled:!t,id:'1',params:(),schema:metric,type:count),(enabled:!t,id:'2',params:(filters:!((input:(language:kuery,query:'sentiment:POSITIVE'),label:Positive),(input:(language:kuery,query:'sentiment:NEGATIVE'),label:Negative),(input:(language:kuery,query:'sentiment:NEUTRAL'),label:Neutral))),schema:segment,type:filters)),params:(addLegend:!t,addTimeMarker:!f,addTooltip:!t,categoryAxes:!((id:CategoryAxis-1,labels:(filter:!f,rotate:0,show:!t,truncate:200),position:left,scale:(type:linear),show:!t,style:(),title:(),type:category)),dimensions:(x:(accessor:0,aggType:filters,format:(),params:()),y:!((accessor:1,aggType:count,format:(id:number),params:()))),grid:(categoryLines:!f),labels:(),legendPosition:right,seriesParams:!((data:(id:'1',label:Count),drawLinesBetweenPoints:!t,mode:normal,show:!t,showCircles:!t,type:histogram,valueAxis:ValueAxis-1)),times:!(),type:histogram,valueAxes:!((id:ValueAxis-1,labels:(filter:!t,rotate:75,show:!t,truncate:100),name:LeftAxis-1,position:bottom,scale:(mode:normal,type:linear),show:!t,style:(),title:(text:Count),type:value))),title:'',type:horizontal_bar))

http://localhost:5601/app/kibana#/visualize/create?type=pie&indexPattern=faa425e0-d089-11ea-9d8c-6f044db2969a&_g=(filters:!(),refreshInterval:(pause:!t,value:0),time:(from:now-15h,to:now))&_a=(filters:!(),linked:!f,query:(language:kuery,query:''),uiState:(),vis:(aggs:!((enabled:!t,id:'1',params:(),schema:metric,type:count),(enabled:!t,id:'2',params:(field:sentiment.keyword,size:3),schema:segment,type:significant_terms)),params:(addLegend:!t,addTooltip:!t,dimensions:(metric:(accessor:0,aggType:count,format:(id:number),params:())),isDonut:!t,labels:(last_level:!t,show:!f,truncate:100,values:!t),legendPosition:right,type:pie),title:'',type:pie))

http://localhost:5601/app/timelion#?_g=(refreshInterval:(pause:!t,value:0),time:(from:now-5h,to:now))&_a=(columns:2,interval:'1m',rows:2,selected:0,sheet:!('.es(q%3D!'sentiment:NEGATIVE!').label(!'Negative!'),%0A.es(q%3D!'sentiment:POSITIVE!').label(!'Positive!'),%0A.es(q%3D!'sentiment:NEUTRAL!').label(!'Neutral!')'))

http://localhost:5601/app/kibana#/visualize/create?type=histogram&indexPattern=faa425e0-d089-11ea-9d8c-6f044db2969a&_g=(refreshInterval:(pause:!t,value:0),time:(from:now-5h,to:now))&_a=(filters:!(),linked:!f,query:(language:kuery,query:''),uiState:(),vis:(aggs:!((enabled:!t,id:'1',params:(),schema:metric,type:count),(enabled:!t,id:'2',params:(filters:!((input:(language:kuery,query:'sentence:*Trump*'),label:Trump),(input:(language:kuery,query:'sentence:*Obama*'),label:Obama))),schema:segment,type:filters)),params:(addLegend:!t,addTimeMarker:!f,addTooltip:!t,categoryAxes:!((id:CategoryAxis-1,labels:(filter:!t,show:!t,truncate:100),position:bottom,scale:(type:linear),show:!t,style:(),title:(),type:category)),dimensions:(x:(accessor:0,aggType:filters,format:(),params:()),y:!((accessor:1,aggType:count,format:(id:number),params:()))),grid:(categoryLines:!f),labels:(show:!f),legendPosition:right,seriesParams:!((data:(id:'1',label:Count),drawLinesBetweenPoints:!t,mode:stacked,show:true,showCircles:!t,type:histogram,valueAxis:ValueAxis-1)),thresholdLine:(color:%2334130C,show:!f,style:full,value:10,width:1),times:!(),type:histogram,valueAxes:!((id:ValueAxis-1,labels:(filter:!f,rotate:0,show:!t,truncate:100),name:LeftAxis-1,position:left,scale:(mode:normal,type:linear),show:!t,style:(),title:(text:Count),type:value))),title:'',type:histogram))

http://localhost:5601/app/kibana#/visualize/create?type=histogram&indexPattern=faa425e0-d089-11ea-9d8c-6f044db2969a&_g=(refreshInterval:(pause:!t,value:0),time:(from:now-5h,to:now))&_a=(filters:!(),linked:!f,query:(language:kuery,query:''),uiState:(),vis:(aggs:!((enabled:!t,id:'1',params:(),schema:metric,type:count),(enabled:!t,id:'2',params:(filters:!((input:(language:kuery,query:'sentiment:POSITIVE%20and%20sentence:*Trump*'),label:'Trump%20Positive'),(input:(language:kuery,query:'sentiment:POSITIVE%20and%20sentence:*Obama*'),label:'Obama%20Positive'),(input:(language:kuery,query:'sentiment:NEGATIVE%20and%20sentence:*Trump*'),label:'Trump%20Negative'),(input:(language:kuery,query:'sentiment:NEGATIVE%20and%20sentence:*Obama*'),label:'Obama%20Negative'))),schema:segment,type:filters)),params:(addLegend:!t,addTimeMarker:!f,addTooltip:!t,categoryAxes:!((id:CategoryAxis-1,labels:(filter:!t,rotate:0,show:!t,truncate:100),position:top,scale:(type:linear),show:!t,style:(),title:(),type:category)),dimensions:(x:(accessor:0,aggType:filters,format:(),params:()),y:!((accessor:1,aggType:count,format:(id:number),params:()))),grid:(categoryLines:!f,valueAxis:ValueAxis-1),labels:(show:!f),legendPosition:right,orderBucketsBySum:!t,seriesParams:!((data:(id:'1',label:Count),drawLinesBetweenPoints:!t,interpolate:cardinal,mode:stacked,show:true,showCircles:!t,type:area,valueAxis:ValueAxis-1)),thresholdLine:(color:%2334130C,show:!f,style:full,value:10,width:1),times:!(),type:histogram,valueAxes:!((id:ValueAxis-1,labels:(filter:!f,rotate:0,show:!t,truncate:100),name:LeftAxis-1,position:left,scale:(mode:normal,type:'square%20root'),show:!t,style:(),title:(text:Count),type:value))),title:'',type:histogram))