<!-- @import "C:\Users\mdriscoll\.mume\themes\night.css"--->

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