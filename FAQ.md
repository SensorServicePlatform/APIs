**How will I retrieve sensor readings from a specific sensor?**

First, you will need to find the device that contains the specific sensor. To do it, you can use the API "Get all Devices". 
All devices registered at the SDSP will be retrived, each with its device id, device type, location, etc. Second, from the 
device type of a device, you will use the API "Get Sensor Types" to find out all sensor types included in the device type.
Third, you will use "device id + sensor type" to retrieve sensor readings for a specific sensor.

