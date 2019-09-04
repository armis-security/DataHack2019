# DATAHACK 2019

![Armis](/resources/Armis-LinkedIn-Banner-IMG.png?raw=true "Armis")

# Participants
Please join our Telegram channel<br>
https://t.me/joinchat/EYtM0k4eAKoPDjJhVamHpg
<br>
And please fill in this google form so we'll know who you are:
https://docs.google.com/forms/d/e/1FAIpQLSdcWkf3ciQeC_uqPS-l6qxcT-EMGdzteqxtSQ9HUvyy0L6tmw/viewform?usp=sf_link

# Devices Gone Rogue Challenge
Ever wondered what would happen if you just plug in that seemingly innocent USB you found laying around? You’re about to find out! In this devices-gone-rogue challenge - should you choose to accept it - you will gain access to traffic data of ~100K devices, and will be tasked with finding the devices that, well, misbehave. 
This challenge is fully unsupervised - so put your anomaly belt on and get to it!

# Table of Contents  
[The Data](#Data)  
[Dataset](#Dataset)  
[Evaluation](#Evaluation)  
[Solution Example](#SolutionExample)    
[Submissions](#Submissions)  
[Contact Us](#Contact)

# The DATA
<a name="Data"></a>
Ok, so finally the data is here: <br>
[Devices](https://armis-datahack.s3.amazonaws.com/all_devices.csv) <br>
[Sessions](https://armis-datahack.s3.amazonaws.com/all_sessions.csv )

# Dataset
<a name="Dataset"></a>
The dataset contains device data and network traffic taken from several different networks.
There are two CSV files:
* **Devices.csv** - Data of devices and their type, manufacturer and model.
* **Sessions.csv** - Details of the connections between a device and its hosts,
aggregated by hours (each row holds the aggregated data of several sessions).

## Device Dataset In Depth
### Fields:
| Field | Description |
| ------------- |-------------|
| network_id | A numeric network identifier, this file contains device information from 4 independent networks (0, 1, 2, 3)|
| device_id | A numeric device identifier, unique inside the network |
| type | The device type, one of ("MOBILE_PHONE", "TABLET", "PC", "WATCH", "VOIP", "PRINTER", "IP_CAMERA") |
| model | The device model |
| manufacturer | The device manufacturer |
| operating_system_version | The device operating system version |

* Other than "network_id", "device_id" and "type", all fields are optional, and can be null.

### Device Dataset - Example:
In the snippet below there are 4 devices: 3 apple watches and 1 ipad. They are all from network 0.

![Devices](/resources/devices.png?raw=true "Devices")

## Sessions Dataset In Depth

### Fields:
| Field | Description |
| ------------- |-------------|
| network_id | A numeric network identifier; the data contains sessions from 4 independent networks (0, 1, 2, 3) |
| device_id | A numeric device identifier; it is only unique within its specific network |
| timestamp | The hour for which the sessions are aggregated |
| host | The domain the device was connected to; if domain is unknown, the host IP address will be displayed (this field is hashed) |
| host_ip | The IP address of the host the device was connected to (this field is hashed) |
| port_dst | The destination port used in the session |
| transport_protocol | The connection protocol - could be TCP or UDP |
| service_device_id | The device id of the host our device was connected to |
| packets_count | Total number of packets transferred during the aggregated sessions |
| outbound_bytes_count | Total bytes sent during the aggregated sessions |
| inbound_bytes_count | Total bytes received during the aggregated sessions |
| packet_loss | Total number of packets that were lost during the session |
| retransmit_count | Total number of packets that were retransmitted during the aggregated sessions |
| latency | Network latency during the aggregated sessions |
| session_count | Number of sessions this aggregated row holds |
| outbound_packets_count | Total number of packets sent during the aggregated sessions |
| inbound_packets_count | Total number of packets received during the aggregated sessions |
| outbound_bytes_max | Max number of bytes sent in one session of the aggregated sessions |
| outbound_bytes_min | Min number of bytes sent in one session of the aggregated sessions |
| outbound_bytes_mean | Mean number of bytes sent during the aggregated sessions |
| outbound_bytes_median | Median of bytes sent during the aggregated sessions |
| outbound_bytes_stddev | Standard deviation of bytes sent during the aggregated sessions |
| inbound_bytes_max | Max number of bytes received in one session of the aggregated sessions |
| inbound_bytes_min | Min number of bytes received in one session of the aggregated sessions |
| inbound_bytes_mean | Mean number of bytes received during the aggregated sessions |
| inbound_bytes_median | Median number of bytes received during the aggregated sessions |
| inbound_bytes_stddev | Standard deviation of bytes received during the aggregated sessions |
| outbound_packet_size_max | Max packet size sent in one session of the aggregated sessions |
| outbound_packet_size_min | Min packet size sent in one session of the aggregated sessions |
| outbound_packet_size_mean | Mean packet size sent during the aggregated sessions |
| outbound_packet_size_median | Median packet size sent during the aggregated sessions |
| outbound_packet_size_stddev | Standard deviation of packet size sent during the aggregated sessions |
| inbound_packet_size_max | Max packet size received in one session of the aggregated sessions |
| inbound_packet_size_min | Min packet size received in one session of the aggregated sessions |
| inbound_packet_size_mean | Mean packet size received during the aggregated sessions |
| inbound_packet_size_median | Median packet size received during the aggregated sessions |
| inbound_packet_size_stddev | Standard deviation of packet size received during the aggregated sessions |


* Other than "network_id" and "device_id", all fields are optional and can be null.
* Sessions between two devices in the same network will only be displayed once, with the device_id field indicating the id of device that initiated the session, and service_device_id indicating the id of the target device.

### Example:
In the snippet below you can find some aggregations for network 0.<br>
Let's look at the first line - what does it tell us? <br>
We can see that device no. 35 had initiated 39 sessions with host "ecbb92...", using protocol TCP and port 49152, during the hour that started at 156507480 (epoch time for 06.08.2019 07:00 UTC). During those sessions, a total of 260 packets were transferred. <br> 
What can you learn from the other lines?<br>
![Sessions](/resources/sessions.png?raw=true "Sessions")


## Example - Network Diagram:

Let's review some connections from network number 1. The following tables display the data of 5 devices and their relevant sessions:

![Devices](/resources/viz_devices.png?raw=true "Devices")

![Sessions](/resources/viz_sessions.png?raw=true "Sessions")

From these snippets we can learn there is one PC, connected to 3 IP cameras, and that 
one of the IP cameras is connected to another IP camera. Here is a quick network diagram showing the connections:

![Network](/resources/DataHackViz.png?raw=true "Data Hack")

# Evaluation
<a name="Evaluation"></a>
**Please note: the scoring guidelines have been updated.**
As this is an unsupervised challenge, the evaluation process will be a mix of classic "leaderboard" evaluation and in-person review of the models used. <br>
The final score will be composed of: 
* **Leaderboard Evaluation** (60%) <br>
Your model results will be matched against a prelabeled dataset, and AUC (Area Under the Curve) will be calculated on the test set.
* **Explainability** (20%) <br>
In what measures is this an anomaly? How important is it?
* **Innovation** (20%) <br>
Use of a non-trivial algorithm. Creation of ingenious useful features. Any other creative ideas could also be credited with extra scores. Surprise us.

# Solution Example
<a name="SolutionExample"></a>

There are many approaches to an anomaly detection in network security. <br>

In the [solution example notebook](solution-example/full_flow_solution.ipynb) you'll find a very naive approach using Elliptic Envelope model. 

The model run on each network separately using only 5 features to detect anomalies. 

Ignoring the unsatisfying but predictable score, this solution contains the full flow - reading the data, creating feature set, running the model and than sending the results to our leader board. 


# Submissions
<a name="Submissions"></a>
Our Leader Board can be accessed here https://leaderboard.datahack.org.il/armis <br>
In order to automatically grade your results - and, of course, appear in the challenge leaderboard - you need to send your results using a HTTP POST request to our api endpoint. <br>
The results - a list of all device IDs, with the anomaly score for each device - needs to be sent as a JSON list, with the following structure:
```
[
  [
    "network_id - The network id int",
    "device_id - The network id int",
    "confidence - The anomaly score for this device - float between 0 and 1"
   ]
]
```
**Please pay close attention to the order of the inner array - network id, device id, confidence.** <br> <br>
For example:
```
[
  [ 1, 222, 0.75 ],
  [ 0, 24, 0.11 ]
]
```
In this example there are 2 devices: the first one is device_id 222 from network 1, and its anomaly score is 0.75 (the higher the score, the more likely it is that this device is anomalous).<br>
The second device is device_id 24 from network 0, and it received the anomaly score of 0.11. <br><br>

A detailed code for submission is available in the solution example. <br><br>

After each submission, please send us your code, in a zipped file, to "datahack@armis.com", so we review your solution.

More details about DataHack leader board can be found [here](https://dagshub.com/Datahack/DataHack-Resources-2019/src/master/Leaderboard.md) 


# Contact Us
<a name="Contact"></a>
For every question, suggestion, or any other observations you might have, please do not hesitate to contact us: datahack@armis.com

# ENJOY!
