# IOS_EEM

EEM is a powerful and flexible tool to automate tasks and customize the behavior of Cisco IOS Software and the operation of a device.

The Cisco IOS EEM allows the creation of very powerful and complex policies. These are some examples:

1. The Cisco IOS EEM applet for disabling specific commands disables the desired command to be executed from the command line. For example, if you do not want the device to be manually reloaded, you can disable the reload command.

2. The Cisco IOS EEM applet for turning on an interface and assigning it the IP address can automatically turn on the interface when you connect the device to it and assign the IP address from the prespecified subnet.

3. The Cisco IOS EEM applet for saving the configuration and automatically reloading the device can automatically save the configuration in case it has been changed. It also can notify you about the changes.

4. The Cisco IOS EEM applet for reporting the device configuration changes can inform you in case changes have been made to the configuration. For example, you may have a device whose configuration should not be changed. If changes happen, you must take quick action in response. You have to be immediately informed about the changes.

5. The Cisco IOS EEM applet for reporting the routing table changes can inform you when the number of routes in the routing table changes. For example, if one route is no longer present in the routing table, this condition could indicate that packets are traversing a backup link, which may be more costly. If this failure happens, you should be informed so you can quickly recover the failed link.

6. The Cisco IOS EEM applet for periodically checking the reachability of certain devices can check the reachability of certain devices in specific time intervals. Normally, you want all devices to be reachable all the time. When there is a failure on the link, the EEM applet can immediately notify you about it so you can take further action.

## EEM Example

### Example 1 Use the Syslog Event Detector with EEM

The script you are creating is called “interface_Shutdown” and it is monitoring syslog messages for the string pattern “Interface Ethernet0/0, changed state to administratively down.” When this pattern is matched, it will trigger the action list. The action list first enters interface configuration mode and issues the no shutdown command, then sends an email to a monitoring email address explaining the action that was observed. The actions will be taken in the order that they are listed in the script.

```
R1# configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)# event manager applet interface_Shutdown
R1(config-applet)# event syslog pattern "Interface Ethernet0/0, changed state to administratively down"      
R1(config-applet)# action 1.0 cli command "enable"
R1(config-applet)# action 1.5 cli command "config t"
R1(config-applet)# action 2.0 cli command "interface e0/0"
R1(config-applet)# action 2.5 cli command "no shutdown"
R1(config-applet)# action 3.0 cli command "end"
R1(config-applet)# action 3.5 cli command "who"
R1(config-applet)# action 4.0 mail server "192.168.1.1" to ".engineer@cisco.com." from ".EEM@cisco.com." subject ".ISP1_Interface_Ethernet0/0_SHUT." body "Current users $_cli_result"       
R1(config-applet)# end
```

### Example 2 Use the CLI Event Detector with EEM

The script you are creating is called “Stop_Debug” and it is monitoring the CLI for the debug command. When this pattern is matched, it will skip the command so that it does not take effect. The action list first enters the enabled mode and issues the show users | append flash:Debug command. This command will append the output from the show users command to the end of a file in “Flash” (the lab uses the unix directory as a flash equivalent) called Debug. The next action will then append the current time stamp to the end of the file in “Flash” (the lab uses the unix directory as a flash equivalent) named Debug_clock. By matching the order of the entries in both files, you will have a list of the users that tried to enter the debug command and the date and time that the user attempted it.
```
R1# configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)# event manager applet Stop_Debug
R1(config-applet)# event cli pattern "debug" sync no skip yes
R1(config-applet)# action 1.0 cli command "enable"
R1(config-applet)# action 2.0 cli command "sh users | append unix:Debug"
R1(config-applet)# action 3.0 cli command "sh clock | append unix:Debug_clock"     
R1(config-applet)# end
```
```
dir unix: | include Debug
```
```
more unix:Debug
more unix:Debug_clock
```



```
show event manager policy registered 
```
