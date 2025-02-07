# Liveview Metric Store

TIBCO LiveView Web provides various visualizations to represent data. Using TIBCO LiveView Web you can also perform aggregations over this data. The visualizations are updated in real time based on the changes to the underlying entities such as concepts, events, or metrics of the TIBCO BusinessEvents projects.
The charts could be pie charts to show status, bar charts to display trends in changing values, and so on. You can integrate TIBCO LiveView Server with TIBCO BusinessEvents projects of cache OM and in-memory type.

The following entities of a TIBCO BusinessEvents project are supported:
* Concepts
* Events
* Scorecards

History for concepts, metric URLs, and tracking fields are not supported.

There are three main components of this integration:
* TIBCO Streaming
  * TIBCO LiveView Server
  * TIBCO Liveview Web
* TIBCO BusinessEvents Enterprise Edition

Reference link of sample example is as follows :

* [FraudDetectionLiveView](https://github.com/tibco/be-samples/tree/main/FraudDetectionLiveView)

## High-Level Architecture

The inference agent publishes metrics to TIBCO LiveView Server which acts as the metric store. The metrics in this case can be entities like concepts and events defined in TIBCO BusinessEvents. These are then used for the TIBCO LiveView dashboards that help to visualize the metric data stored in the metric store.

## Displaying BusinessEvents Project Entities in LiveView Web

TIBCO LiveView Web dashboard is used to showcase various visualizations based on the data sent from TIBCO BusinessEvents.
Based on the project configuration, TIBCO BusinessEvents generates a LiveView project, containing LiveView configuration files (that is, LiveView Tables) corresponding to TIBCO BusinessEvents entities (concepts, events, or metrics). The id of the entities is used as primary key for these LiveView tables. TIBCO LiveView Web dashboard then uses data from the LiveView tables, and based on the visualization setup, displays the project metrics in different charts.

## Reference Documents

* Go through the [Custom Metric Store API](https://docs.tibco.com/emp/businessevents-enterprise/6.3.0/doc/html/api/javadoc/com/tibco/cep/runtime/appmetrics/package-summary.html) documentation.

* Go through the developer guide [here](https://docs.tibco.com/emp/businessevents-enterprise/6.3.0/doc/html/Configuration/Custom-Application-Metrics-Store.htm) to get more details around the various classes/interfaces involved and how to set it up.

## Pre-requisites

* Install TIBCO LiveView Server and setup LiveView web with it.

* Since Liveview is dependent on SB classpath and as its jars are already removed, a property for SB home is set in pom.xml of Liveview Metric Store which is used to get the required jars (sbclient.jar and lv-client.jar).

## Getting Started

* If a new metric store jar is needed, follow these [instructions](https://github.com/tibco/be-contribution) to clone/update/build a new jar.
* Configure the application metrics for the metric store and add an inference agent for it.
* Save the project.
* Generate the LiveView configuration (.lvconf) files for the project using the studio-tools utility.
* Start the TIBCO LiveView server with the location of the generated LiveView configuration files for the project.
* Start the BusinessEvents engine with the BusinessEvents project configured for LiveView.
* Open the LiveView Web URL (such as, http://localhost:10080/lvweb) to view the LiveView Web UI.
  If an ACL is configured, there will be an authenticated login else, you are directly taken to the dashboard.
* In the dashboard, you can create a page and card, while adding various visualization, aggregation, and alerts over the LiveView tables.

## Generating LiveView Configuration Files Using BusinessEvents Studio Tools Utility

Using the TIBCO BusinessEvents Studio Tools utility you can manually generate LiveView Configuration (.lvconf) files for the BusinessEvents project.
A separate LiveView configuration file is generated for each entity that is configured to be pushed to the TIBCO LiveView server.

### Procedure

* Navigate to BE_HOME/studio/bin/ and open a command prompt.
* Execute a command with the following format (all on one line) at a command prompt:
  ```java
  studio-tools -core generateLDMProject [-h] -o outputDirectory -p studioProjDir  -c CDDFilePath 
  ```

## LiveView Configuration File Reference

The LiveView configuration file is generated by TIBCO BusinessEvents representing a BusinessEvents entity (such as, concept, event, or metric). The LiveView configuration file is used by LiveView Server to create the corresponding LiveView tables.
For each entity that is configured in the LiveView agent, a XML configuration file is generated. Each property of the instance is transformed to a field of LiveView table. The configuration file also record the datatype of each property. The id field is used as the primary key and the extid field is used as the secondary key for the table. Additional indexes for fields can be added based on the Properties Metadata section in the CDD file (Cluster > Object Management > Domain Object > Overrides).
The following sample shows the XML configuration for the an example concept Account:
```java
<?xml version="1.0" encoding="UTF-8"?>
<liveview-configuration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://www.streambase.com/schemas/lvconf/">
<data-table id="Concepts_Account">
<fields>
<field name="Balance" type="double"/>
<field name="Debits" type="int"/>
<field name="Status" type="string"/>
<field name="AvgMonthlyBalance" type="double"/>
<field name="Created" type="timestamp"/>
<field name="Updated" type="timestamp"/>
<field name="Address" type="long"/>
<field name="FraudAttempt" type="int"/>
<field name="Reason" type="string"/>
<field name="DebitUpdate" type="timestamp"/>
<field name="DebitCount" type="int"/>
<field name="id" type="long"/>
<field name="extId" type="string"/>
<field name="version__" type="int"/>
<field name="typeId__" type="int"/>
<field name="deleted__" type="bool"/>
</fields>
<primary-key>
<field ref="id"/>
</primary-key>
<indices>
<index>
<field ref="extId"/>
</index>
</indices>
</data-table>
</liveview-configuration>
```

## Authentication and SSL Configuration Between BusinessEvents and LiveView Server

The TIBCO BusinessEvents supports both one-way (server side) and two-way (server side and client side) SSL authentication with TIBCO LiveView server.

### Setting up One-Way SSL Between BusinessEvents and LiveView Server

You can set a one-way SSL connection between TIBCO BusinessEvents and LiveView server by adding SSL properties in the be-engine.tra file.

### Procedure

* Open the BE_HOME\bin\be-engine.tra file for editing.
* Add the following properties and their values in the be-engine.tra file:
  ```java
  java.property.javax.net.ssl.trustStore=<location of the truststore file>
  java.property.javax.net.ssl.trustStorePassword=<password of the truststore file>
  ```
* Save the file and start the BusinessEvents engine with this (be-engine.tra) file to establish secure connection to LiveView server.

### Setting up Two-Way SSL Between BusinessEvents and LiveView Server

You can set a two-way SSL connection between BusinessEvents and LiveView server by adding SSL properties in the be-engine.tra file.

### Procedure

* Open the BE_HOME\bin\be-engine.tra file for editing.
* Add the following properties and their values in the be-engine.tra file:
  ```java
  java.property.javax.net.ssl.trustStore=<location of the truststore file>
  java.property.javax.net.ssl.keyStore=<location of the keystore file>
  java.property.javax.net.ssl.keyStorePassword=<password of the keystore file>
  java.property.javax.net.ssl.trustStorePassword=<password of the truststore file>
  ```
* Save the file and start the BusinessEvents engine with this (be-engine.tra) file to establish secure connection to LiveView server.

## Configuring TIBCO LiveView as a Metric Store

To configure TIBCO LiveView as a metric store, configure the inference agent, TIBCO LiveView server, and entities which you want to publish to the TIBCO LiveView Web dashboard.
By using the inference agent you can connect the TIBCO BusinessEvents project to the TIBCO LiveView server.
The inference agent is added to a processing unit similar to how other agents are added to the processing unit.

### Procedure

* In the TIBCO BusinessEvents Studio, open the project CDD file for editing.
* Add the Inference agent class.
* On the Cluster tab, select Application Metrics and configure the following metrics:
  
| Configuration    | Description                                                                             |
|:-------------    |:-------------------------------------------------------------------------               |
| Queue Size       | Specify the size of the queue to limit the amount of data you want to accept while      |
|                  | publishing to the TIBCO LiveView.                                                       |
|                  | The Queue Size and Thread Count fields are used for scaling purposes depending on the   |
|                  | data size and load.                                                                     |
| Thread Count     | Specify the number of threads for starting publishing the data to the TIBCO             |
|                  | LiveView. You need multiple threads to publish data to TIBCO LiveView concurrently.     |
| Max Retries      | Specifies the maximum number of retry attempts for connecting to the TIBCO LV server.   |
| Retry Wait Time  | Specifies the time interval before retrying to connect to the TIBCO LiveView server.    |
| Properties       | Add properties and specify the values as required.                                      |

* On the Cluster tab, under Application Metrics, select Metrics Store and provide values for the following configurations.

| Configuration    | Description                                                                  |
|:-------------    |:-------------------------------------------------------------------------    |
| Store Provider   | Select LiveDataMart.                                                         |
| LDM Url          | Specify URL of the TIBCO LiveView server.                                    |
| User Name        | User name for the TIBCO LiveView server.                                     |
| Password         | Password for the User Name.                                                  |
| Initial Size     | Specify the initial size of the connection pool.                             |
| Max Size         | Specify the maximum size of the connection pool.                             |

* Specify entities which you want to publish to TIBCO LiveView server. You can also specify a filter to select only those instances which qualifies the filter.
* Save the project.

## Adding Entity Configurations for Metric Stores

You can use TIBCO BusinessEvents Studio to specify entities that you want to publish to the TIBCO LiveView Web dashboard. You can specify a filter to select only those instances which qualify the filter. Also, for the TIBCO LiveView integration you can specify a trimming rule to trim the LiveView table based on the rule.

### Procedure

* In the BusinessEvents Studio, open the project CDD file for editing and open theCluster tab.
* From the Application Metrics list, select Entity Configurations, and click Add to add an entity configuration.
* Specify the value for the following fields for the new entity and save the agent.

| Field            | Description                                                                             |
|:-------------    |:-------------------------------------------------------------------------               |
| Entity Uri       | Browse and select the supported entity that you want to send to the publisher based on  |
|                  | your filter. The supported entities are concepts, events, and scorecards.               |
| Entity Filter    | Specify a query to filter out the entity based on your requirement.                     |
|                  | For example, the following values specifies to send the Account concept to the publisher|
|                  | only when Balance is greater than 10000.                                                |
|                  | Entity Uri - /Concepts/Account                                                          |
|                  | Entity Filter - Balance > 10000                                                         |
|                  | If this field is empty, then all instances of the entity are pushed to the LV server.   |
| Trimming         | Specify name for an optional timestamp field for your entity.                           |
| TimeStamp Field  | If your entity does not have a timestamp field to write the trimming rule,  you can use |
|                  | this field to create a new timestamp field for the entity. This field is part of the    |
|                  | LiveView table after generating the LiveView configuration file.                        |
| Trimming Rule    | Specify a rule to omit the entity data from the LiveView table that matches the         |
|                  | trimming rule. The rule can use any field within the entity specified in the Entity Uri |
|                  | field.  If required, you can also use Trimming TimeStamp Field for creating rules.      |
|                  | eg. OrdStatus=='BAD' when ArrivalTime between epoch() and now()-seconds(6)              |
|                  | where, OrdStatus is a property of a concept named Account,                              |
|                  |  ArrivalTime is a property defined in the Trimming TimeStamp Field field.               | 

## Main Product Components and Add-On

In addition to the core functionalities, TIBCO Businessevents also provides advanced components for different use cases:

### Live DataMart Plugin 

Live DataMart Plugin provides the capability to use TIBCO LiveView Web to display real-time web-based dashboards that give visibility into the data flowing through a running TIBCO BusinessEvents application, using meaningful metrics that are presented to business users for proactive decision making.

## Dashboard Agents

Dashboard agents are similar to a query agent in that their role is to generate information based on queries. The information is made available to the TIBCO LiveView Web or any configured application metrics store dashboard.
