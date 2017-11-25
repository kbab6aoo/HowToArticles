## Understanding IP Addresses, Subnets and CIDR Notation for Networking

### Introduction

Understanding networking is a fundamental part of configuring complex environments on the internet. This has implications when trying to communicate between servers efficiently, developing secure network policies, and keeping your nodes organized.

In a previous guide, we went over some basic networking terminology. You should look through that guide to make sure you are familiar with the concepts presented there.

In this article, we will discuss some more specific concepts that are involved with designing or interacting with networked computers. Specifically, we will be covering network classes, subnets, and CIDR notation for grouping IP addresses.

### Understanding IP Addresses

Every location or device on a network must be _addressable._ This is simply a term that means that it can be reached by referencing its designation under a predefined system of addresses. In the normal TCP/IP model of network layering, this is handled on a few different layers, but usually, when we refer to an address on a network, we are talking about an IP address.

IP addresses allow network resources to be reached through a network interface. If one computer wants to communicate with another computer, it can address the information to the remote computer's IP address. Assuming that the two computers are on the same network, or that the different computers and devices in between can translate requests across networks, the computers should be able to reach each other and send information.

Each IP address must be unique on its own network. Networks can be isolated from one another, and they can be bridged and translated to provide access between distinct networks. A system called **Network Address Translation,** allows the addresses to be rewritten when packets traverse network borders to allow them to continue on to their correct destination. This allows the same IP address to be used on multiple, isolated networks while still allowing these to communicate with each other if configured correctly.

### The differnce between IPv4 and IPv6

There are two revisions of the IP protocol that are widely implemented on systems today. IPv4, which is the fourth version of the protocol, currently is what the majority of systems support. The newer, sixth revision, called IPv6, is being rolled out with greater frequency due to improvements in the protocol and the limitations of IPv4 address space. Simply put, the world now has too many internet-connected devices for the amount of addresses available through IPv4.

IPv4 addresses are 32-bit addresses. Each byte, or 8-bit segment of the address, is divided by a period and typically expressed as a number 0-255. Even though these numbers are typically expressed in decimal to aid in human comprehension, each segment is usually referred to as an octet to express the fact that it is a representation of 8 bits.

A typical IPv4 address looks something like this:

```
192.168.0.5
```

The lowest value in each octet is a 0, and the highest value is 255.

We can also express this in binary to get a better idea of how the four octets will look. We will separate each 4 bits by a space for readability and replace the dots with dashes:

```
1100 0000 - 1010 1000 - 0000 0000 - 0000 0101
```

Recognizing that these two formats represent the same number will be important for understanding concepts later on.

Although there are some other differences in the protocol and background functionality of IPv4 and IPv6, the most noticeable difference is the address space. IPv6 expresses addresses as an 128-bit number. To put that into perspective, this means that IPv6 has space for more than 7.9×1028 times the amount of addresses as IPv4.

https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking















