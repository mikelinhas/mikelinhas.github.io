---
layout: post
title: Secomea + AWS IoT
description: Collecting process data from PLCs and storing it in AWS DynamoDB.
techstack: [JavaScript, AWS-IoT, AWS-Lambda, DynamoDB, NodeJS]
---

---

## Introduction

This project started as a way to securely connect to all our devices installed in each of our customer's production centers. In many of them, SECOMEA was becoming a standard entry point due to its ease of use and installation. The VPN would connect through SECOMEA and  access the process PLC network.

Additionally, SECOMEA includes a Data Collection Module which bundles up the data from the PLC devices and publishes them to your IoT service of choice, basically becoming an **IoT Gateway**. This feature led to a small data collection project in AWS.

<p class="center" style="margin-top: 60px"> The main structure of the project was </p>

<div class="center-div" style="max-width: 500px; margin-top: 20px;">
	<img src="/assets/images/AWS-IoT.png" title="AWS IoT" alt="AWS IoT">
</div>


---

## Project Outline

### Secomea - Data Collection Module
The Secomea Site Manager can be integrated with a Module called Data Collection Module to retrieve and emit data from the running PLCs. This is done by configuring `COLLECTORS` (PLCs) and `DATA SERVERS` (AWS IoT).

To establish a PLC as a `COLLECTOR` we need to determine how to communicate with it and what data we are going to retrieve from it. An example configuration would be by using the proprietory Siemens TCP protocol as seen below. In this step we also configure the data which we are going to read.

```json
"Collectors": [
 {"CollectorName": "PLC",
  "Protocol": "S7/TCP",
  "S7Access": {
    "S7Model": "S7-1200",
    "S7Rack": 0,
    "S7Slot": 1
   },
   "SamplePoints": [
    {"SampleName": "VARIABLE-1",
     "SampleDataType": "bool",
     ...},
    {"SampleName": "VARIABLE-2",
     "SampleDataType": "integer",
     ...}
   ]
  }
]
```

<div style="height: 50px"></div>

To establish a AWS as a `DATA SERVER` we need to configure the protocol and save the Certificate Key, Private Key, and RootCA where the SECOMEA gateway can find it. An example configuration would be as seen below:


```json
"DataServers" : [
  {"DatasrvName": "AWS",
   "DatasrvProtocol": "“MQTT/AWS", 
   "IOPin": true,
   "MQTTAWSParams": {
      "BrokerAddr": "xxxx-ats.iot.eu-west-3.amazonaws.com",
      "BrokerPort": 8883, 
      "RootCA": "AmazonRootCA1.pem", 
      "DeviceCert": "ca2afb96f3-certificate", 
      "DevicePrivKey": "ca2afb96f3-private",
      "PublishInterval": 20,
      ...
    }
  }
 ] 
```

---

### AWS IoT Core - Data Streaming

##### Registering the SECOMEA IoT Gateway as a "thing"

The first step to read data coming from the Data Collection Module is to **resgister a thing** in the AWS IoT Core service. By doing this, AWS will give the thing an Amazon Resource Name - `arn` - which will be needed further on.

<div class="center-div" style="max-width: 700px; margin-top: 40px; margin-bottom:70px;">
  <p class="image-subtitle"> Adding a thing in AWS IoT </p>
	<img src="/assets/examples/secomea-thing.png" title="IoT Secomea Thing" alt="IoT Secomea Thing">
</div>


##### Testing the SECOMEA stream 

Before we head on to configure the lambda triggers, we will test to see if the SECOMEA device is publishing the data correctly. To do so, AWS gives us a useful "Test" tool where we can **subscribe** to the SECOMEA stream.

<div class="center-div" style="max-width: 700px; margin-top: 40px; margin-bottom:50px;">
  <p class="image-subtitle"> Configuring IoT test</p>
	<img src="/assets/examples/secomea-test.png" title="IoT Secomea Test" alt="IoT Secomea Test">
</div>

<br />

By doing this, we are now listening to the SECOMEA topic stream. As soon as the SECOMEA device publishes on that topic, it will appear in the interface.


<div class="center-div" style="max-width: 500px; margin-top: 40px; margin-bottom:70px;">
  <p class="image-subtitle"> Testing SECOMEA device</p>
	<img src="/assets/examples/secomea-topic-test.png" title="IoT Secomea Topic" alt="IoT Secomea Topic Test">
</div>


##### Creating an action for each publish

Now we are ready to create an **action**. When doing this we will create a simple lambda function called `writeToDynamoDB`, which we will modify later on.

<div class="center-div" style="max-width: 700px; margin-top: 40px; margin-bottom:70px;">
  <p class="image-subtitle"> Configuring IoT action</p>
	<img src="/assets/examples/secomea-action.png" title="IoT Secomea Action" alt="IoT Secomea Action">
</div>

---

### AWS DynamoDB

##### Creating the DynamoDB database table

Creating a DynamoDB database is straightforward in the AWS console. A table must have at least one `primary key`, which can be the `date_time` of each SECOMEA publish.

Once the table is created, we will see the Amazon Resource Name for it: `arn`.

---

### AWS Lambda

##### IAM Role Configuration

The lambda function will need the following `ALLOW` policies to work correctly: 

* `Basic Lambda Execution Role` policies
* `dynamodb:PutItem` policy for the created DynamoDB Table 

We will name this the `LambdaDynamoDB` role and apply it to the Lambda Function.


##### Configuring the Lambda function Trigger

The trigger was created after creating the **action** in the IoT Core service. Inside the Lambda function it looks like this:

<div class="center-div" style="max-width: 700px; margin-top: 40px; margin-bottom: 70px;">
  <p class="image-subtitle"> Lambda trigger visualization</p>
	<img src="/assets/examples/lambda-trigger.png" title="Lambda IoT Trigger" alt="Lambda IoT Trigger">
</div>


##### The Lambda Function itself

The NodeJS code that saves the IoT publish is pretty simple. It handles the event and saves the data in the `Secomea` table, after appending the `date_time` value.

```javascript
'use strict';

const AWS = require('aws-sdk');

AWS.config.update({ region: "eu-west-3"});

exports.handler = async (event, context) => {
  const documentClient = new AWS.DynamoDB.DocumentClient({ region: "eu-west-3"});
  const params = {
    TableName: "Secomea",
    Item: {
      date_time: new Date(),
      pressure: event.pressure,
      temperature: event.temperature
    }
  };
  
  try {
    const data = await documentClient.put(params).promise();
  } catch (err) {
    console.log(err);
  }

};
```

---

There it is, industrial PLCs connecting over AWS IoT to a NoSQL database! 



