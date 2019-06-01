
# OPC UA Remote Monitoring

**OPC Unified Architecture (OPC UA)** is a machine to machine communication protocol for industrial automation developed by the OPC Foundation. Distinguishing characteristics are: Focus on communicating with industrial equipment and systems for data collection and control. _...[OPC Unified Architecture - Wiki](https://en.wikipedia.org/wiki/OPC_Unified_Architecture)_

## Overview

Make the manufacturuing data available so that it can be visialized and trigger alerts. Read tag values from OPC-HA and also write values to OPC HA.

1. OPC HA Server - Node-RED OPCUA Server
2. OPC HA Agent/Client - Node-RED OPCUA Client  
Presumely it will be deployed to access the local network to interact with OPC UA Server(s)
3. Remote Monitor - Blynk
Critical information should be delivered for an immediate attention
4. Visualization - InfluxDB & Grafana

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-fan-simulation.gif" width="700px"/>
</p>

## OPC HA Server

Install `node-red-contrib-opcua` from "Manage palette"

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-server.png" width="700px"/>
</p>

## OPC HA Agent/Client

Install `node-red-contrib-opcua` from "Manage palette"

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-agent.png" width="700px"/>
</p>

## Remote Monitor and Control : Blynk 

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-remote-fan-monitor.gif" width="300px"/>
</p>

## Visualization

- Install `node-red-contrib-influxdb` from "Managae palette"
- Install InfluxDB
> brew install  influxdb
- Install Grafana
> brew install grafana

- Start Services
> brew tap homebrew/services
> brew services start influxdb
> brew services start grafana

For OPA-HA demo, the following data were added to `temperature` measurement. 

> insert temperature, region=east_1,host=IP address  value=89

Conceptually you can think of a measurement as an SQL table, where the primary index is always time.tags and fields are effectively columns in the table. tags are indexed, and fields are not.

Created multiple regions to visualize the regional data. Only `east_[2|3|4|5|6]` were simulated from OPC UA Agent. 



#### Related Links

- [OPC Foundation](https://opcfoundation.org/about/opc-technologies/opc-ua/)
- [OPC UA Stack  1.0.0.125](http://documentation.unified-automation.com/uasdkhp/1.0.0/html/index.html)
- [Tutorial: Using OPC-UA with FRED](http://developers.sensetecnic.com/article/tutorial-using-opc-ua-with-fred/)
- [NodeOPCUA - OPC UA stack fully written in javascript and NodeJS](http://node-opcua.github.io/)
- [InfluxDB - the open source time series database](https://www.influxdata.com/)
- [Grafana - The ohttps://www.influxdata.com/pen platform for analytics and monitoring](https://grafana.com/)
- [Open Source OPC UA Application Server and OPC UA Client/Server C++ Libraries](https://github.com/ASNeG/OpcUaStack)
