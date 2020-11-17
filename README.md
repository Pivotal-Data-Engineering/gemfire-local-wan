<h1> VMware has ended active development of this project, this repository will no longer be updated.</h1><br># Setting Up a Local WAN Gateway

This project uses [gemfire-manager](https://github.com/Pivotal-Data-Engineering/gemfire-manager) to start a pair of 2 node clusters suitable for testing 
WAN gateway. It requires that you have python 2 installed on your local 
machine.

## Instructions 

- Set the `GEMFIRE` and `JAVA_HOME` environment variables. An example is shown 
	below.
	
	```
	export GEMFIRE=/usr/local/Cellar/gemfire/9.3.0/libexec
	export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_102.jdk/Contents/Home
	```
	
- Start Cluster 1
	
	```
	cd cluster1
	python cluster.py start 
	```
	
	You can access Pulse at [http://localhost:17070/pulse](http://localhost:17070/pulse).  
	
	Use `connect --locator=localhost[10000]` to connect to gfsh.
	
	All settings for cluster 1 are in `cluster1/cluster.json`
	
- Start Cluster 2
	
	```
	cd cluster2
	python cluster.py start 
	```
	
	You can access Pulse at [http://localhost:27070/pulse](http://localhost:27070/pulse).  
	
	Use `connect --locator=localhost[20000]` to connect to gfsh.
	
	All settings for cluster 2 are in `cluster2/cluster.json`
	
- Creat a test region and a receiver in cluster2.

	```
	gfsh>connect --locator=localhost[20000]
	gfsh>create gateway-receiver --start-port=29001 --end-port=29009	gfsh>gfsh create region --name=test --type=PARTITION_REDUNDANT
	```
	
- Create test region and a sender in cluster1.

	```
	gfsh>connect --locator=localhost[10000]
	gfsh>create gateway-sender --id=test-sender --parallel=true --remote-distributed-system-id=2
	create region --name=test --type=PARTITION_REDUNDANT --gateway-sender-id=test-sender
	gfsh>list gateways
	```
	
- Put an entry in the cluster 1 test region.

	```
	gfsh>put --region=test --key=hello --value=world
	```
	
- Connect to the second cluster and verify that the entry is there.

	```
	gfsh>get --region=test --key=hello
	```
	
	

	
	