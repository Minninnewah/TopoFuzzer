# <img src="./templates/images/LOGO2.png" alt="drawing" width="150"/>  TopoFuzzer - A Network Topology Fuzzer 

-----------------------------------------

## :page_with_curl: What is TopoFuzzer?

TopoFuzzer is a gateway node with two main functionalities:
1. It assists your service (containers or VMs) migration and reinstantiation at the networking level, mapping the public IP used by users to connect and the private IP allocated to the new instance. The advantage of TopoFuzzer is the live handover of connections without having to close them and re-establish them. This is critical for a seamless migration of services with long lived connections.
2. It establishes a mininet network allowing for dynamic changes of the network topology to disrupt reconnaissance and scanning of external and internal attackers with Moving Target Defense (MTD) strategies.

You can read a detailed presentation of TopoFuzzer

## :clipboard: Features

- REST API to update the mapping between the public IP and the private IP of a service
- Instant handover of TCP connections: _e.g.,_ HTTP/2
- Instant handover of UDP connections: _e.g.,_ QUIC, HTTP/3
- No TLS certificates needed for HTTPS/3 (no trust on the intermediary needed)
- Add and remove nodes, switches, and links dynamically with the mininet API
- Change the traffic paths in the data plane by connecting an external SDN controller 


## :hammer_and_pick: Quick Start

**PREREQUIREMENTS:**
- Operating System: Ubuntu 18.04
- Python3.6.9 (```sudo apt install python3.6```)
- Python3-pip (```sudo apt install python3-pip```)
- Mininet 2.3.0 (follow option 2  of the mininet guide http://mininet.org/download/)
- redis (```sudo apt install redis```). Set redis to use the external IP of your machine or VM.


**INSTALL:**

1. ```git clone https://github.com/wsoussi/TopoFuzzer.git```
2. ```git checkout v0.1-fixes```
3. ```python3.6 -m virtualenv venv```
4. ```source venv/bin/activate```
5. ```pip install -r requirements.txt```

**DEPLOY:**
1. change the file `settings.py` to put the host IP and the redis port in the correspondent field `TOPOFUZZER_IP` and `REDIS_PORT` (default port is 6379)
2. create an admin user with the command ```python manage.py createsuperuser```
3. start the server with the command ````python manage.py runserver 0:8000````, which starts the TopoFuzzer REST API interface 

**Deploy the mininet _"fuzzing network"_ with isolated redirection proxies per service**

4. start the TopoFuzzer mininet middle network and the redirection proxies per service with the command ````python manage.py proxy_handler_main --sdnc-ip <SDNC>```` where _\<SDNC\>_ is the IP or the hostname of the external SDN controller


**Deploy a single redirection proxy for all services (available in v0.2)**

This option is convenient when the isolation of traffic between services is not relevant and allows to reduce the CPU consumption by at least 20 fold (for 4 services and over). The main reason is that only one proxy and listener is deployed for all the services.
4. Add TPROXY mangling rule with the command ````sudo iptables -t mangle -I PREROUTING ! -s <host_IP> -d <services_public_IP_range> -p tcp -j TPROXY --on-port=5555 --on-ip=127.0.0.1````
5. add _net_admin_ rights to the redirection proxy with ````sudo setcap cap_net_raw,cap_net_admin=eip proxy_handler/management/commands/singleHostProxy.py````
6. Start the single redirection proxy using the command ````sudo python manage.py singleHostProxy````


## :book: Usage and documentation:
Now you can transfer open connections to different instances of your service dynamically and control the mininet middle-network using your SDNC.
More details on the deployment, the usage of TopoFuzzer, and the description of the REST API interface is detailed in the Wiki/Documentation here: [Wiki](https://github.com/wsoussi/TopoFuzzer/wiki)