# Zeek documentation

- Zeek is a traffic monitoring tool which generates logs about the network activities, and, thus, these informations can be used to detect anomalies in the traffic;
- There is the possibility of promoting a custom analysis over the traffic, generating specific logs with information that interests the user;
- This document will adress the process of installing Zeek and its basic cases of use. To obtain further information about the plataform it is valid to consult the [official documentation](docs.zeek.org).

## Installation process

- There are some different ways to [install](https://zeek.org/get-zeek/) Zeek and, in use case that will be further discussed, Zeek was installed from the current feature source;
- In first place, it is necessary to install the dependencies:

```
sudo apt-get install cmake make gcc g++ flex libfl-dev bison libpcap-dev libssl-dev python3 python3-dev swig zlib1g-dev
```

- After downloading the source code, the following commands should be executed in order to install Zeek:

```
tar xzvf zeek-<version_number>.tar.gz
cd zeek-<version_number>
./configure
make
make install
```

The default installation path is `/usr/local/zeek`.

## First steps

- Zeek has two operating ways, real time traffic monitoring and analysing traffic packets (offline).

### Setting the interface to analyzed in real time

- Edit the `usr/local/zeek/etc/node.cfg` in the following way:

```
[zeek]
type=standalone
host=localhost
interface=eth0   # change this according to your listening interface in ifconfig
```

- In this way, whenever Zeek is initalized through ZeekControl, it will analyse the interface which was set in `node.cfg` file.

### Analyzing interfaces

One way to analyse interfaces in Zeek is through ZeekControl:

- *"ZeekControl is an interactive shell for easily operating/managing Zeek installations on a single system or even across multiple systems in a traffic-monitoring cluster".*

To use ZeekControl, execute, with root privileges:

```
zeekctl # or /usr/local/zeek/bin/zeekctl
[ZeekControl] > deploy # promotes installation and starts Zeek
[ZeekControl] > stop
```

- The installation of zeekctl can be useful in a first time use, and can be also run after any changes to Zeek policy scripts or the ZeekControl configuration;
- The logs generated are kept in `/usr/local/zeek/bin`. While zeekctl is running, the logs are stored in `/current` file, then, they go to the upper directory;

Another way to monitor live traffic is to execute a direct command, and the logs generated will be stored in the present directory, for instance:

```
zeek -i en0 <list of scripts to load>
```

If no aditional scripts are loaded, Zeek will use only the standard scripts.

### Reading Packet Capture (pcap) Files

