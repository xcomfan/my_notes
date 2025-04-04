---
layout: page
title: "Adrian Cantrill Tech Fundamentals Course Notes"
permalink: /unsorted/tech_fundamentals
---
#UNPROCESSED

# OSI 7-Layer Network Model

## Layer 1 - Physical Layer

Layer 1 (Physical) specifications define the transmission and reception of **raw bit stream** between devices and a **shared** physical medium. It defines things like voltage levels, timing, rates, distances,  modulation and connections. For a copper cable electrical signals are used, for fibre light is used, WiFi uses RF. The standards make it such that each device can understand the medium being used to receive the binary signal.

At layer 1, there is not direct device addressing. One computer cannot directly address another computer. Even if you are suing a hub anything ent by one computer is transmitted to all of the computers.

Only one device can transmit at once on a shared medium (cable or something similar linking the devices) in Layer 1. If two devices transmit at once there is a collision and it will corrupt any transmission on the shared medium. At layer 1 there is no method of controlling which devices can transmit so collisions are almost guaranteed.

Effectively there IS NO device to device communication on a Layer 1 network. Everything is broadcast to the shared medium.

## Layer 2 - Data Link Layer

Layer 2 is built on top of Layer 1. Layer 2 network requires a functional Layer 1 network to operate. It does not matter if Layer 1 is copper fibre or something else Layer 2 will have same capabilities.

Layer 2 introduces the concept of a Data Link Frame and a MAC address for every devices on the network.

A MAC address is 48 bits in hex. 24 bits is used for the manufacturer of the networking hardware and the rest is used to make the device unique.

Layer 2 provides frames and Layer 1 provides the physical medium for sending those Frames.

A Data Frame is a containers of sorts. First part of a frame is the Preamble and start frame delimiter. This lets devices know where a Frame starts so they can know where to look for the various components of the Frame.  Next come the destination and source MAC addresses. In Layer 2 you can send a message to a specific device by putting its MAC address in the destination of use all `F` in MAC address to send to all devices (this is known as a broadcast). Next is the "Ether Type" which is commonly used to specify which Layer 3 protocol is putting data inside the Frame. Next comes the payload which is the actual data that is being carried by the Frame. This data is a Layer 3 IP Packet. Last you have the CRC check to help identify any errors in the Frame.

| Preamble 56 bits SFD - 8 Bits | Destination MAC address | Source MAC Address | ET 16 Bits | Payload 46 - 1500 Bytes | FCS Check 32 bits |
| ----------------------------- | ----------------------- | ------------------ | ---------- | ----------------------- | ----------------- |

Layer 2 also provides controlled access to the Physical Medium which addresses the collision problem of Layer 1. Layer 2 can communicate with Layer 1 and look for signs of a carrier signal. If any device is transmitting at time of check you would see the carrier signal on the Layer 1 network. This is done via CSMA (Carrier Sense Multiple Access). So Layer 2 waits for Layer 1 to be free (nothing transmitting) to send its data. If two devices find no carrier signal and send at the exact same time a collision is detected via Collision Detections (CD) then a jam signal is sent by all devices which detect it an a random backoff occurs. CD is a part of Layer 2 and part of Layer 2 CSMA/CD. Random backoff is each device backing off for a random time and trying to send again. IF collision is detected again they will back off for a longer random time. Essentially you are reducing likelihood of collision. Collision detection is what allows multiple devices to coexist on the same network. Collision detecting works by the sending device checking for another device transmitting while its sending. If detected it sends the Jam signal.

When data is received on Layer 1 and given to Layer to that is called de-encapsulated and encapsulation is when you pass the data to a lower Layer from 2 to 1 for example. You encapsulate the data into a Frame and de-encapsulate it from the Frame.

One thing of note is Layer 2 will talk to Layer 2 and they don't really care about Layer 1. Layer 2 abstracts away the Level 1 details.

Revisiting using a HUB. A HUB is a layer 1 device so it will jure rebroadcast all Layer 1 traffic including Collisions. A switch however is a Layer 2 devices. Switch maintains a MAC address table whit it uses to learn what is connected to each of its port. Thus a switch can be smart enough to take the traffic from one of the machines connected to it, and send it directly to another machine connected to it (while switch is populating the MAC address table it acts like a HUB. In other words if it does not know which port destination MAC address is on it will sent to all the ports). This also makes it so that each of the ports on a Switch is its own collision domain. Only collisions between the switch port and device its connected to can occur on that domain. Corrupted data will not be forwarded to any other ports.

The internet is a collection of Layer 2 networks.

Layer 2 allows for identifiable devices. Media access control and collision detection. It also allows you to do unicast communication (1:1) and multicast communication (one to many).

## Layer 3 - Network Layer

Job of Layer 3 is to get data from one device to another. Think of loading a website fo example.

Layer 3 deals with the IP packet similar to how Layer 2 deals with Frames.

Every IP Packet has a source and destination IP address. There is also a protocol field which stores which Layer 4 protocol is being transmitted by this Layer 3 packet. This protocol fields specifies if you are using TCP (value will be 6), ICMP (value will be 1) or UDP (value will be 17). The rest is the data section as well as a TTL (which is used to control the maximum number of hops so that the packet will not bounce around forever.) There is a header section of the IP packet the captures the IP protocol version, header length, types of service, total length, identification, IP flags, and fragment offset.

Layer 3 works with both IPv4 and IPv6.

### IPv4 details

Lets use IP address 133.33.3.7 as an example.  IP addresses use dotted decimal notation and are made of 4 numbers between 0 and 255 separated by dots (`.`).

The first two numbers (133.33 in our example) are the network part of the address. The second two numbers (3.7 in our example are the host parts). If network part of two IP addresses matches then they are on the same IP network. If its different they are on different networks.

IP addresses can be assigned manually of via DHCP. IP addresses need to be unique on your local network or bad things happen.

Subnet Masks help us determine if a network is local. They are configured on network interfaces. Default Gateway is also configured on network interfaces. That is where packets are forwarded to if the destination is not a local IP address.

#### Submask Deep Dive

Lets take our example IP address and break it down into binary

`133.33.3.7` becomes:

`10000101.00100001.00000011.00000111`

When applying/creating a subnet mask a `1` represents a part of the address that is used for network and a `0` denotes a part that is used for hosts.

When we refer to a slash n network such as a slash 16 (`/16`) that is a count of ones. So a slash 16 network will have 16 ones `11111111.11111111.00000000.00000000`. So if you overlay an IP address and a subnet its easy to tell which is the network part and which is the host part. Network mask also makes it easy to calculate the start of a network.  If you use all the numbers in your IP address where the subnet has 1s and set all of the host portion to zeo that will be the start of the network addresses. If you use all of the numbers in your IP address where the subnet 1s and set the host portion to all 1s that will be the end of the range.

The higher the slash number the more specific the address. A `/32` is the maximum and represents just one IP address.

### Route Tables and Routes

Every router has a routing table per interface and in that routing table it has a Destination network as well as the next hop/target. For example.

| Destination    | Next Hop/Target |
| -------------- | --------------- |
| 52.217.13.0/24 | 52.217.13.1     |
| 0.0.0.0/0      | 52.42.214.1     |
| 52.43.215.0/24 | 52.43.215.1     |

If multiple routes in routing table match the most specific one is used. In above example 0.0.0.0/0 is most specific so its the default route.

Route tables can be populated statically or via BGP.

Routing is the process where packets are routed hop by hop from source to destination.

MAC address of the destination routed is done via ARP (Address Resolution Protocol)

### ARP

ARP is the protocol used to find a MAC address for a given IP address. 

As an example lets say we have two computers one at IP `133.33.3.7` and one at `133.33.3.10` trying to send data. At Layer 3 we create a packet with `133.33.3.7` as the source and `133.33.3.10` as the destination. When we encapsulate this packet into Layer 2 we need to know the MAC address. ARP will send a broadcast on all FFF address asking for who has the  `133.33.3.10`. The computers with the `133.33.3.10` address will respond with its MAC address. Now that the sending computer has the MAC address is can encapsulate the Frame to Layer 1 and send the data over the physical medium.

In a more complex example where the two computers are not on the same network routers get involved. The sending computer will check the destination IP address and use the Netmask to determine if its sending to local network or not. Since its not a local network send the default gateway IP is used which is the router. So at layer 3 we have the destination IP of the system we are sending to, but at Layer 2 we are going to use ARP and find the MAC address of the Router to send it the Frame. When the router receives the packet will see that the MAC address is its own and it will strip away the frame leaving just the packet. A normal device such as a computer or phone will just drop a packet not intended for it, but a router is meant to route packets. At this point the router will check the destination IP of the packet and use its routing table to determine  where to send it for the next hop. It will encapsulate the packet into a Fram and use ARP to find the MAC address for the next hop target (next router on the network). This goes on until we reach the router where the destination computer is local.

### Layer 3 Summary

Layer 3 adds IPv4 and IPv6 also known as cross network addressing

It also adds ARP for finding the MAC address of an IP

Layer 3 adds routes which define where to forward this packet, and route tables so that multiple routes can be managed.

Routers are Layer 3 devices. They move packets from source to destination encapsulating in L2 along the way.

Layer 3 allows for device to device communication over the internet.

Layer 3 DOES NOT provide a method for channels for communication.  You only have packets with source and destination. You can't have different applications on two computers communicating with each other at the same time. This is addressed by layers 4 and 5. Layer 3 just gives you one stream of data.

Layer 3 packets may also be delivered out of order. Due to network conditions packets may arrive in different order then they were sent in.

## Layer 4 and 5 Transport and Session Layer

Mixing these two because its easier to cover them together as there is some overlap between the two. "Its generally not worth the argument to decide if something is covered in Layer 4 or Layer 5"

Layer 3 Limitations:

* At Layer 3 each packet is an independent entity and is routed separately over the internet. This leads to the potential issues of:
	* Out of order arrival of packets
	* Packets can just go missing due to network conditions. (Not Guaranteed to be reliable)
	* Per packet routing can introduce delays to packets in route. Different packets can experience different delays.
	* No way to separate the packets meant for different applications.
	* No flow control. If source is sending faster than destination can receive it can saturate the destination causing packet loss.


### Layer 4

Layer 4 adds two protocols. TCP (Transmission Control Protocol) and UDP (User Datagram Protocol). Both work on top of IP and have a different set of features.

#### UDP

Faster than TCP but less reliable. It does not have the overhead needed for reliable delivery which TCP does.
#### TCP

Provides reliability, error handling and ordering of data. Used for HTTP, HTTPS, SSH and so on.

TCP is a connection oriented protocol and establishes a bi directional channel of communications between two devices.

TCP operates on **segments**. TCP segments are encapsulated within IP packets. Segments don't have source and destination IP addresses, the packets provide IP addressing.

Segment has the following structure. ***Note:*** options and padding is omitted for simplicity.

| Source Port | Destination Port | Sequence Number | Acknowledgement | Flags and things* | Window | Checksum | Urgent Pointer | Data | 

Ports allow for multiple applications on same computer to communicate at the same time.

Sequence Number is used to determine order of packets and can also be used in error correction. It is incremented with each segment sent.

Acknowledgment is used for one side to indicate that it received up to and including a certain sequence number. Every segment that is transmitted needs to be acknowledged.

Flags and things is used for various controls over TCP segments and the wider connection. Can close the connection here, but also have options for offsets etc. Some of the value are `FIN` to close connection `ACK` for acknowledgements and `SYN` for synchronizing sequence numbers.

Window indicates the number of bytes you are willing to receive between acknowledgements. Sender will pause until you receive that amount of data. This is used for flow control.

Checksum used for error checking.

Urgent Pointer is used to indicate high priority traffic. For example if 99% of traffic send is just data and 1% is control traffic you can give the control traffic priority. FTP and Telnet use this field for example.

All of these above are in the TCP header. The remainder of the segment is the actual data being sent. 

TCP provides for a way to send a reliable (segment can be retransmitted if it fails to arrive) ordered (we have sequence numbers) set of data.  You can build on this to establish a connection between two systems.

##### TCP 3 Way Handshake
Step 1: Client sends a segment with `SYN` in the flags and things part of the segment with a random sequence number known as `CS`. The random sequence number is called ISN (Initial Sequence Number) This is how the client says it wants to establish a connection.

Step 2: Server replies with a `SYN-ACK` segment. It picks its own random sequence number known as `SS` (Server Sequence) It sets the acknowledgement part to `CS+1` from the client call. This is saying I have received up to `CS` now send me `CS + 1` . 

SYN is synchronize sequence numbers and SYN-ACK is also used to synchronize sequence numbers (its the acknowledgment)

Step 3: Client sends segment with `ACK` (Acknowledge) set to `SS+1` which means I have received `SS` now send me `SS+1` and the sequence set to `CS + 1`

At this point the connection is established. Going forward any time either side sends data it increments the sequence and the other side acknowledges the sequence + 1. This allows for re-transmission when data is lost.

##### Sessions and State
Strictly speaking the concept of a session and an established connection is considered Layer 5

# Network Address Translation (NAT)

NAT translates private IPv4 addresses to public ones.

NAT is designed to overcome IPv4 shortages

## Static NAT
**Static NAT** maps one private IP to one fixed public address. **Dynamic NAT** is similar but you have a pool of public IP addresses. So its a one private IP maps to first available public IP. Typically used when you have a lot of private IP addresses that need access to the internet.

#TODO: need to write up and link public vs private addresses.

Private IP addresses cannot communicate over the public internet. 

With Static NAT the router (NAT Device) maintains a NAT table which maps private IP to public IP in a one to one mapping. So each device has a private IP and a public one assigned to it in the NAT table.

If trying to communicate to a public IP the client will generate a packet with its private IP as the source and the public IP as the destination. Since the destination is not local it will be sent to the Default Gateway which is the router. As the packet passed though the route the source address will be swapped by the route (the NAT device here) based on the NAT table.  (AWS Internet Gateway does this FYI). On the way back the same thing happens, but here the NAT device swaps the public IP in the destination for the private one so that the requesting client can get the response from the public IP.

**Port Address Translation (PAT)** is when you have many private devices mapping to one public IP address. This is what most home routers do.

NAT is only needed for IPv4. IPv6 has so many addresses that translation is not needed.

## Dynamic NAT

Imagine a scenario where in a private network you have 4 machines, but you only have two public IP addresses. In this scenario the router (NAT device) maintains a NAT table which maps private to public IP addresses, but with Dynamic NAT the IP allocation are temporarily assigned from a pool of available public IPs.

If there are not public IP addresses available in the pool (all being used for example) than access for the private machine trying to reach something on public internet will fail.

## Port Address Translation

Port Address Translation allows many devices to share a single public IP address.

The NAT device records the source (private) IP and source port. It replaces the source IP with the single public IP and a public source port allocated from a pool which allows IP overloading (many to one)

The nat table when using port address translation would look like:

| Private IP | Private Port | Public IP   | Public Port |
| ---------- | ------------ | ----------- | ----------- |
| 10.0.0.42  | 32768        | 52.95.36.67 | 1337        |
| 10.0.0.43  | 32769        | 52.95.36.67 | 1338        |
| 10.0.0.44  | 32768        | 52.95.36.67 | 1339        |
Note that you cannot initiate traffic to the devices on the private network as without the entry in the NAT table which gets created from the request coming from the private network there is no way to route the traffic.

Your home routers and AWS Internet Gateway use Port Address Translation

# IP Address Space and Subnetting

## Network Classes

### Class A Networks

Starts at `0.0.0.0` end at `127.255.255.255` has 128 networks with 16,777,216 IPs each

First octet denotes the network the rest is available for hosts or subnetting.

0.x.x.x is reserved.

Historically Class A networks were allocated to large businesses but now they have been giving to regional management to allocated IPs within that region.

### Class B Networks

Starts at `128.0.0.0` end at `191.255.255.255` has 16,384 networks each with 65,536 IPs each.

This class was typically used for larger businesses that did not need a Class A allocation. These are also now allocated by regional authorities.

First two octets define the network and the rest are used for hosts or for subnetting.

### Class C Networks

Starts at `192.0.0.0` end at `223.255.255.255` has 2,097,152 networks with 256 IPs each.

First 3 octets denote the network and the rest is for hosts or subnetting. 

Used to be for smaller businesses that needed an IP presence, but now also managed by regional authorities.

### Class D and Class E

Class D is used for multicast and Class E is reserved.

## Private IP Addresses

There are IP addresses reserved for private use. These IP addresses are not routable over public internet.

Defined by standards document RFC1918

The 3 ranges are:

`10.0.0.0` - `10.255.255.255` ( 1 x Class A Network) - 16,777,216 IPv4 addresses

`172.16.0.0` - `172.31.255.355` (16 x Class B Networks) - each of 16 the has 65,536 addresses.

`192.168.0.0` - `192.168.255.255` (246 x Class C Networks) - each of the 256 has 256 addresses. Typically used for home and small office networks.

You can allocate these for private use however you like, but remember that you will have challenges if you have overlapping networks.

## Subnetting

Classless Inter Domain Routing (CIDR) lets us take a network (Class A, B, C etc) and break them down.

`/8` is a class A network (only first octet is used to define the network)

Entire internet is a `/0` network.

`/32` is a single IP address.

Networks are usually split into 2, 4, 8 .. though while unusual odd number splits are valid.

Subnetting is the process of taking a larger network and breaking it into more smaller networks each of which has a higher prefix.

Lets work through an example:

`10.16.0.0/16` = `10.16.0.0` to `10.16.255.255`

A `/17` will be half the size of a `/16` so ...

`10.16.0.0/17` = `10.16.0.0` to `10.16.127.255` (halfway though the original `/16` range). The second smaller network will be  `10.16.128.0/17` from `10.16.128.0` to `10.16.255.255`

We have not split the `/16` into two.

Note: in the above example if we split second half, but not the fist half that is how you get an odd number of splits.

You can split each of the `/17` in half again by using a `/18`; and so on till you have a single IP with a `/32`


## IPv6

IPv6 has effectively infinite IPv6 addresses.

# VLANs, Trunks and Q-in-Q

Imagine a scenario where you need to have different networks for different parts of your business. For example an out of band management network an office network, a server network, production network, a testing network. Isolating these on a physical network while still allowing them to communicate is very difficult. This is where VLANs come in.

VLANs work by leveraging the 802.1q standard (also known as 1.Q) which changes the frame format of a standard Layer 2 Ethernet Frame by adding a 32 bit field. (Frame size is made larger to acomodate this new data) 12 bits of this field can be used to represent values from 0 to 4096 which identifies the VLAN. A 0 in this field means no VLAN; 1 if generally used to signify the management VLAN. Rest can be used as desired by the local network admin.

This allows you to create virtual isolated network domains. A broadcast to all Fs (of a MAC address) would only be broadcast to devices on the same VLAN.

A challenge you may run into is if you are working with a provider that on their network uses the same VLAN ID as you are using.  To help with this scenario 802.1AD standard helps. This is known as **QinQ** also known as provider bridging or stacked VLANs.  This adds another field to the Ethernet frame. So now you have one for your network known as the **CS tag** (customer tag) and one for the provider know as **SS tag** (service tag). Now both you and the provided can use VLAN tags. Once frame arrives at your network the SS tag is stripped off and you get the frame as a standard 802.1q style frame.

In a VLAN capable network there are two kinds of ports on a switch the first is access ports that are used to connect devices on that network to the switch the other type is the trunk port which is used to connect switches to each other 802.1Q capable switches.

On an access port, if a broadcast is received unlike a normal switch which would broadcast to every port except the one it received the broadcast from, a VLAN capable port needs to strip off the VLAN field from the frame as the devices receiving the broadcast may not be VLAN capable and won't know how to process the VLAN tag. The broadcast will also be sent to the trunk ports. So VLAN broadcast frames get forwarded either to all access ports with VLAN tag stripped or to the trunk port with the VLAN tag intact. Unicast is either sent directly to a device on that switch or a broadcast is done if the device is not on that switch. Note that when sending on a trunk port the VLAN tag is intact so you are sending to the specific VLAN on the other switch. Thus you have virtual networks.

Devices on a VLAN cannot communicate to other VLANs without a Layer 3 router between them.

VLANs allow you to create separate Level 2 networks with isolated traffic. These separate networks are individual broadcast domains.

# SSL and TLS

SSL = Secure Socket Layer
TLS = Transport Layer Security (newer more secure version of SSL)


# Border Gateway Protocol (BGP)

An Autonomous System (AS) from perspective of BGP is a "black box".  An AS is controlled by a single entity and BGP has no idea what goes on inside an AS.

Each AS is assigned a number between 0 and 65535 by IANA. Of that range 64512 to 65534 is for private use and can be used without needing an allocation for private peering. That number is the Autonomous System Number **ASN**. ASNs are how BGP identifies different networks.

BGP is designed to be reliable and distributed. It operates on TCP over port 179.

BGP is NOT automatic. You need to connect two Autonomous Systems manually for them to start sharing network information with each other.  An AS will learn network details from its peers and any information it has it will share with its peers. All the core networks of the internet are sharing topology information with each other.

BGP is a path-vector protocol. It exchanges the best path to a destination between peers. This path is called the **ASPATH**. This just factors in number of hops. It does not take into account link speed or condition.

**iBGP** = Internal BGP - Routine within an AS

**eBGP** = External BGP - Routing between AS's

# Jumbo Frames and MTU

An ethernet frame has overhead (the header) and the payload.  A normal payload is up to 1500 bytes but a Jumbo Frame is 9000 bytes.

The reason you may want to utilize Jumbo Frames is if you want a lower ration of overhead to data being sent. You will also send fewer frames using Jumbo Frames and that may speed things up.

You need to make sure that everything in the path supports Jumbo frames or you wind up with Fragmentation.

In AWS you have these limitations:

* Traffic outside of a single VPC does not support Jumbo Frames
* Traffic over an inter region VPC peering connection does not support Jumbo Frames (same region peering does support it)
* Traffic over VPN connections does not support Jumbo Frames
* Traffic over an Internet Gateway does not support Jumbo Frames
* Direct Connect DOES support Jumbo Frames
* Transit Gateway can support up to 8500 bytes (larger than usual but not full Jumbo Frame)

# Layer 7 Firewalls

A Layer 7 Firewall will understand Layer 7 protocols (such as HTTP) and be able to inspect/operate on them and thus protect agains protocol specific attacks. All sorts of logic can be placed here such as spam filtering TLS termination etc.

# IPSEC and VPN Fundamentals

IPSEC is a group of protocols used to set up secure tunnels across insecure networks between two peers (local and remote).

Any data carries over IPSEC tunnel is encrypted and thus cannot be viewed and can't be modified without detection.

Similar to TLS you start with sharing an encryption key via asymmetric encryption to establish the tunnel and once established the shared symmetric key is used to encrypt and decrypt.

IPSEC has two main phases:

* IKE Phase 1 - The slow part where you authenticate via a password or a certificate and establish the shared symmetric encryption key. At the end of phase 1 you have a phase 1 tunnel and work of establishing a symmetric key is done.
* IKE Phase 2 - Uses keys established in phase 1 for bulk data transfer. Creates an IPSEC SA phase 2 tunnel (architecturally running over phase 1)

There are two types of VPNs; policy based VPNs have rules to figure out what if "interesting traffic" that needs to be sent over a tunnel or route based where target matching is used to figure out what should go over a tunnel.

# Encryption Basics

## Asymmetric Encryption

* Both parties agree on asymmetric algorithm to use.
*  Both parties have their own public and private key.
* Public key cannot be used to decrypt data. Only the private key can decrypt data which was encrypted with the public key.
	* public key is used for encryption only.
* Sender of data can take the public key, encrypt the data and send it and only the holder of the private key can decrypt that data.

Asymmetric encryption is computationally expensive so typically you will use it to agree on a symmetric key and use that to send data.

## Signing

Signing is when you use your private key to "sign" a message. The receiver of the message can use the senders public key to verify that the message was signed by the holder of the private key. This does not reveal the private key only validates that holder of private key was the one that encrypted the message.

## Steganography

With encryption if you encrypt something is is known that it is encrypted data and that you encrypted it. Steganography is used to obfuscate that you are the one that encrypted the data. This is typically done by hiding your encrypted data in another piece of data.

## Digital Signatures

Digital signatures verify the integrity of the data (the what) and authenticity of the data (the who). This lets you download some data from a specific entity and make sure that the data has not been tampered with and that the entity is the one that created the data.

Layered on top of normal usage so if you don't care about verifying you can just use the data.

* Hash of the data is taken, original data remains unaltered (integrity)
	* This allows normal usage of data without worrying about hashing or keys.
* Digitally sign the hash (using private key). This authenticates the hash.
* Public key can be widely distributed.
* hash cannot be changed as nobody else has the private key.
* Document cannot be changed as this invalidates the HASH
* trust public key -> trust private key -> trust entity -> trust data
	* this is the chain of trust

Receiver will use signature and public key to get back the original hash. Receiver can then use the same hashing algorithm to hash the documents and if the hashes match then the document can be trusted.

# DNS

## DNS Terms

* **DNS Zone** - Think of a DNS zone as a database. `netflix.com` for example is a DNS Zone. Inside that zone are DNS records such as `www.netflix.com`.
* DNS Zone information is stored in a file somewhere. That file is called a **Zone File**
* **Name Server (NS)** is a DNS server that hosts one or more DNS Zones and stores one or more Zone Files.
* **Authoritative** record means that it contains real/genuine records. The "boss" for the record.
* **Non-Authoritative/cached** DNS records are copies of records/zones stored elsewhere to speed things up. Your router or another DNS host may be able to provide the DNS record for google.com for example because you visited it before. There expire baed on the TTL setting.

## DNS Architecture


DNS Starts with the **DNS Root**. This is a DNS server just like any other but every DNS client knows about and trusts the root. There are 13 root server IP addresses distributed geographically and managed by various organizations. ICAN for example operates one of the IP addresses. In reality each of these IP addresses is backed by many servers using any-cast IP addresses. Managed of the Root Zone is done by IANA.

#TODO What is an any-cast IP address?

Root Zone only stores high level information on Top Level Domains (TLDs). Thera are two types of TLDs; generic ones such as `.com` and country specific ones such as `.uk` or `.us`. IANA delegates the management of these TLDs to other organizations known as **Registries**. Thus the job of the Root Zone is to just point at these TLD registries. So for example Verisign controls the `.com` Zone; so in the Root Zone there is an entry that points at the name-servers that point at Verisign servers. 

Obviously TLDs only store records for domains in that TLD. So the `.com` TLD will have a record for `netflix.com` but not for `www.netflix.com` or other records in `netflix`. Those records wold be in the `netflix` Zone. In this example the name servers for `netflix.com` would be **authoritative for the domain** 

## What happens during a DNS query

When we query from our local computer for `www.netflix.com` ...

1. Check local cache and hosts file. If we don't have a record there we go to step 2.
2. Query a DNS Resolver.  A DNS resolver is a type of DNS server often running on your home router or ISP that will do the query on our behalf. The resolver has a local cache and may be able to return a non authoritative answer from there. Assuming you don't have it there you go to step 3.
3. DNS resolver will query the Root Zone via one of the 13 IP addresses. There are hard coded and updated by the operating system vendor. The root server will reply with the `.com` TLD name servers which gets us one step closer.
4. Resolver will now query the `.com` name servers with the query `www.netflix.com` and they will delegate to the `netflix.com` name servers.
5. Since `netflix.com` name server are authoritative for `netflix.com` they provide the answer to the DNS Resolver. 
6. The DNS Resolver will cache the result and return it to the client which in this case is our machine.

Every query to a name server in the chain moves you one stop closer to your answer. This is called "walking the tree"

At the end of walking the tree you may not necessarily get an IP address. You may get another name via a CNAME entry and need to resolve that name.

## What happens when a domain is registered

When you register a domain there are a few key entities involved:

* The person registering the domain
* The Domain Registrar (such as Route53 or Hover). Registrar has one function; to let you purchase domains. To allow this they have a relationship with the registry for many top level domains.
* The DNS Hosting Provider (Route53 and Hover are also examples of these) - operates DNS Zones and allows you to manage the content of those Zones.
* The TLD Registry (Verisign if you are using .com for example)

These entities may be a bit confusing because a lot of organizations such as Route53 provide multiple of these services.

1. If the domain you want is available you par for the domain via the Registrar. Now you need a DNS Zone for the domain being registered and the Zone needs to be hosted on some name server. 
2. If the DNS Hosting provider is the same as the Registrar the hosted zone is created automatically. If its a different company you will be asked for the information of where the name server is hosted. 
3. Registrar communicates where your hosted zone for the domain is to the Registry for the TLD and those details are added to the TLD Zone.

## DNS Sec

## Benefits of DNSSEC

* Data Origin Authentication - Allows you to verify that the data you receive is from the Zone you expect. Also if you get cached results are they really from that zone.
* Data Integrity Protection - ensures data has not been modified in transit or since it was created by the administrator of the zone.

DNS uses public key cryptography to secure itself similar to how https works with certificates. 

DNS Sec adds to DNS it does not replace it. If a device is not DNS Sec capable it will make a normal DNS query and get back normal DNS results. A device the is DNS Sec capable will make DNS Sec requests get DNS Sec results and will be able to verify them.

In a compromised environment regular DNS has no way on knowing that you got a compromised results.

DNS Sec cannot correct anything. It can just verify.

## How DNSSEC works within a zone

If you want to try a DNS sec request you can use the command `dig www.ican.org +dnssec`

Below is a sample output. Notice the `RSIG` This is a DNS Sec resource type that is a digital signature which can be used to validate results.

```
; <<>> DiG 9.10.6 <<>> www.icann.org +dnssec
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 63376
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags: do; udp: 1232
;; QUESTION SECTION:
;www.icann.org.			IN	A

;; ANSWER SECTION:
www.icann.org.		2955	IN	CNAME	www.icann.org.cdn.cloudflare.net.
www.icann.org.		2955	IN	RRSIG	CNAME 13 3 3600 20250224035717 20250202204237 12050 icann.org. fJ1GpbIi6RcT6W3AnhjyPOCZwaKOPN9+ehyrCTlZnh9icF5huveEHjYJ pgELO1LJAociKrS+z3/X3PY2KDTpLQ==
www.icann.org.cdn.cloudflare.net. 300 IN A	104.18.3.93
www.icann.org.cdn.cloudflare.net. 300 IN A	104.18.2.93
www.icann.org.cdn.cloudflare.net. 300 IN RRSIG	A 13 6 300 20250210131436 20250208111436 34505 cloudflare.net. H0RLPKECmFaCSrpXJ/q53pV0dX4SmCHFB12zmA0uk6h0uEoKAQMN+jTp tF5pF7MPlEEsKew1oRxJT6bpz2unoA==

;; Query time: 15 msec
;; SERVER: 192.168.50.1#53(192.168.50.1)
;; WHEN: Sun Feb 09 04:14:36 PST 2025
;; MSG SIZE  rcvd: 335
```

An **RRSET** in DNSSEC is a Resource Record Set. It lets you group related records together into a set. Fore example the CNAME www.ican.org the A record for that CNAME entry and the AAAA record for that CNAME entry can be group into a single RRSET. Similarly the MX records for a domain can be group into a single RRSET. Within an RRSET the names an types of records will be the same. 

DNSSEC does not validate individual records but instead validates RRSETs.

RRSETs are digitally singed using a public and private pari of keys knows as **Zone Signing Key (ZSK)** This key is not stored in the Zone but kept offline. 

An RRSET is taken and encrypted with the private key creating the RRSIG. The RRSIG generated is stores in the Zone with the same record name, but the type RRSIG. When a regular DNS client make a request they will get the RRSET as the response but DNSSEC clients will get both the RRSET and the RRSIG. Note that if the RRSET changes the RRSIG needs to be updated to be valid. If the RRSET is changed without the updated signature the result is invalid. Thus you can tell that something was changed without the approval of the private key holder. (Only the private part of the key pair can be used for the signature)

Inside a DNSSEC Zone will be a DNSKEY record which is used to share/distribute the public portion of the Zone Signing Key (ZSK). DNSKEY record can store two types of Keys a Zone Signing Key and a Key Signing Key. Each has a different flag value 256 means its a ZSK and 257 means KSK. 

A DNSSEC resolver can take the value of the RRSET (the plain text part) and the RRSIG and use the DNSKEY record to verify that the RRSET value is authentic.

This all assumes that we trust the DNSKEY. To establish a chain of trust to the DNS root you have an RRSIG for the DNSKEY itself. This is the Key Signing Key (KSK). The Key Signing Key is reference from the parents zone.  So when you sign your public ZSK you use the public portion of your KSK. The private portion of the KSK is trusted by the parents zone (similar to certificate authority). This establishes a chain of trust as well as lets a Zone rotate their ZSKs frequently without involving the parent zone.

## More on the chain of trust

The parent zone needs to be able to explicitly state that it trusts the child zone. This is done using a **Delegated Signer (DS)** record set. This stores a hash of the public portion of the child KSK in the parent Zone. Since the hash is unique and one way adding this record shows that the parent zone trusts the child zones KSK. Because we need a way to validate the DS record there is a corresponding RRSIG made using the parent zones private key.  Just like the the child zone the parent zone has DNSKEY records to share the public portion of its ZSK and KSK keys which are used to validate the RRSIGs in this Zone. This trust delegation continued all the way up to the root zone.

The root zone is explicitly trusted because there is not parent to verify it. This trust is accomplished via the root signing ceremony.  The private DNS Root KSK is locked away in 2 locations one in California and one in Virginia. Trusting these keys is encoded into all DNSSEC clients. These are locked away on HSMs (Hardware Security Modules) and never exposed.  To get to the HSM you need to go though very strict security and verification. We all know the public part of this key. This key is the **Trust Anchor** . Because the Trust Anchor is hard to access the root zone has its own ZSK. This ZSK is generated in the [signing ceremony](https://www.cloudflare.com/dns/dnssec/root-signing-ceremony) you access the Trust Anchor to produce the root zone RRSIG DNS key. This process happens every 3 months and is publicly audited.

[CloudFlare Write Up on how DNSSEC works](https://www.cloudflare.com/learning/dns/dnssec/how-dnssec-works/)
































