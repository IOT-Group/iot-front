title

1. login(string:username,string password) @return int:userid

2. register(string:username,string password)

3. getEnvironment(int:userid)

4. getDevices(string:username)

5. sendVoiceMessage(string:msg) @return string:code ? device[]

6. operate(int:time,int:id,string:code)

7. getSchedule(int:userid)

8. addSchedule(schedule) or setSchedule?

9. sendEnvironment(int:userid,int:temperature,int humidity,int time,int ownerstate,int timeInterval) @return device[]+string[]msgs ? string[]:code,int[]:deviceid,int:temperature,int:humidity

10. getDeviceTypes() ? @return devicetype[]

11. addDevice(string:username,int:devicetypeid) @return int:deviceid

12. deleteDevice(int:deviceid)
