# Cisco-MDT-TIG-Docker
Docker Container deployment of TIG stack with Cisco MDT inputs prepped for Telegraf


# Enable streaming telemetry on IOS-XE - CHANGE YOUR IP ADDRESSES!
```
telemetry ietf subscription 1
 encoding encode-kvgpb
 filter xpath /process-cpu-ios-xe-oper:cpu-usage/cpu-utilization/five-seconds
 source-address 10.10.21.15
 stream yang-push
 update-policy periodic 500
 receiver ip address 10.10.21.196 57000 protocol grpc-tcp
telemetry ietf subscription 2
 encoding encode-kvgpb
 filter xpath /interfaces-ios-xe-oper:interfaces/interface[name='GigabitEthernet1']/statistics
 source-address 10.10.21.15
 stream yang-push
 update-policy periodic 500
 receiver ip address 10.10.21.196 57000 protocol grpc-tcp

netconf-yang
end
```
