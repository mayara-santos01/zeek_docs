# DNS Tunnelling

This attack is based on sending non-DNS packets trough the 53/UDP port, in order to mock the security systems, such as firewalls. DNS tunnels can be used to establish Command and Control channels, used to manage DDoS attacks. They can also be used to send malwares and provoke damages in an aimed device or set of devices. 

In this sense, it is extremely important to detect this type of attack, in order to avoid major damages. Thus, Zeek can be used in this task, due to its capability to promote a custom parsing of the traffic. 

It was studied the detection of DNS Tunneling using 2 techniques:

1. A custom script to calculate the entropy of DNS queries used in the establishement of the tunnel. The entropy was calculate using the Shannon formula, which can be used to calculate how random is a data;
2. A custom analyzer which parses all traffic going to the port 53, generating logs based on the presence of error bytes and the high size of the payload.

More information on creating new Zeek's analyzers and scripts [here](https://github.com/13917931-project-tasks/dnstun-docs/blob/master/zeek_info.md).

## Setting the custom script 🔧

The script used to calculate the entropy of the queries can be downloaded [here](https://github.com/13917931-project-tasks/dnstun-docs/blob/master/main.zeek). Then, you can create a new folder in the directory `/usr/local/zeek/share/zeek/policy/protocols`, and place the script in there. A zeek script is a `.zeek` file.

Then, to make Zeek recognize the script, you should change the `local.zeek` file, located in the `/usr/local/zeek/share/zeek/site/` directory, and add some lines, like it is shown in the following example:

```
# Write a brief description of your script
@load protocols/<your_folder_name>/main.zeek
```

You should **not** write the `.zeek` extension while adding tour script to `local.zeek` file.

## Installing the custom analyzer ⬇️

```
git clone https://github.com/13917931-project-tasks/DNSAnomalies.git
zkg install /DNSAnomalies
# or run in the root mode, instead:
/usr/local/zeek/bin/zkg install /DNSAnomalies
```

## Testing the perfomance of the custom frameworks 🔍

To run the tests, it will be used the *firewall* topology of [Mininet-Sec](https://github.com/mininet-sec/mininet-sec/?tab=readme-ov-file#mininet-sec). After isntalling Mininet-Sec, run:

```
python3 /mininet-sec/examples/firewall.py
```

In another terminal, edit the interface listened by Zeek, in order to track the traffic of the mnsec topology. In order to do this, the user must edit the node.cfg file, which is located in the directory /usr/local/zeek/etc/, in case installation from source code, and make the following changes:

```
[zeek]
type=standalone
host=localhost
interface=nettap1-eth2
```

Then, in root mode, start Zeek:

```
cd /usr/local/zeek/bin
./zeekctl
```

Then, run *deploy* on ZeekControl shell to activate Zeek. The user can check if Zeek is running correctly by executing *status* in ZeekControl shell.

In another terminal run:

```
mnsecx fw0 iptables -A FORWARD -d 10.0.0.2 -p udp --dport 53 -j ACCEPT

mnsecx srv2 iodined -f -c -P ChangeMe-123 10.199.199.1/24 iodine.hackinsdn.ufba.br 
```
Due to firewall configurations, perhaps it will be necessary to make some adjusts in order to allow the traffic which will be used to establish a DNS Tunnel.

```
iptables -I FORWARD -i s1+ -j ACCEPT
iptables -I FORWARD -i s2+ -j ACCEPT
mnsecx o1 iodine -f -P ChangeMe-123 10.0.0.2 iodine.hackinsdn.ufba.br
```

Then, some test commands can be run to analyze the activity of tunnel and its detection by Zeek, for instance:

```
sudo mnsecx o1 ping -s 1200 -f 10.199.199.1
```
