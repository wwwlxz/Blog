title: storm测试
date: 2015-07-27 17:00:03
tags: storm
---

# storm测试

标签（空格分隔）： 云计算 storm

--- 

> 参考文档：http://shiyanjun.cn/archives/934.html

##1、背景说明
系统：centos6.5
节点：2个
Zookeeper版本：3.4.6
storm版本：0.9.4

##2、maven配置
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.lxz.www</groupId>
	<artifactId>com.lxz.www</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>com.lxz.www</name>
	<url>http://maven.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.apache.storm</groupId>
			<artifactId>storm-core</artifactId>
			<version>0.9.4</version>
			<scope>provided</scope>
		</dependency>
	</dependencies>
	
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
				<version>1.4</version>
				<configuration>
					<createDependencyReducedPom>true</createDependencyReducedPom>
				</configuration>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
							<transformers>
								<transformer
									implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
								<transformer
									implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
									<mainClass>com.lxz.storm.wordcount.WordTopology</mainClass>
								</transformer>
							</transformers>
						</configuration>
					</execution>
				</executions>
			</plugin>

			<!-- <plugin> <artifactId>maven-assembly-plugin</artifactId> <configuration> 
				<archive> <manifest> <mainClass>com.lxz.storm.test02.FirstTopo</mainClass> 
				</manifest> </archive> <descriptorRefs> <descriptorRef>jar-with-dependencies</descriptorRef> 
				</descriptorRefs> </configuration> </plugin> -->
		</plugins>
	</build>
</project>

```
编译maven：
```cmd
mvn clean install
```

##3、建立spout
Spout负责发射数据
```java
package com.lxz.storm.wordcount;

import java.util.Map;
import java.util.Random;

import backtype.storm.spout.SpoutOutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.base.BaseRichSpout;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;

public class RandomSpout extends BaseRichSpout{
	private SpoutOutputCollector collector;
	private Random rand;
	private static String[] sentences = new String[]{"edi:I'm happy",
			"marry:I'm angry", "john:I'm sad", "ted:I'm exceited", "laden:I'm dangerous"};
	
	public void open(Map conf, TopologyContext context,
			SpoutOutputCollector collector) {
		this.collector = collector;
		this.rand = new Random();
	}
	
	public void nextTuple() {
		String toSay = sentences[rand.nextInt(sentences.length)];
		this.collector.emit(new Values(toSay));
	}

	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("sentence"));
	}

}
```
##4、建立bolt
```java
package com.lxz.storm.wordcount;

import backtype.storm.topology.BasicOutputCollector;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.base.BaseBasicBolt;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Tuple;
import backtype.storm.tuple.Values;

public class ExclaimBasicBolt extends BaseBasicBolt{

	public void execute(Tuple input, BasicOutputCollector collector) {
		String sentence = input.getString(0);
		String out = sentence + "!";
		collector.emit(new Values(out));
	}

	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("sentence"));
	}
	
}
```

```java
package com.lxz.storm.wordcount;

import backtype.storm.topology.BasicOutputCollector;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.base.BaseBasicBolt;
import backtype.storm.tuple.Tuple;

public class PrintBolt extends BaseBasicBolt{
	
	public void execute(Tuple input, BasicOutputCollector collector) {
		String rec = input.getString(0);
		System.out.println("String recieved: " + rec);
	}

	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		// TODO Auto-generated method stub
		
	}

}

```
##5、建立Topology
```java
package com.lxz.storm.wordcount;

import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.StormSubmitter;
import backtype.storm.generated.AlreadyAliveException;
import backtype.storm.generated.InvalidTopologyException;
import backtype.storm.topology.TopologyBuilder;
import backtype.storm.utils.Utils;

public class WordTopology {
	public static void main(String[] args) throws AlreadyAliveException, InvalidTopologyException{
		TopologyBuilder builder = new TopologyBuilder();
		builder.setSpout("spout", new RandomSpout());
		builder.setBolt("exclaim", new ExclaimBasicBolt()).shuffleGrouping("spout");
		builder.setBolt("print", new PrintBolt()).shuffleGrouping("exclaim");
		Config conf = new Config();
		conf.setDebug(false);
		
		if(args != null && args.length > 0){
			conf.setNumWorkers(3);
			StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
		}else{
			LocalCluster cluster = new LocalCluster();
			cluster.submitTopology("test", conf, builder.createTopology());
			Utils.sleep(20000);
			cluster.killTopology("test");
			cluster.shutdown();
		}
	}
}

```



