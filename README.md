
# OPC UA Remote Monitoring

**OPC Unified Architecture (OPC UA)** is a machine to machine communication protocol for industrial automation developed by the OPC Foundation. Distinguishing characteristics are: Focus on communicating with industrial equipment and systems for data collection and control. _...[OPC Unified Architecture - Wiki](https://en.wikipedia.org/wiki/OPC_Unified_Architecture)_

## Overview

Make the manufacturuing data available so that it can be visialized and trigger alerts. 

1. OPC UA Demo
2. OPC UA Server 
3. OPC UA Agent/Client :  Presumely it will be deployed on the local network to interact with OPC UA Server(s)
4. Remote Monitor :  Critical information should be delivered for an immediate attention
5. Visualization 

## OPC UA Demo

In order to keep the temperature under control, at least two fans should be functional. Otherwise, the temperature goes up gradually (max 100). OPC UA agent sends an alert message at a certain level (re-sends if the temperature is still high after timeout) and waits for the next action (e.g., turn on the backup unit) 

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-fan-simulation.gif" width="700px"/>
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-remote-fan-monitor.gif" width="300px"/>
</p>

1. OPC UA Agent monitors **Temperature** and 3 **Fan**s.
2. Disable Fan #1 from OPC UA Server. (simulating hardware malfunction)
3. Send an alert message.
4. Turn on the backup unit (Fan #3). **Auto-remediation** should be triggered in real world. 

## OPC UA Server

Install `node-red-contrib-opcua` from "Manage palette"

**104-opcuaserver.js** : 4 variables are added for this demo.

```
           addressSpace.getOwnNamespace().addVariable({
                componentOf: vendorName,
                nodeId: "ns=1;s=Temperature",
                browseName: "Temperature",
                dataType: "UInt16",

                value: {
                    get: function () {
                        return new opcua.Variant({
                            dataType: opcua.DataType.UInt16,
                            value: Temperature
                        });
                    },
                    set: function (variant) {
                        Temperature = opcuaBasics.build_new_value_by_datatype(variant.dataType, variant.value);
                        return opcua.StatusCodes.Good;
                    }
                }
            });

            addressSpace.getOwnNamespace().addVariable({
                componentOf: vendorName,
                nodeId: "ns=1;s=Fan1Status",
                browseName: "Fan1Status",
                dataType: "UInt16",

                value: {
                    get: function () {
                        return new opcua.Variant({
                            dataType: opcua.DataType.UInt16,
                            value: Fan1Status
                        });
                    },
                    set: function (variant) {
                        Fan1Status = opcuaBasics.build_new_value_by_datatype(variant.dataType, variant.value);
                        return opcua.StatusCodes.Good;
                    }
                }
            });
            // Same for ns=1;s=Fan2Status, ns=1;s=Fan3Status
```

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-server.png" width="700px"/>
</p>

## OPC UA Agent/Client

Install `node-red-contrib-opcua` from "Manage palette"

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-agent.png" width="700px"/>
</p>

## Remote Monitor and Control

[Blynk](https://blynk.io/) lets you connect your devices to the cloud, design apps to control them, analyze telemetry data, and manage your deployed products at scale. 

| Blynk Widget | Blynk  Pin    | OPC UA Variable | 
| --|------------- | ------------- |
| SuperChart|Virtual Pin 0  | ns=1;s=Temperature |
| Button(Switch)|Virtual Pin 1  | ns=1;s=Fan1Status  |
| Button(Switch)|Virtual Pin 2  | ns=1;s=Fan2Status  |
| Button(Switch)|Virtual Pin 3  | ns=1;s=Fan3Status  ||

Only Virtual Pin 3 is writable for this demo.

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/blynk-settings.gif" width="300px"/>
</p>

## Visualization

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-grafana-dashboard.gif" width="700px"/>
</p>

1. Install `node-red-contrib-influxdb` from "Manage palette"
2. Install InfluxDB
>`$ brew install  influxdb`
3. Install Grafana
>`$ brew install grafana`
4. Start Services ( `$ brew tap homebrew/services` if necessary ) 
>`$ brew services start influxdb grafana` 

The following data were added to `temperature` measurement. Use `drop measurement <measurement-name>` to delete.

<p align="left">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/influxdb_measurement.png" width="400px"/>
</p>


Conceptually you can think of a measurement as an SQL table, where the primary index is always time.tags and fields are effectively columns in the table. tags are indexed, and fields are not.

Created multiple regions to visualize the regional data. region `east_[2|3|4|5|6]` were also simulated. The following `msg.payload` was sent to `influxdb batch` node.

```
return  { payload : [
          {
                      measurement: "temperature",
                      fields: {
                                 temp: value - value2
                      },
                      tags:{
                                 region: "east-2",
                                 ipaddr: "192.123.22.34"
                      }
         },
         {
                      measurement: "temperature",
                      fields: {
                                  temp: value + value2
                      },
                      tags:{
                                 region: "east-3",
                                 ipaddr: "192.123.23.34"
                      }
         },
         ...
         ]       
```


#### Related Links

- [OPC Foundation](https://opcfoundation.org/about/opc-technologies/opc-ua/)
- [OPC UA Stack  1.0.0.125](http://documentation.unified-automation.com/uasdkhp/1.0.0/html/index.html)
- [Tutorial: Using OPC-UA with FRED](http://developers.sensetecnic.com/article/tutorial-using-opc-ua-with-fred/)
- [NodeOPCUA - OPC UA stack fully written in javascript and NodeJS](http://node-opcua.github.io/)
- [InfluxDB - the open source time series database](https://www.influxdata.com/)
- [Grafana - The ohttps://www.influxdata.com/pen platform for analytics and monitoring](https://grafana.com/)
- [Open Source OPC UA Application Server and OPC UA Client/Server C++ Libraries](https://github.com/ASNeG/OpcUaStack)
