## Dynamo API ##
1. get_devices - return a json array of all devices.
    eg. http://cmu-sds.herokuapp.com/get_devices
2. sensor_readings - retrieve sensor readings of a device within a specific time range.
    specify device id with following paramters:
    - start_time, end_time - T he time range to retrieve readings. These are Unix epoch UTC timestamp in millisecond.
    - tuples - optional parameter. Instead of raw readings return the aggregated reading per specified amount of records. If there are less readings than the specified tuples, then no results will be returned. Attributes unique to tuples: average_timestamp, first, last, min, max, average.
    - eg.
    - http://cmu-sds.herokuapp.com/sensor_readings/23100015?start_time=1359581004000&end_time=1359582134000
    - http://cmu-sds.herokuapp.com/sensor_readings/23100015?start_time=1359581004000&end_time=1359582134000?tuples=4

3. get_latest_reading - retrieves the latest reading of a device
   specify the device_id in id parameter, eg.
   - http://cmu-sds.herokuapp.com/get_latest_reading?id=10170005

4. get_last_reading_time_for_all_devices - get time of the last reading from all devices
   - eg. http://cmu-sds.herokuapp.com/get_last_reading_time_for_all_devices
