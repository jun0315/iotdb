<!--

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at
    
        http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.

-->

## 1. Switch the schema 

Suppose your originally InfluxDB  accessed code as follows:

```java
InfluxDB influxDB = InfluxDBFactory.connect(openurl, username, password);
```

To switch services to loTDB ，you only need to switch InfluxDBFactory into **IoTDBInfluxDBFactory**

```java
InfluxDB influxDB = IoTDBInfluxDBFactory.connect(openurl, username, password);
```

## 2. Schema design

### 2.1 InfluxDB - Protocol adapter

Based on the IoTDB Java Session interface, the adapter implements the Java interface `Interface InfluxDB` and provides users with interface methods of all InfluxDB.
End users can use InfluxDB to initiate write and read requests to IoTDB without being aware.

![architecture-design]()

![class-diagram]()



### 2.2 Metadata format conversion

 The metadata of InfluxDB is tag-field model, while the metadata of loTDB is tree model. To enable the adapter to be compatible with the InfluxDB protocol, the InfluxDB metadata model needs to be converted to the IoTDB metadata model.

#### 2.2.1 InfluxDB metadata

The following shows the structure of the InfluxDB metadata:

1. **database**: the name of database
2. **measurement**: the name of measurement index
3. **tags**: properties with index
4. **fields**: **recordings**(properties without index)

![influxdb-data]()

#### 2.2.2 IoTDB metadata

The following shows the structure of the IoTDB metadata:

1. **storage group**： **存储组**
2. **path**(time series ID)：the store path
3. **measurement**： **物理量

![iotdb-data]()

#### 2.2.3 Mapping between InfluxDB metadata and IoTDB metadata

The InfluxDB metadata and IoTDB metadata are mapped as follows:  

1. The database and measurement in the InfluxDB are combined as the storage group in the IoTDB.
2. The field key in the InfluxDB serves as the measurement path in the IoTDB, and the field value in the InfluxDB is the measured point value recorded in the path.
3. The tags in InfluxDB are expressed in IoTDB using a path between storage group and measurement.
   The tag key of the InfluxDB is implicitly expressed by the order of the paths between the storage group and measurement. The tag value is recorded as the name of the path in the corresponding order.

