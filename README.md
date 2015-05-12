# JIRA Rest Connector File to Issue example

+ [Use Case](#usecase)
+ [Run it!](#runit)
	* [Running on Studio](#runonstudio)
	* [Properties to be configured](#propertiestobeconfigured)
+ [Customize It!](#customizeit)
+ [Report an Issue](#reportissue)


### Author
Hotovo s.r.o.

### Supported Mule runtime versions
Mule 3.5.x or higher

### JIRA supported versions
JIRA 5.2 or higher

### Service or application supported modules
JIRA Rest API 5.2 or higher

# Use Case <a name="usecase"/>
This example application shows simplified use of the JIRA Rest Connector. It serves as a simple example about how to automatically 
create Issues from file input. It periodically polls a folder for new files and automatically creates issues from file input. 
The section immediately below describes the functions of each of the flows in the application. 

This example assumes that you are familiar with Mule, the Anypoint Studio interface, Global Elements, DataSense, and the DataMapper transformer. Further, it is assumed that you are 
familiar with JIRA and have a JIRA account. Learn more about flows and subflows to gain more insight into the design of this example.

# Run it! <a name="runit"/>
Follow these simple steps to get this example running

### Where to Download Anypoint Studio and Mule ESB
First thing to know if you are a newcomer to Mule is where to get the tools.

+ You can download Anypoint Studio from this [Location](http://www.mulesoft.com/platform/mule-studio)
+ You can download Mule ESB from this [Location](http://www.mulesoft.com/platform/soa/mule-esb-open-source-esb)

### Importing example into Studio
Mule Studio offers several ways to import a project into the workspace, for instance: 

+ Anypoint Studio generated Deployable Archive (.zip)
+ Anypoint Studio Project from External Location
+ Maven-based Mule Project from pom.xml
+ Mule ESB Configuration XML from External Location

You can find a detailed description on how to do so in this [Documentation Page](http://www.mulesoft.org/documentation/display/current/Importing+and+Exporting+in+Studio).

### Running on Studio <a name="runonstudio"/>
Once you have imported this example into Anypoint Studio you need to follow these steps to run it:

+ install Jira Rest Connector into Anypoint Studio [Installing connector](#installingConnector)
+ Locate the properties file `common.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Update issue-input.csv from src/main/resources accordingly. Project key `MULETEST` should be replaced with some existig Project key in your JIRA instance. 
+ Copy issue-input.csv from src/main/resources into your 'directory.source' folder
+ Once that is done, right click on project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application"`

## Installing connector <a name="installingConnector"/>
The following sections details how to use and install this module under different scenarios / environments.
To make the module available to a Mavenized Mule application, first add the following repositories to your POM:

```
<repositories>
    <repository>
        <id>mulesoft-releases</id>
        <name>MuleSoft Repository</name>
        <url>http://repository-master.mulesoft.org/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <id>mulesoft-snapshots</id>
        <name>MuleSoft Snapshot Repository</name>
        <url>http://repository-master.mulesoft.org/snapshots/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

Then add the module as a dependency to your project. This can be done by adding the following under the dependencies element your POM:

```
<dependency>
    <groupId>org.mule.modules</groupId>
    <artifactId>jirarest-connector</artifactId>
    <version>RELEASE</version>
</dependency>
```

or if you want to be on the bleeding edge

```
<dependency>
    <groupId>org.mule.modules</groupId>
    <artifactId>jirarest-connector</artifactId>
    <version>LATEST</version>
</dependency>
```

If you plan to use this module inside a Mule application, you need add it to the packaging process. That way the final ZIP file which will contain your flows and Java code will also contain this module and its dependencies. Add an special inclusion to the configuration of the Mule Maven plugin for this module as follows:

```
<plugin>
    <groupId>org.mule.tools</groupId>
    <artifactId>maven-mule-plugin</artifactId>
    <extensions>true</extensions>
    <configuration>
        <excludeMuleDependencies>false</excludeMuleDependencies>
        <inclusions>
            <inclusion>
                <groupId>org.mule.modules</groupId>
                <artifactId>jirarest-connector</artifactId>
            </inclusion>
        </inclusions>
    </configuration>
</plugin>
```


## Properties to be configured (With examples) <a name="propertiestobeconfigured"/>
In order make this example running you need to configure properties (Credentials, configurations, etc.) in common.properties file

```
### JIRA
jira.username=jirauser
jira.password=passwd
jira.host=https://example.atlassian.net

directory.source=path-to-csv-file-source-folder
directory.target=path-to-csv-file-backup-folder
```

# Customize It!<a name="customizeit"/>
This section contains a brief description about how this example works. Flow configuration file is separated in three disctinct flows to make it more readable. 

## mainflow
Inbound File connector periodically polls a directory for a new csv file in following structure
Please note that you should replace "MULETEST" to a valid Project key for your JIRA instance to run the flow correctly.

```
Summary,Description,IssueType,IssuePriority,ProjectKey
Summary 1,Description for first issue,1,3,MULETEST
...

```

Datamapper then converts each row fetched from the csv file to a map element. Each map element servers as input map for inserting new issue into configured Jira instance. Result of the operation is logged to console.

```
<mule xmlns:data-mapper="http://www.mulesoft.org/schema/mule/ee/data-mapper" xmlns:file="http://www.mulesoft.org/schema/mule/file" xmlns:context="http://www.springframework.org/schema/context" xmlns:jirarest="http://www.mulesoft.org/schema/mule/jirarest" xmlns="http://www.mulesoft.org/schema/mule/core" xmlns:doc="http://www.mulesoft.org/schema/mule/documentation"
	xmlns:spring="http://www.springframework.org/schema/beans" version="EE-3.6.1"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-current.xsd
http://www.mulesoft.org/schema/mule/core http://www.mulesoft.org/schema/mule/core/current/mule.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-current.xsd
http://www.mulesoft.org/schema/mule/jirarest http://www.mulesoft.org/schema/mule/jirarest/current/mule-jirarest.xsd
http://www.mulesoft.org/schema/mule/file http://www.mulesoft.org/schema/mule/file/current/mule-file.xsd
http://www.mulesoft.org/schema/mule/ee/data-mapper http://www.mulesoft.org/schema/mule/ee/data-mapper/current/mule-data-mapper.xsd">
    <context:property-placeholder location="common.properties"  />
    <jirarest:config-type name="JiraRest" username="${jira.username}" password="${jira.password}" serverUrl="${jira.host}" doc:name="JiraRest"/>
    <data-mapper:config name="CSV_To_Issue" transformationGraphPath="csv_to_issue.grf" doc:name="CSV_To_Issue"/>
    <flow name="mainFlow" >
        <file:inbound-endpoint path="${directory.source}" moveToDirectory="${directory.target}" responseTimeout="10000" doc:name="File"/>
        <data-mapper:transform config-ref="CSV_To_Issue" doc:name="CSV To Issue"/>
        <foreach doc:name="For Each">
            <jirarest:issues-issue-create config-ref="JiraRest" entityType="Issue" doc:name="insert Issue">
                <jirarest:payload ref="#[payload]"/>
            </jirarest:issues-issue-create>
            <logger message="#[payload]" level="INFO" doc:name="log response"/>
        </foreach>
        <exception-strategy ref="default-exception-strategy" doc:name="Reference Exception Strategy"/>
    </flow>
    <catch-exception-strategy name="default-exception-strategy">
        <logger message="#[payload]" level="ERROR" doc:name="Log exception"/>
    </catch-exception-strategy>
</mule>
```

# Report an Issue <a name="reportissue"/>
We use JIRA for tracking issues with this connector. You can report new issues at this location https://hotovo.jira.com
