## An Introduction to Networking Terminology, Interfaces, and Protocols

### Introduction

A basic understanding of networking is important for anyone managing a server.  Not only is it essential for getting your services on-line and running smoothly, it also gives you the insight to diagnose problems.  

This document will provide a basic overview of some common networking concepts.  We will discuss basic terminology, common protocols, and the responsibilities and characteristics of the different layers of networking.  

This guide is operating system agnostic, but should be very helpful when implementing features and services that utilize networking on your server.

#### Networking Glossary

Before we begin to discuss networking with any depth, we must define some common terms that you will see throughout this guide, and in other guides and documentation regarding networking.

These terms will be expanded upon in the appropriate sections that follow:

-	**Connection:**	In networking, a connection refers to pieces of related information that are transfered through a network.  This generally infers that a connection is built before the data transfer (by following the procedures laid out in a protocol) and then is deconstructed at the end of the data transfer.

-	**Packet:** A packet is, generally speaking, the most basic unit that is transfered over a network.  When communication over a network, packets are the envelopes that carry your data (in pieces) from one end point to the other.  

Packets have a header portion that contains information about the packet including the source and destination, timestamps, network hops, etc.  The main portion of a packet contains the actual data being transfered.  It is sometimes called the body or the payload.

-	**Network Interface:** A network interface can refer to any kind of software interface to networking hardware.  For instance, if you have two network cards in your computer, you can control and configure each network interface associated with them individually.

A network interface may be associated with a physical device, or it may be a representation of a virtual interface.  The "loopback" device, which is a virtual interface to the local machine, is an example of this.

-	**LAN:** LAN stands for "local area network".  It refers to a network that is not publicly accessible to the greater INTERNET.  A home o office network is an example of a LAN.

-	**WAN:** WAN stands for "wide area network".  It means a network that is much more extensive than a LAN.  While WAN is the relevant term to use to describe large, dispersed networks in general, it is usually meant to mean the INTERNET as a whole.

If an interface is said to be connected to the WAN, it is generally assumed that it is reachable through the INTERNET
















