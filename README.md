# Freeswitch for Docker

Docker image for Freeswitch telephony platform. Based upon Freeswitch 1.2.17.

## Instructions

Download and initialise:

```
$ docker run -t -i lucaspiller/freeswitch /bin/bash
# apt-get install vim # or your favourite editor 
# vim /opt/freeswitch/conf
# hostame # get the container ID
f961910954e5
<Ctrl-D>
```

Commit your changes:

```
docker commit f961910954e5 freeswitch
```

Run:

```
docker run -d -p 5060:5060/tcp -p 5060:5060/udp -p 16384:16384/udp -p 16385:16385/udp -p 16386:16386/udp -p 16387:16387/udp -p 16388:16388/udp -p 16389:16389/udp -p 16390:16390/udp -p 16391:16391/udp -p 16392:16392/udp -p 16393:16393/udp freeswitch run
```

For details on configuration read the [Freeswitch Getting Started Guide](http://wiki.freeswitch.org/wiki/Getting_Started_Guide).

## Configuration

### IP address

By default Freeswitch uses STUN to route through NAT, however this doesn't work with Docker. As such you'll have to set your IP manually*.

Find and set these params to the correct value:

`/opt/freeswitch/conf/autoload_configs/switch.conf.xml`:

```
<X-PRE-PROCESS cmd="set" data="external_sip_ip=<YOUR_EXTERNAL_IP>"/>
<X-PRE-PROCESS cmd="set" data="external_rtp_ip=<YOUR_EXTERNAL_IP>"/>
```

`/opt/freeswitch/conf/sip_profiles/internal.xml`:

```
<param name="ext-rtp-ip" value="$${external_rtp_ip}"/>
<param name="ext-sip-ip" value="$${external_sip_ip}"/>
```

`/opt/freeswitch/conf/sip_profiles/external.xml`:

```
<param name="ext-rtp-ip" value="$${external_rtp_ip}"/>
<param name="ext-sip-ip" value="$${external_sip_ip}"/>
```

*You can also set a DNS name if you have a dynamic IP address. See [NAT Traversal](http://wiki.freeswitch.org/wiki/NAT_Traversal) for further details.

### RTP Ports

Docker doesn't currently allow a range of ports to be opened, as such all RTP ports have to be specified on the command line. In the example above we are only opening 10, so you will be limited to 10 simulataneous calls.

Edit the configuration to ensure only these ports are used:

`/opt/freeswitch/conf/autoload_configs/switch.conf.xml`:

```
<param name="rtp-start-port" value="16384"/>
<param name="rtp-end-port" value="16394"/>
```

## Docker 0.7 bug

There is a bug with Docker 0.7.x that means a port cannot be opened for both TCP and UDP. There is [a fix](3435), but it hasn't yet been merged. As such it is recommended to run this on Docker 0.6.7.k