# An Introduction to Using Consul, a Service Discovery System, on Ubuntu 14.04

Introduction
**Consul** is a distributed, highly available, datacenter-aware, service discovery and configuration system. It can be used to present services and nodes in a flexible and powerful interface that allows clients to always have an up-to-date view of the infrastructure they are a part of.

Consul provides many different features that are used to provide consistent and available information about your infrastructure. This includes service and node discovery mechanisms, a tagging system, health checks, consensus-based election routines, system-wide key/value storage, and more. By leveraging consul within your organization, you can easily build a sophisticated level of awareness into your applications and services.

In this article, we will introduce you to some of the basics of using consul. We will cover the general procedures necessary to get consul running on your servers to test it out. In the next guide, we will focus on setting up consul in a production environment.

## Prerequisites and Goals

In this article, we will be getting familiar with using consul to build out a system of service discovery and configuration for your infrastructure.

For our demonstration, we will be configuring three servers and one client. Servers are used to handle queries and maintain a consistent view of the system. The client is also a member of the system, and can connect to the servers for information about the infrastructure. Clients may also contain services that will be monitored by consul.

For the purposes of this guide, and this series as a whole, we will be configuring 4 instances. The first three will be consul servers as described above. The last one will be a consul agent that acts as a client and can be used to query information about the system.

For us to implement some of the security mechanisms at a later point, we need to name all of our machines within a single domain. This is so that we can leverage a wildcard SSL certificate in the future.

The details of our machines are here:

| Hostname | IP Address | Role
|:-:|:-:|:-:|
svr1.jagho.tk | 10.0.0.254 | bootstrap consul server
svr2.jagho.tk | 10.0.0.250 | consul server
svr3.jagho.tk | 10.0.0.70 | consul server
svr4.jagho.tk | 10.0.0.25 | consul client

We will be using 64-bit Ubuntu 16.04 servers for this demonstration, but any modern Linux server should work equally well.

### Downloading and Installing Consul

The first step that we need to take is to download and install the consul software on each of our machines. The following steps should be taken on each of the machines listed above. You should be logged in as _***root***_.

Before we look into the consul application, we need to get `unzip` to extract the executable. We will also use the `screen` application to allow us to easily have multiple sessions in a single terminal window. This is useful for our introduction, since consul typically takes up the entire screen when not run as a service.

Update the local systems package cache and then install the package using `apt`:

```
apt-get update
apt-get install unzip screen
```

So we do not forget to do so later, start your screen session now:

```
screen
```

Press enter if you get a copyright message. You will be dropped back into a terminal window, but you are now inside a screen session.

Now, we can go about getting the consul program. The [consul project's page](https://www.consul.io/downloads.html) provides download links to binary packages for Windows, OS X, and Linux.

Go to the page above and right-click on the operating system and architecture that represents your servers. In this guide, since we are using 64-bit servers, we will use the "amd64" link under "linux". Select "copy link location" or whatever similar option your browser provides.

```
cd /usr/local/bin
wget https://releases.hashicorp.com/consul/1.0.0/consul_1.0.0_linux_amd64.zip
```

Now, we can extract the binary package using the `unzip` command that we installed earlier. We can then remove the zipped file:

```
unzip *.zip
rm *.zip
```

You should now have the `consul` command available on all of your servers.

## Starting the Bootstrap Server

To begin working with consul, we need to get our consul servers up and running. When configuring this in the recommended multi-server environment, this step will have to be done in stages.

The first thing we need to do is start the consul program on one of our servers in `server` and `bootstrap` mode. The server mode means that the consul will start up as a server instance instead of a client. The bootstrap option is used for the first server. This allows it to designate itself as the "leader" for the cluster without an election (since it will be the only server available).

In the table that specifies our hosts, we designated our `svr1` as the bootstrap server. On svr1, start the bootstrap instance by typing:

```
consul agent -server -bootstrap -data-dir /tmp/consul
```

The server will start up in the current terminal and log data will be output as events occur. Towards the end of the log data, you will see these lines:

```
. . .
2017/11/03 14:32:15 [ERR] agent: failed to sync remote state: No cluster leader
2017/11/03 14:32:17 [WARN] raft: Heartbeat timeout reached, starting election
2017/11/03 14:32:17 [INFO] raft: Node at 192.0.2.1:8300 [Candidate] entering Candidate state
2017/11/03 14:32:17 [INFO] raft: Election won. Tally: 1
2017/11/03 14:32:17 [INFO] raft: Node at 192.0.2.1:8300 [Leader] entering Leader state
2017/11/03 14:32:17 [INFO] consul: cluster leadership acquired
2017/11/03 14:32:17 [INFO] consul: New leader elected: server1.example.com
2017/11/03 14:32:17 [INFO] consul: member 'server1.example.com' joined, marking health alive
```

As you can see, no cluster leader was found since this is the initial node. However, since we enabled the bootstrap option, this server was able to enter the leader state by itself in order to initiate a cluster with a single host.

## Starting the Other Servers

On `svr2` and `svr3`, we can now start the consul service without the bootstrap option by typing:

```
consul agent -server -data-dir /tmp/consul
```

For these servers, you will also see the log entries. Towards the end, you will see messages like this:

```
. . .
2017/11/03 14:37:25 [ERR] agent: failed to sync remote state: No cluster leader
2017/11/03 14:37:27 [WARN] raft: EnableSingleNode disabled, and no known peers. Aborting election.
2017/11/03 14:37:53 [ERR] agent: failed to sync remote state: No cluster leader
```

This happens because it cannot find a cluster leader and is not enabled to become the leader itself. This state occurs because our second and third server are enabled, but none of our servers are connected with each other yet.

To connect to each other, we need to join these servers to one another. This can be done in any direction, but the easiest is from the our `svr1` machine.

Since we are running the consul server in the current terminal window of `svr1`, we will have to create another terminal with `screen` in order to do additional work. Create a new terminal window within the existing screen session of `svr1` by typing:

```
CTRL-A C
```

This will open a fresh terminal instance while keeping our previous session running. You can step through each of the existing terminal sessions by typing:

```
CTRL-A N
```

Back in your fresh terminal, join the other two instances by referencing their IP addresses like this:

```
consul join 10.0.0.250 10.0.0.70
```

This should instantly join all three servers into the same cluster. You can double check this by typing:

```
consul members
```

```
Node                Address             Status  Type    Build  Protocol
svr1.jagho.tk 		10.0.0.254:8301  	alive   server  0.3.0  2
svr2.jagho.tk  		10.0.0.250:8301  	alive   server  0.3.0  2
svr3.jagho.tk   	10.0.0.70:8301  	alive   server  0.3.0  2
```

You can get this information from any of the other servers as well by creating a new terminal session in screen as we described above and issuing the same command.























































