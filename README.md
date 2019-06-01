
# OPC UA Remote Monitoring

**OPC Unified Architecture (OPC UA)** is a machine to machine communication protocol for industrial automation developed by the OPC Foundation. Distinguishing characteristics are: Focus on communicating with industrial equipment and systems for data collection and control. _...[OPC Unified Architecture - Wiki](https://en.wikipedia.org/wiki/OPC_Unified_Architecture)_

## Overview

Make the manufacturuing data available so that it can be visialized and trigger alerts. Read tag values from OPC UA Server and also write values to OPC UA Server.

1. OPC UA Server - Node-RED OPCUA Server
2. OPC UA Agent/Client - Node-RED OPCUA Client  
Presumely it will be deployed to access the local network to interact with OPC UA Server(s)
3. Remote Monitor - Blynk
Critical information should be delivered for an immediate attention
4. Visualization - InfluxDB & Grafana

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-fan-simulation.gif" width="700px"/>
</p>

## OPC UA Server

Install `node-red-contrib-opcua` from "Manage palette"

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-server.png" width="700px"/>
</p>

**104-opcuaserver.js**

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
## OPC UA Agent/Client

Install `node-red-contrib-opcua` from "Manage palette"

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-agent.png" width="700px"/>
</p>

## Remote Monitor and Control : Blynk 

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-remote-fan-monitor.gif" width="300px"/>
</p>

## Visualization

<p align="center">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/opc-ha-grafana-dashboard.gif" width="700px"/>
</p>

1. Install `node-red-contrib-influxdb` from "Managae palette"
2. Install InfluxDB
> brew install  influxdb
3. Install Grafana
> brew install grafana
4. Start Services
> brew services start influxdb grafana /* brew tap homebrew/services if necessary */

The following data were added to `temperature` measurement. 

<p align="left">
<img src="https://github.com/phyunsj/opc-ua-monitoring/blob/master/images/influxdb_measurement.png" width="400px"/>
</p>


Conceptually you can think of a measurement as an SQL table, where the primary index is always time.tags and fields are effectively columns in the table. tags are indexed, and fields are not.

Created multiple regions to visualize the regional data. `east_[2|3|4|5|6]` were also simulated. The following `msg.payload` was sent to `influxdb batch` node.

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
