# RemoteLabz's Worker installation guide

This section guides you through the installation of RemoteLabz's worker and its components on an Ubuntu system. We assume you have already installed an Ubuntu Server 20.04 LTS.

!!! note "Foreword"

    - This section has only been tested with **Ubuntu Server 20.04 LTS**

# Retrieve the RemoteLabz source
A remotelabz directory will be create on your home directory.
```bash
cd ~
git clone https://github.com/crestic-urca/remotelabz-worker.git
cd remotelabz-worker
```

# Installation
You should modify the `.env` file according to your environment

``` bash
sudo cp .env.dist .env
sudo nano .env
# you may change the network interface name
# DATA_INTERFACE will be used by the virtual machine
# ADM_INTERFACE will be used by the RemoteLabz's front and the RabbitMQ to communicate with the worker. This interface is also used to ssh connexion
DATA_INTERFACE="enp0s8"
ADM_INTERFACE="enp0s3"
# you may change the MESSENGER_TRANSPORT_DSN variable with the following, with your credentials, and the RabbitMQ IP or its FQDN
MESSENGER_TRANSPORT_DSN=amqp://remotelabz-amqp:password-amqp@X.X.X.X:5672/%2f/messages
sudo ./install
```
# Configuration
### Activate the forward between the interface
In the file `/etc/sysctl.conf`, looking for the line `#net.ipv4.ip_forward=1` and uncomment it. Then, reload this `/etc/sysctl.conf` file
```bash
sudo sysctl --system
```

### Configure the route from the worker to the front for the VM's network
We assume you have configure now all variables in your .env which was modified after a copy of the .env.dist
```bash
source /opt/remotelabz-worker/.env
sudo ip route add $VPN_NETWORK/$VPN_NETWORK_NETMASK via $FRONT_IP_ADDRESS
```

### Instances

```bash
sudo service remotelabz-worker start
```

You can check the log of the worker in `~/remotelabz-worker/var/log/prod.log`

## Options

- `-p` Port used by remotelabz-worker
    - Default : `8080`

!!! warning
    When consuming messages, a timestamp is used to determine which messages the messenger worker is able to consume. Therefore, each machines needs to be time-synchronized. We recommand you to use a service like `ntp` to keep your machines synchronized.

