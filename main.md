# Zeek documentation 📃

- Zeek is a traffic monitoring tool which generates logs about the network activities, and, thus, these informations can be used to detect anomalies in the traffic;
- There is the possibility of promoting a custom analysis over the traffic, generating specific logs with information that interests the user;
- This document will adress the process of installing Zeek and its basic cases of use. To obtain further information about the plataform it is valid to consult the [official documentation](docs.zeek.org).

## Zeek structure 

## Installation process ⬇️

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

## Initial configuration 🔧

- Zeek has two operating ways, real time traffic monitoring and analysing traffic packets (offline);
> ZeekControl is an interactive shell for easily operating/managing Zeek installations on a single system or even across multiple systems in a traffic-monitoring cluster.
- To set the interace which will be analyzed in real time by ZeekControl, edit the `usr/local/zeek/etc/node.cfg` in the following way:

```
[zeek]
type=standalone
host=localhost
interface=eth0   # change this according to your listening interface in ifconfig
```

- In this way, whenever Zeek is initalized through ZeekControl, it will analyse the interface which was set in `node.cfg` file.

## Analyzing interfaces 🔍

One way to analyse interfaces in Zeek is through ZeekControl:

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

## Reading Packet Capture (pcap) Files 📁

One way to generate pcap files is to use tcpdump:

```sudo tcpdump -i en0 -s 0 -w mypackets.trace```

In this command, `-i` is a parameter used to define the interface which will be analyzed, and `-w` indicates the file in which the content of the traffic will be stored. The `-s 0` argument tells tcpdump to capture whole packets, in cases where it’s not supported, one must use -s 65535 instead.

There are other programs which can be used in this process, such as Wireshark.

Zeek can analyse files containing packet captures and generate logs over them. This can be made using commands like the following one:

```
zeek -Cr mypackets.trace
```

In which `-r` is the parameter that tells Zeek to read a file and `-C` is a command that tells Zeek to ignore invalid IP checksums. 

Zeek uses the standard scripts while reading the files. In order to read the files using custom scripts, you can write their directories in the end of the command. For instance:

```
zeek -r mypackets.trace my-script.zeek
```


  
