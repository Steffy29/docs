---
title: Configure your Data Collector project
slug: datacollector
excerpt: Configure a Data Collector
section: Getting started - Data Collector
order: 2
---

**Last updated 31th May, 2018**

## Configure your data collector

Once you have ordered an agent, you should receive several information : 

* The **agent ID** you just ordered
* If necessary, your **tenant ID and the password** associated with it

But the agent that has been created is an empty shell and still need configuration before you can use it.

The aim of this guide is, therefore, to help you in configuring your new data collector.

## Using Data Convergence API to configure your data collector

First, you will have to generate a JWT token which will be signed and contains all the claims allowing you to use certain routes and not others. By default, any new tenant can use all routes for the Data Collector API but this might be restricted later if some endpoints are created for administration purposes, for instance.

> **Warning** :  Your JWT token is limited in time (24 hours). 

### Create JWT token

* Go, with your browser, to the following URL: [https://api.dataconvergence.ovh.com](https://api.dataconvergence.ovh.com)
![Create a JWT token](images/create_jwt_token.png "Create a JWT token")
* In the Authorization field, enter "Basic " followed by "your username:your password" encoded in Base64.
    
    * To encode in Base64, you can use the following command in linux:
    
    ```
        echo -n 'your username:your password' | base64
    ```
    
    * For example, "echo -n 'user-tutorial:password-tutorial' | base64" will gives us "dXNlci10dXRvcmlhbDpwYXNzd29yZC10dXRvcmlhbA==", so the Authorization field will contain "Basic dXNlci10dXRvcmlhbDpwYXNzd29yZC10dXRvcmlhbA" 
    
* Now, click on "Execute"
* Now, in the Response Body, you should see your JWT token, generated for these credentials. Keep it somewhere to avoid having to regenerate one.

### List your collectors

* Click on the "GET /collectors" route from the "Data Collector" section. It should look similar to this:
![List all collectors](images/list_all_collectors.png "List all collectors")
* In the Authorization field, enter "Bearer " followed by the JWT token generated in the previous step.
* Click on "Execute" and check the resulting IDs in the Response Body.

### Select source

For the next step, you need to configure a data source for your Data Collector.

* First, start by listing all the currently supported sources. Expand the "**GET /collectors/{agentId}/sources/availableSources**"
![List available sources](images/list_available_sources.png "List all available sources")
* Fill in the Authorization field with "Bearer " followed by the JWT token generated in the first step.
* Fill in the agentId field with the agent you want to configure, for example you can fill the agent ID in the result from the previous step
* Click on "Execute" and check the response body to select the source you want to use.
* Here, we are going to choose to configure a "mysqlQuery" source. This source requires the following information, as specified in the previous response :
![Available sources](images/available_sources.png "Available sources")
* Open the "**POST /collectors/{agentId}/sources/{source}**" route and fill in the information (Authorization as before, same for agentId and "MysqlQuery" in "source"). In the "body" field, enter the JSON data from the "MysqlQuery" JSON from the previous call:

```
{
    "name": "your-source",
    "user": "your-source-user",
    "password": "your-source-password",
    "host": "your-host-ip",
    "port": 3306
}
```
    
* Then Click on "Execute" and check the response body. It should look like this:
![Source result](images/source_result.png "Source result")
**Note**: the password is not in the response for obvious security reasons.

> To make sure everything is okay, open the "**GET /collectors/{agentId]/sources/{source}**" route and fill the information (Authorization as before, same for agentId and "your-source" in "source").
Click on "Execute" and it should give you the resulting data:
![Resulting source](images/resulting_source.png "Resulting source")


### Select sink

For the next step, you need to prepare the sink for your data collector. As of now, only a Kafka sink, configured by OVH, is available.
*  To be sure that no other sinks are available, we will start by calling the "**GET /collectors/{agentId}/sinks/availableSinks**" route, which should give you the following response:
![Available sink](images/available_sink.png "Available sink")
* Now, just call the "**POST /collectors/{agentId}/sinks/{sink}**" route with "ovhkafka" in the "sink" field and the "body" field with this :

```
{
"name": "your-sink"
}
```

* It should give you the following response:
![Sink result](images/sink_result.png "Sink result")
* As for the sources, let's check the configuration of the sink (which will allow you to get data on how to read events from the kafka queue created for you). Call the "**GET /collectors/{agentId}/sinks/{sink}**" route with the right parameters, which should give you the following response:
![Resulting sink](images/sink_result.png "Resulting sink")
* With this, you now all information necessary to connect to your Data Collector's Kafka topic

### Deploy your agent

The last step is to deploy your agent !

Just launch the "**PUT /collectors/{agentId}/deploy**" and... wait for a **200** Response Body !

And you are done ! Your Data Collector is now configured and up and running. If your data source is accessible, you should now see your events arriving into Kafka ! 
