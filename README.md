Last updated: Jia Zhang, 9/24/2013

EXECUTIVE SUMMARY
=================
On top of CMU SensorAndrew, the largest nation-wide campus sensor network, our Sensor Data and Service Platform (SDSP) 
aims to build a software service layer serving sensor data service discovery, reuse, composition, mashup, provisioning, and 
analysis. Another major objective of our project is to support SDSP-empowered innovative application design and 
development. Supported by a cloud computing enrironment with high-performance database, SDSP provides a platform 
to enable and facilitate a variety of research projects at CMUSV in the areas of mobile services, internet of things, 
cloud computing, big data analytics, software as a service, and social services.

CMUSV Sensor Data Service Platform
===================================
Service URL:
------------
[http://einstein.sv.cmu.edu][1]


Overview:
---------
Currently we are providing APIs in 3 categores:

**Category 1: Post sensor readings**<br/>
    -Post sensor reading data through a file (#3)<br/>
    
**Category 2: Query database for sensor readings**<br/>
    -Get sensor reading at a time point, for a sensor (specify by sensor type) in a device (#4)<br/>
    -Get sensor readings in a time frame, for a sensor (specify by sensor type) in a device (#5)<br/>
    -Get current sensor readings for a sensor type in all registered devices (#6)<br/>
    -Get latest sensor readings for a sensor type in all registered devices (#7)

**Category 3: Query database for metadata**<br/>
    -Get all devices registered (#1)<br/>
    -Get all sensor types of a specific device (#2)<br/>
    

Detailed Usages:
----------------
Note: all TimeStamps are in Unix epoch time format to millisecond. Conversion from readable timestamp format to Unix epoch timestamp can be found in http://www.epochconverter.com

1. **GET ALL DEVICES**
    - **Purpose**: Query all registered devices' metadata.
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/get_devices/<"result_format">
    - **Semantics**:
        - **uri**: User-defined identifier for a device. Each uri is an identifier unique to the corresponding device
        - **device_type**: Model of the device. A device is a container (i.e., physical device) object that comprises one or more sensors and is capable of transmitting their readings over a network to a Device Agent.
        - **device_agent**: A local server or proxy that manages a set of devices registered to it. Device agents can receive data from devices, convert data to another format (eg. JSON), and can transmit it to central server over a LAN or WAN. 
        - **device_location**: The location of the device that is transmitting sensor data. 
        - **result_format**: Either json or csv (2 formats are supported).
    - **Sample Usages**:
      - **Sample request in csv format**: http://einstein.sv.cmu.edu/get_devices/csv
      - **Sample result in csv format**: <br/>
          uri,device_type,device_agent,device_location <br/>
          10170202,Firefly_v3,SensorAndrew2,B23.216
      - **Sample request in json format**: http://einstein.sv.cmu.edu/get_devices/json
      - **Sample result in json format**: {"device_type":"Firefly_v3","device_location":"B23.216","device_agent":"SensorAndrew2","uri":"10170202"}


2. **GET SENSOR TYPES OF A DEVICE**
    - **Purpose**: Query all sensor types contained in a specific device model (type).
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/get_sensor_type/<"device_type">/<"result_format">
    - **Semantics**:        
        - **device_type**: Model of the device.
        - **sensor_type**: Type of a contained sensor, e.g., temperature, CO2 level etc. A device type could correspond to multiple sensor types if the device has multiple sensors.
        - **result_format**: Either json or csv.
    - **Sample Usages**:
      - **Sample csv request**: http://einstein.sv.cmu.edu/get_sensor_types/firefly_v3/csv
      - **Sample csv result**: <br/>
          sensor_types<br/>
          temp<br/>
          digital_temp
      - **Sample json request**: http://einstein.sv.cmu.edu/get_sensor_types/firefly_v3/json        
      - **Sample json result**: {"device_type":"Firefly_v3", "sensor_type":"temp,digital_temp,light,pressure,humidity,motion,audio_p2p,acc_x,acc_y,acc_z"}


3. **PUBLISH SENSOR READINGS**
    - **Purpose**: Publish sensor readings to sensor data service platform.
    - **Method**: POST
    - **URL**: http://einstein.sv.cmu.edu/sensors
    - **Semantics**: As a POST method, the API cannot be directly executed through a web browser.  Instead, it may be executed through Rails, JQuery, Python, BASH, etc.
        - **device_id** (string): Unique device uri/id.
        - **timestamp** (int): Recording timestamp of the sensor reading in Unix epoch timestamp format. 
        - **sensor_type** (string): Type of the sensor, e.g., temperature, CO2 Levels, etc. It is user's responsibility to tag the correct sensor type to the sensor reading.
        - **sensor_value** (double): The value of the sensor reading. It is user's responsibility to calibrate the sensor readings before publishing.
    - **Sensor data format**: {"id": <"device_id">, "timestamp": <"timestamp">, <"sensor_type">: <"sensor_value">} 
        <br/> Note: more than one (sensor_type:sensor_value) pairs can be included in a json file.   
    - **Sample Usages**:
      - **Command Line Example**: 
          1. Prepare input sensor reading data in a json file:
              - "sample_reading.json" file contains: {"id":"test", "timestamp": 1373566899100, "temp": 123}
          2. curl -H "Content-Type: application/json" -d @sample_reading.json "http://einstein.sv.cmu.edu/sensors"
      - **Result**: "saved" if the sensor readings have been successfully added to the database.

4. **GET SENSOR READINGS OF A TYPE OF SENSOR IN A DEVICE AT A TIME**
    - **Purpose**: Query sensor readings for a specific type of sensor, in a particular device, at a specific time point.
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/sensors/<"device_id">/<"timestamp">/<"sensor_type">/<"result_format">
    - **Semantics**: 
        - **device_id**: Unique uri/identifier of a device.
        - **timestamp**: Time of the readings to query.
        - **sensor_type**: Type of the sensor (e.g., temperature, CO2, etc.) to query.
        - **result_format**: Either json or csv.
    - **Sample Usages**: 
      - **Sample csv request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/temp/csv<br/> (note: "temp" represents the temperature sensor type)
      - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>10170102,1368568896000,temp,518.0
      - **Sample json request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/temp/json
      - **Sample json result**: {"timestamp":1368568896000,"sensor_type":"temp","value":518,"device_id":"10170102"}

5. **GET SENSOR READINGS IN A TIME RANGE FOR A DEVICE**
    - **Purpose**: Query sensor readings for a specific type of sensor, in a particular device, for a specific time range. 
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/sensors/<"device_id">/<"start_time">/<"end_time">/<"sensor_type">/<"result_format">
    - **Semantics**:
        - **device_id**: Unique uri/identifier of a device.
        - **start_time**: Start time to retrieve the sensor readings.
        - **end_time**: End time to retreive the sensor readings.
        - **sensor_type**: Type of the sensor (e.g., temperature, CO2, etc.) to retrieve its readings.
        - **result_format**: Either json or csv.
    - **Sample Usages**: 
      - **Sample csv request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/1368568996000/temp/csv
      - **Sample csv result**: (device_id,timestamp,sensor_type,value)<br/>
          10170102,1368568993000,temp,517.0 <br/>
          ... <br/>
          10170102,1368568896000,temp,518.0
      - **Sample json request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/1368568996000/temp/json
      - **Sample json result**: <br/>
          [{"timestamp":1368568993000,"sensor_type":"temp","value":517,"device_id":"10170102"},
          ... <br/>
          {"timestamp":1368568896000,"sensor_type":"temp","value":518,"device_id":"10170102"}]

6. **GET CURRENT SENSOR READINGS AT A TIME POINT FOR A TYPE OF SENSOR IN ALL REGISTERED DEVICES**
    - **Purpose**: Query all sensor readings at a time point (within 60 seconds), of a specific sensor type contained in all registered devices.
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/<"timestamp">/<"sensor_type">/<"result_format">
    - **Semantics**:
        - **timestamp**: Time to query the last readings of all sensors for all devices registered at the sensor data service platform.
        - **sensor_type**: Type of the sensor (e.g., temperature, CO2, etc.).
        - **result_format**: Either json or csv.
    - **Sample Usages**: 
      - **Sample csv request**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/1368568896000/temp/csv
      - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>
          10170203,1368568896000,temp,513.0 <br/>
          ... <br/>
          10170204,1368568889000,temp,516.0
      - **Sample json request**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/1368568896000/temp/json
      - **Sample json result**: <br/>
          [{"timestamp":1368568896000,"sensor_type":"temp","value":513,"device_id":"10170203"},
          ... <br/>
          {"timestamp":1368568889000,"sensor_type":"temp","value":516,"device_id":"10170204"}]


7. **GET LATEST SENSOR READINGS AT A TIME POINT FOR A TYPE OF SENSOR IN ALL REGISTERED DEVICES**
    - **Purpose**: Query all latest sensor readings, of a specific sensor type contained in all devices.  If no reading for a sensor in the last 60 seconds, the latest stored reading of the corresponding sensor will be returned. 
    - **Method**: GET
    - **URL**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/<"sensor_type">/<"result_format">
    - **Semantics**:
        - **sensor_type**: Type of the sensor (e.g., temperature, CO2, etc.).
        - **result_format**: Either json or csv.
        - Note: The difference between API#7 and API#6 (last_readings_from_all_devices) given the current timestamp is that, API#7 returns the last readings stored for each device even if it is more than 60 seconds old.
    - **Sample Usages**: 
      - **Sample csv request**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/temp/csv     
      - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>
          10170203,1368568896000,temp,513.0 <br/>
          ... <br/>
          10170204,1368568889000,temp,515.0
      - **Sample json request**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/temp/json
      - **Sample json result**: <br/>
        [{"timestamp":1368568896000,"sensor_type":"temp","value":513,"device_id":"10170203"},
        ... <br/>
        {"timestamp":1368568889000,"sensor_type":"temp","value":515,"device_id":"10170204"}]



[1]: http://einstein.sv.cmu.edu/ "The Application Server running in the Smart Spaces Lab, CMUSV"

