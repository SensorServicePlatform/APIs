Executive Summary
=================
On top of CMU SensorAndrew, the largest nation-wide campus sensor network, our Sensor Data and Service Platform (SDSP) 
aims to build a software service layer serving sensor data service discovery, reuse, composition, mashup, provisioning, and 
analysis. Another major objective of our project is to support SDSP-empowered innovative application design and 
development. Supported by a cloud computing enrironment with high-performance database, SDSP aims to provide a platform 
to enable and facilitate a variety of research projects in the areas of mobile services, internet of things, 
cloud computing, big data analytics, software as a service, and social services.

APIs
====
API interface for CMUSV Sensor Data and Service Platform (SDSP)

CMU Sensor Service Platform
===========================

Service URL:
------------

[http://einstein.sv.cmu.edu][1]

Usage:
------

Note: all TimeStamps are in Unix epoch time format to millisecond. Conversion from readable timestamp format to Unix epoch timestamp can be found in http://www.epochconverter.com

1. **GET ALL DEVICES**
    - **Method**: GET
    - **Input Parameters**:
        - **uri**: user-defined identifier for a device. Each uri is an identifier unique to the corresponding device
        - **device_type**: Model of the device. A device is a container (i.e., physical device) object that comprises one or more sensors and is capable of transmitting their readings over a network to a Device Agent.
        - **device_agent**: a local server or proxy that manages a set of devices registered to it. Device agents can receive data from devices, convert data to another format (eg. JSON), and can transmit it to central server over a LAN or WAN. 
        - **device_location**: the location of the device that is transmitting sensor data 
        - **ResultFormat**: either json or csv (2 formats are supported)
    - **URL**: http://einstein.sv.cmu.edu/get_devices/<"ResultFormat">
    - **Sample Usages**:
      - **Sample request in csv format**: http://einstein.sv.cmu.edu/get_devices/csv
      - **Sample result in csv format**: <br/>
          uri,device_type,device_agent,device_location <br/>
          10170202,Firefly_v3,SensorAndrew2,B23.216
      - **Sample request in json format**: http://einstein.sv.cmu.edu/get_devices/json
      - **Sample result in json format**: {"device_type":"Firefly_v3","device_location":"B23.216","device_agent":"SensorAndrew2","uri":"10170202"}


2. **GET SENSOR TYPE**
    - **Method**: GET
    - **Semantics**:        
        - **DeviceType**: Model of the device.
        - **SensorType**: Type of the sensor eg. temperature, CO2 level etc. A device type could correspond to multiple sensor types if the device has multiple sensors.
        - **ResultFormat**: either json or csv
    - **URL**: http://einstein.sv.cmu.edu/get_sensor_type/<"DeviceType">/<"ResultFormat">
    - **Sample csv request**: http://einstein.sv.cmu.edu/get_sensor_types/firefly_v3/csv
    - **Sample csv result**: <br/>
        sensor_types<br/>
        temp<br/>
        digital_temp
    - **Sample json request**: http://einstein.sv.cmu.edu/get_sensor_types/firefly_v3/json        
    - **Sample json result**: {"device_type":"Firefly_v3", "sensor_type":"temp,digital_temp,light,pressure,humidity,motion,audio_p2p,acc_x,acc_y,acc_z"}


3. **Add sensor readings**
    - **Method**: POST
    - **Semantics**: this is a POST method, so the command cannot be directly execute through the browser.  It may be executed through Rails, JQuery, Python, BASH, etc.
        - **device id** (string): device uri/id
        - **timestamp** (int): time of the reading in Unix epoch timestamp. 
        - **sensor type** (string): Type of the sensor, such as temperature, CO2Levels, etc. It is up to the user to choose the sensor type to post to.
        - **sensor value** (double): this input corresponds to the value the user would like to post. It is up to the user to post the correct, pertinent value that correctly corresponds to the sensor type.
    - **URL**: http://einstein.sv.cmu.edu/sensors
    - **Data**: {"id": <"device id">, "timestamp": <"timestamp">, <"sensor type">: <"sensor value">} 
        <br/> Note: more than one sensor type:sensor value pairs can be included in the json.   
    - **Command Line Example**: 
        1. input sensor reading data in a JSON file
            - sample_reading.json contains {"id":"test", "timestamp": 1373566899100, "temp": 123}
        2. curl -H "Content-Type: application/json" -d @sample_reading.json "http://einstein.sv.cmu.edu/sensors"
    - **Result**: "saved" if the reading has been successfully added to the database.

4. **Get sensor readings at a specific time**
    - **Method**: GET
    - **Semantics**: 
        - **DeviceID**: The device uri/unique identifier
        - **TimeStamp**: Time of the reading to query
        - **SensorType**: Type of the sensor (temperature, CO2, etc.)
        - **ResultFormat**: either json or csv
    - **URL**: http://einstein.sv.cmu.edu/sensors/<"DeviceID">/<"TimeStamp">/<"SensorType">/<"ResultFormat">
    - **Sample csv request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/temp/csv<br/>("temp" is the temperature sensor type)
    - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>10170102,1368568896000,temp,518.0
    - **Sample json request**: http://einstein.sv.cmu.edu/sensors/10170102/1368568896000/temp/json
    - **Sample json result**: {"timestamp":1368568896000,"sensor_type":"temp","value":518,"device_id":"10170102"}

5. **Get sensor readings in a time range**
    - **Method**: GET
    - **Semantics**:
        - **DeviceID**: The device uri/unique identifier
        - **StartTime**: Start time to retrieve the readings
        - **EndTime**: End time to retreive the readings
        - **SensorType**: Type of the sensor (temperature, CO2, etc.)
        - **ResultFormat**: either json or csv
    - **URL**: http://einstein.sv.cmu.edu/sensors/<"DeviceID">/<"StartTime">/<"EndTime">/<"SensorType">/<"ResultFormat">
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

6. **Get the latest readings at specific time from all devices**
    - **Method**: GET
    - **Semantics**:
        - **TimeStamp**: Time to get the last readings. The query returns the latest readings up to 60 seconds before this time.
        - **SensorType**: Type of the sensor (temperature, CO2, etc.)
        - **ResultFormat**: either json or csv
    - **URL**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/<"TimeStamp">/<"sensorType">/<"ResultFormat">
    - **Sample csv request**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/1368568896000/temp/csv
    - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>
        10170203,1368568896000,temp,513.0 <br/>
        ... <br/>
        10170204,1368568889000,temp,513.0
    - **Sample json request**: http://einstein.sv.cmu.edu/last_readings_from_all_devices/1368568896000/temp/json
    - **Sample json result**: <br/>
        [{"timestamp":1368568896000,"sensor_type":"temp","value":513,"device_id":"10170203"},
        ... <br/>
        {"timestamp":1368568889000,"sensor_type":"temp","value":513,"device_id":"10170204"}]


7. **Get the latest readings at current time from all devices**
    - **Method**: GET
    - **Semantics**:
        - **SensorType**: Type of the sensor (temperature, CO2, etc.)
        - **ResultFormat**: either json or csv
        - Note: the difference between this API and last_readings_from_all_devices given the current timestamp is that this API returns the last reading from each device even if it's more than 60 seconds old.
    - **URL**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/<"sensorType">/<"ResultFormat">
    - **Sample csv request**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/temp/csv     
    - **Sample csv result**: (device_id,timestamp,sensor_type,value) </br>
        10170203,1368568896000,temp,513.0 <br/>
        ... <br/>
        10170204,1368568889000,temp,513.0
    - **Sample json request**: http://einstein.sv.cmu.edu/lastest_readings_from_all_devices/temp/json
    - **Sample json result**: <br/>
        [{"timestamp":1368568896000,"sensor_type":"temp","value":513,"device_id":"10170203"},
        ... <br/>
        {"timestamp":1368568889000,"sensor_type":"temp","value":513,"device_id":"10170204"}]



[1]: http://einstein.sv.cmu.edu/ "Application Server runs in Smart Spaces Lab"

