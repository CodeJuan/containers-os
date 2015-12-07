# Swarm on Mesos scalability tests

## Environment tested

All tests are performed in a cluster of 10 or 20 nodes of SoftLayer machines.

Hosts:
```
SoftLayer Public CCIs
1 master: 8 CPU / 16 GB RAM
10/20 slaves: 8 CPU / 16 GB RAM
Ubuntu 14.04 LTS 64 bits
Kernel: Linux 3.19.0-31-generic #36~14.04.1-Ubuntu SMP Thu Oct 8 10:21:08 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```
Docker version:
```
Client:
 Version:      1.9.0
 API version:  1.21
 Go version:   go1.4.2
 Git commit:   76d6bc9
 Built:        Tue Nov  3 17:43:42 UTC 2015
 OS/Arch:      linux/amd64

Server:
 Version:      1.9.0
 API version:  1.21
 Go version:   go1.4.2
 Git commit:   76d6bc9
 Built:        Tue Nov  3 17:43:42 UTC 2015
 OS/Arch:      linux/amd64
```
Mesos:
```
version 0.25.0
```
Swarm:
```
version 1.0.0 (official image from Docker Hub - https://hub.docker.com/_/Swarm/ )
```
For non-Marathon managed Swarm, we start Swarm with:
```
docker run -e Swarm_MESOS_USER=root -d -p 4375:2375 -p 3376:3375 --name Swarm Swarm manage -c mesos-experimental --cluster-opt mesos.address=0.0.0.0 --cluster-opt mesos.port=3376 --cluster-opt mesos.tasktimeout=10m --cluster-opt mesos.offertimeout=<N>m 1<Master-IP>:5050
```
We tested with <N>=1 and <N>=10000 (big offer timeout)

## Benchmark
Baseline scalability test:
The test executes sequentially the following steps:
1. Start container on Swarm with busybox image, default docker networking with docker bridge and icc = true, and the default httpd server
2. Capture the time it takes for docker run to return - 'Container Launched'
3. Use inspect on launched container to find out when it goes to 'Running' state - 'Container Running'
4. Use inspect to get IP address and host for the container and measure time to TCP connectivity.
Note that we wait for completion of each step before starting the next one.

## Results
We have tested Swarm with the following setups:

1. Swarm standalone (without mesos)

2. Swarm on mesos, with Swarm manager as marathon managed app

3. Swarm on mesos, with Swarm manager non managed by marathon

We ran the tests on Swarm standalone to provide a baseline for the tests on mesos. Our tests results for a 10 nodes cluster are shown here:

![alt text](https://github.com/Open-I-Beam/containers-os/blob/master/swarm-mesos/151201-paolo-swarm-on-mesos/test-10000-d1.9-k3.19-swarm1.0-10nodes.png "Swarm 1.0, Docker 1.9, 10 Nodes Cluster")
