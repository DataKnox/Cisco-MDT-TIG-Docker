# Introduction
This repo contains a Docker Compose file that can spin up 3 containers - Telegraf, InfluxDB, and Grafana. This is geared towards Cisco because it comes with the Cisco Model-Driven-Telemetry inputs for Telegraf.  

This repository has been updated to support the latest versions of the TIG stack Docker containers, as of March 10th, 2023. This includes support for new authentication structures (Bearer Token Authentication) required by InfluxDB versions 2.x.

# Steps to Get It Up and Running
1. Clone the Repo
```
git clone https://github.com/DataKnox/Cisco-MDT-TIG-Docker
```

2. Change into the directory that contains the Docker-Compose.yml file  
EXAMPLE on my computer - *Make sure you choose the right platform (WSL2/Ubuntu)*
```
cd /home/knox/Documents/ciscomdt/Cisco-MDT-TIG-Docker/Ubuntu
```

3. **IMPORTANT**: Edit the Telegraf.conf file to have the IP Address of the computer that will have the InfluxDB using a text editor (e.g., nano or vim)
```
nano conf/telegraf/telegraf.conf
```
- Edit the URLs line
```
# Cisco MDT Telemetry
[[inputs.cisco_telemetry_mdt]]
 transport = "grpc"
 service_address = ":57000"

# Outputs for ciscomdt
[[outputs.influxdb_v2]]
  urls = [ "http://<PUT NEW IP ADDRESS HERE>:8086" ]
  token = "$INFLUXDB_TOKEN"
  organization = "CBTNUGGETS-DEMO"
  bucket = "CBTNUGGETS-DEMO-BUCKET"
```  

- **DO NOT USE LOCALHOST/127.0.0.1 - YOU MUST USE THE REACHABLE IP ADDRESS**  


4. Bring the containers to life
```
docker-compose up
```

5. Enable streaming telemetry on IOS-XE - CHANGE YOUR IP ADDRESSES WHERE INDICATED!
```
netconf-yang

telemetry ietf subscription 1
 encoding encode-kvgpb
 filter xpath /process-cpu-ios-xe-oper:cpu-usage/cpu-utilization/five-seconds
 source-address <IP ADDRESS OF LOCAL INTERFACE>
 stream yang-push
 update-policy periodic 500
 receiver ip address <IP ADDRESS OF COMPUTER HOSTING CONTAINERS> 57000 protocol grpc-tcp
telemetry ietf subscription 2
 encoding encode-kvgpb
 filter xpath /interfaces-ios-xe-oper:interfaces/interface[name='GigabitEthernet1']/statistics
 source-address <IP ADDRESS OF LOCAL INTERFACE>
 stream yang-push
 update-policy periodic 500
 receiver ip address <IP ADDRESS OF COMPUTER HOSTING CONTAINERS> 57000 protocol grpc-tcp
```
6. Login to the Grafana admin panel via http://localhost:3000 with the default user/password as admin/admin.
- You will be prompted to change this - you can keep the password of admin if you'd like. 

7. Add a data source
- Hover over the settings cog in the sidebar and click Data sources
- Click "Add new data source"
- Find influxDB from the list
- Basic Configuration Parameters:
    - Name: whatever you want
    - Query Language: InfluxQL
    - URL: http://\<IP ADDRESS OF COMPUTER HOSTING CONTAINERS\>:8086/
- Custom HTTP Headers:
    - Header: Authorization
    - Value (make sure you enter this value **EXACTLY, including Token**): Token veryverysecuretoken
- InfluxDB Details:
    - Database: CBTNUGGETS-DEMO-BUCKET

Save and Test

8. Create a dashboard!

# Additional Sites and Links
- TIG Stack Documentation: https://www.influxdata.com/
- Cisco MDT Docs: https://github.com/influxdata/telegraf/tree/master/plugins/inputs/cisco_telemetry_mdt

# Contributors
- Knox Hutchinson - DataKnox, CBT Nuggets Trainer
- Kelvin Tran - kelvintechie1, CBT Nuggets Trainer
