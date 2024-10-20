# Python Library for NETGEAR Plus Switches

## What it does

Grabs statistical network data from [supported NETGEAR Switches](#supported-and-tested-netgear-modelsproducts-and-firmwares) from the
[Plus Managed Network Switch](https://www.netgear.com/business/wired/switches/plus/) line. These switches can only be managed using a
Web interface and not through SNMP or cli. This library uses web scraping to collect the switch configuration, statistics and
some basic configuration updates.

## How it works

1. Detecting Switch Model/Product in login.cgi
2. Connects to the Switch and asks for a cookie (`http://IP_OF_SWITCH/login.cgi`)
3. HTTP-Request send to the Switch twice (`http://IP_OF_SWITCH/portStatistics.cgi`) and compared with previous data ("in response time")

### List of port statistics

| Name                          | mapped key from `get_switch_infos()` | Unit                                |
| ----------------------------- | ------------------------------------ | ----------------------------------- |
| Port {port} Traffic Received  | `port_{port}_traffic_rx_mbytes`      | MB (in response time)               |
| Port {port} Traffic Sent      | `port_{port}_traffic_tx_mbytes`      | MB (in response time)               |
| Port {port} Receiving         | `port_{port}_speed_rx_mbytes`        | MB/s                                |
| Port {port} Transferring      | `port_{port}_speed_tx_mbytes`        | MB/s                                |
| Port {port} IO                | `port_{port}_speed_io_mbytes`        | MB/s                                |
| Port {port} Total Received    | `port_{port}_sum_rx_mbytes`          | MB (since last switch reboot/reset) |
| Port {port} Total Transferred | `port_{port}_sum_tx_mbytes`          | MB (since last switch reboot/reset) |
| Port {port} Connection Speed  | `port_{port}_connection_speed`       | MB/s                                |
| Port {port} Status            | `port_{port}_status`                 | "on"/"off"                          |
| Port {poe_port} POE Power     | `port_{poe_port}_poe_power_active`   | "on"/"off"                          |

### List of aggregated statistics

| Sensor Name                | mapped key from `get_switch_infos()` | Unit                  |
| -------------------------- | ------------------------------------ | --------------------- |
| Switch IO                  | `sum_port_speed_bps_io`              | MB/s                  |
| Switch Traffic Received    | `sum_port_traffic_rx`                | MB (in response time) |
| Switch Traffic Transferred | `sum_port_traffic_tx`                | MB (in response time) |

## Supported and tested NETGEAR Models/Products and firmware versions

| Model    | Ports | Firmware versions                      | Bootloader versions |
| -------- | ----- | -------------------------------------- | ------------------- |
| GS105E   | 5     | ?                                      |                     |
| GS108E   | 8     | V1.00.11                               | V1.00.03            |
| GS105Ev3 | 5     | ?                                      |                     |
| GS108Ev3 | 8     | V2.00.05, V2.06.10, V2.06.17, V2.06.24 | V2.06.01 - V2.06.03 |
| GS305EP  | 5     | V1.0.1.1                               |                     |
| GS308EP  | 8     | V1.0.0.10, V1.0.1.4                    |                     |
| GS308EPP | 8     | ?                                      |                     |

Supported firmware languages: GR (German), EN (English)

# Unsupported models

| Model     | Support status                                         |
| --------- | ------------------------------------------------------ |
| GS108PEv3 | Not yet started                                        |
| GS316EP   | Login is supported, statistics and PoE control not yet |
| GS316EPP  | Login is supported, statistics and PoE control not yet |

## Library Usage

### create a python venv with `requests` and `lxml`

```shell
python3 -m venv .venv
source .venv/bin/activate
pip install lxml requests
```

Using this VENV go to your local source folder

### Example calls

```shell
cd src
python3
```

```python
ip = '192.168.178.68' # replace with IP address of your switch
p = 'fyce4gKZemkqjDY' # replace with your password
import netgear_plus
sw = netgear_plus.NetgearSwitchConnector(ip, p)
sw.autodetect_model()
sw.get_login_cookie()

data = sw.get_switch_infos()
print(sw.switch_model.MODEL_NAME)
print(data["port_1_sum_rx_mbytes"])
print(data)
```
