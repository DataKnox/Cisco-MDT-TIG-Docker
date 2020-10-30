# Cisco-MDT-TIG-Docker
Docker Container deployment of TIG stack with Cisco MDT inputs prepped for Telegraf

# Clone the repo

```
git clone https://github.com/DataKnox/Cisco-MDT-TIG-Docker
```

# Edit the Telegraf.conf file to have the IP Address of the computer that will have the InfluxDB
# Edit the URLs line
```
# Outputs for ciscomdt
[[outputs.influxdb]]
  database = "cisco_mdt"
  urls = [ "http://10.10.21.104:8086" ]
  username = "telegraf"
  password = "password123"
```

# Change into the directory that contains the Docker-Compose.yml file
# EXAMPLE on my computer
```
cd /home/knox/Documents/ciscomdt/Cisco-MDT-TIG-Docker/Ubuntu
```
# Bring the containers to life
```
docker-compose up
```

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
# Login to Grafana and connect to InfluxDB

http://localhost:3000

default user and pw is 'admin'

# Add a database
Find influxDB from the list
Name: whatever you want
Query Language: InfluxQL
URL: http://localhost:8086/
Access: Browser
Influx DB Details:
Database: cisco_mdt
user: telegraf
password: password123

Save and Test

# Create a dashboard!
