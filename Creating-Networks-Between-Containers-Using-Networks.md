# Docker Networks

> This scenario explores how to create a `docker network` allowing containers to communicate. We'll also explore the **Embedded DNS Server** added in Docker 1.10.
> **Docker has two approaches to networking**. The first defines *a link between two container*s. This link updates `/etc/hosts` and environment variables to allow containers to discover and communicate.
>The alternate approach is to *create a docker network that containers are connected to*. The network has similar attributes to a physical network, allowing containers to come and go more freely than when using links.

## Step 1 - Create Network

The first step is to create a network using the CLI. This network will allow us to attach multiple containers which will be able to discover each other.

In this example, we're going to start by creating a **backend-network**. All containers attached to our backend will be on this network.

### Task: Create Network

To start with we create the network with our predefined name. `docker network create backend-network`

### Task: Connect To Network

When we launch new containers, we can use the `--net attribute` to assign which network they should be connected to. `docker run -d --name=redis --net=backend-network redis`

In the next step we'll explore the state of the network.

## Step 2 - Network Communication

Unlike using links, `docker network` behave like traditional networks where nodes can be attached/detached.

### Task: Explore

The first thing you'll notice is that Docker no longer assigns environment variables or updates the hosts file of containers. Explore using the following two commands and you'll notice it no longer mentions other containers.

`docker run --net=backend-network alpine env`

`docker run --net=backend-network alpine cat /etc/hosts`

Instead, the way containers can communicate via an Embedded DNS Server in Docker. This DNS server is assigned to all containers via the IP 127.0.0.11 and set in the `resolv.conf` file.

`docker run --net=backend-network alpine cat /etc/resolv.conf`

When containers attempt to access other containers via a well-known name, such as Redis, the DNS server will return the IP address of the correct Container. In this case, the fully qualified name of Redis will be redis.backend-network.

`docker run --net=backend-network alpine ping -c1 redis`

## Step 3 - Connect Two Containers

Docker supports multiple networks and containers being attached to more than one network at a time.

For example, let's create a separate network with a Node.js application that communicates with our existing Redis instance.

### Task

The first task is to create a new network in the same way.

`docker network create frontend-network`

When using the `connect` command it is possible to attach existing containers to the network.

`docker network connect frontend-network redis`

When we launch the web server, given it's attached to the same network it will be able to communicate with our Redis instance.

`docker run -d -p 3000:3000 --net=frontend-network katacoda/redis-node-docker-example`

You can test it using `curl docker:3000`

## Step 4 - Create Aliases

Links are estill supported when uusing `docker network` and provide a way to define an Alias to the container name. This will give the container an extra DNS entry name and way to be  discovered. When using `--link` the embedded DNS will guarantee that localised lookup result only on that container where the `--link` is used.

The other approach is to provide an Alias when connecting a container to a network.

### Connect Container with Alias

The following commands will connect our Redis instance to the frontend-network with the alias of *db*.

```Docker
docker network create frontend-network2
docker network connect --alias db frontend-network2 redis
```

When containers attempt to access a service via the name db, they will be given the IP address of our Redis container.

`docker run --net=frontend-network2 alpine ping -c1 db`

## Step 5 - Disconnect Containers

With our networks created, we can use the CLI to explore the details. the following command will list all the networks on our host:

`docker network ls`

We can then explore hte network to see which containers are attached and their IP addresses.

`docker network inspect frontend-network`

The following command disconnects teh redis container from the frontend-network:

`docker network disconnect frontend-network redis`

# CLI Oveview

```Bash
$ docker network create backend-network
73ad3b81e5032876968b91c6683931f3380897d1022769388b8051400a9cf373
$ docker run -d --name=redis --net=backend-network redis
517f13f262ad632d716349671b19a15d0a4b589a471da019e918708fccb6a9c1
$ docker run --net=backend-network alpine env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=4e6f27d5d86a
HOME=/root
$ docker run --net=backend-network alpine cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.19.0.3      7973a5f2e70b
$ docker run --net=backend-network alpine cat /etc/resolv.conf
nameserver 127.0.0.11
options ndots:0
$ docker run --net=backend-network alpine ping -c1 redis
PING redis (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.121 ms

--- redis ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.121/0.121/0.121 ms
$ docker network create frontend-network
021650540bebcaa93a87f5255acf5e03aa384ca64b9b7a5dd85601d062063e6f
$ docker network connect frontend-network redis
$ docker run -d -p 3000:3000 --net=frontend-network katacoda/redis-node-docker-example
Unable to find image 'katacoda/redis-node-docker-example:latest' locally
latest: Pulling from katacoda/redis-node-docker-example
12b41071e6ce: Pull complete
a3ed95caeb02: Pull complete
49a025abf7e3: Pull complete
1fb1c0be01ab: Pull complete
ae8c1f781cde: Pull complete
db73207ad2ae: Pull complete
446b13034c13: Pull complete
Digest: sha256:1aae9759464f00953c8e078a0e0d0649622fef9dd5655b1491f9ee589ae904b4
Status: Downloaded newer image for katacoda/redis-node-docker-example:latest
2364b05b85a30cc87b0868d9d2341be71fa4c5104fb02e04181f8bb7fdff6a3b
$ curl docker:3000
This page was generated after talking to redis.

Application Build: 1

Total requests: 1

IP count:
    ::ffff:172.17.0.48: 1
$ docker network create frontend-network2
27a4044ab3862fb56cf5553b7eabebc4ee91c32c603cd282043588e8f8f21743
$ docker network connect --alias db frontend-network2 redis
$ docker run --net=frontend-network2 alpine ping -c1 db
PING db (172.21.0.2): 56 data bytes
64 bytes from 172.21.0.2: seq=0 ttl=64 time=0.121 ms

--- db ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.121/0.121/0.121 ms
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
73ad3b81e503        backend-network     bridge              local
d6beccddeef2        bridge              bridge              local
021650540beb        frontend-network    bridge              local
27a4044ab386        frontend-network2   bridge              local
fa054a9af353        host                host                local
f50397115ef2        none                null                local
$ docker network inspect frontend-network
[
    {
        "Name": "frontend-network",
        "Id": "021650540bebcaa93a87f5255acf5e03aa384ca64b9b7a5dd85601d062063e6f",
        "Created": "2020-12-29T13:40:11.07928013Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.20.0.0/16",
                    "Gateway": "172.20.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "2364b05b85a30cc87b0868d9d2341be71fa4c5104fb02e04181f8bb7fdff6a3b": {
                "Name": "distracted_ride",
                "EndpointID": "90dc33e33a24222cd2dee671a3f37f5a3ac2d6fee4a11f0623891a59b14c611b",
                "MacAddress": "02:42:ac:14:00:03",
                "IPv4Address": "172.20.0.3/16",
                "IPv6Address": ""
            },
            "517f13f262ad632d716349671b19a15d0a4b589a471da019e918708fccb6a9c1": {
                "Name": "redis",
                "EndpointID": "866c99adc9fda01cf1db1cff11aa374d3968d0d056532322798ec471335ba328",
                "MacAddress": "02:42:ac:14:00:02",
                "IPv4Address": "172.20.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
$ docker network disconnect frontend-network redis
$
```