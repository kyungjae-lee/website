[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Networks</a> > Network Devices

# Network Devices



Layer 1 - Physical Layer

* Hub

Layer 2 - Data Link Layer

* Switch
* Bridge

Layer 3 - Network Layer

* Router



## Layer 1 - Physical Layer

### Hub

- A **hub** (A.K.A. Network Hub) works at the **Physical Layer (Layer 1)** of the OSI model.
- Used to set up LAN
- Has multiple ports    
  - If more hosts than the ports? $\to$ Connect another hub and extend the network as necessary.

- Star topology
- When a packet arrives at one port, it is copied to all the other ports so that all segments of the LAN can see all packets.    
  - Every data the hub receives will be **broadcasted to all the other nodes** in the network. (Drawback of the hub)
- Computer (or other device) and the hub will be connected using *Ethernet Straight Through Cable*.

- Pros and Cons

  Pros:

  - Cheaper than switches
  - Works good for smaller network

  Cons:

  - Issues with broadcast            

    If the network gets bigger, broadcast issues will get even worse.

  - No memory (Not intelligent)            

    Hubs will simply broadcast everything it receives, but will not store any information since there’s no memory in them.

  - Normally runs in hafl-duplex mode.            

    This is why switches (full-fuplex) are dominating.

### Repeater

- The data signals generally become too weak or corrupted if they travel a long distance.

- Repeater regenerates the signal over the same network. It does not amplify the signal. (Does not increase the volume)

- Operates at the Physical Layer (Layer 1).

- Has only two ports.

- Use case:    

  When two different buildings want to construct a same LAN, bridge can help since the signal generated from the source host in building A needs to reach the destination host in building B without loosing its signal.



## Layer 2 - Data Link Layer

### Switch

- A switch is a Data Link Layer (Layer 2) networking device that connects devices on a computer network to establish a Local Area Network (LAN).    

  Switches connect devices that are belonging to the **same** network.

- A switch is a mechanism that allows us to interconnect links to form a larger network.

- A switch is a **multi-input**, **multi-output** device that transfers packets from an input to one or more outputs.

- The core job of a switch is to take packets that arrive on an input and **forward** (or **switch** them to the right output so that they will reach their appropriate destination.

- Unlike hub, switch has memory stores **MAC Addresses Table** in its memory.

  ```plain
  MAC ADDRESS           INTERFACE / PORT
  ====================  ================
  AA-AA-AA-AA-AA-AA     1
  BB-BB-BB-BB-BB-BB     19
  ```

  > When `Host-A (AA-AA-AA-AA-AA-AA)` that is connected to the switch port 1 wants  to send a data to `Host-B (BB-BB-BB-BB-BB-BB)` that is connected to the switch port 19, switch consults its MAC Address Table and **forwards the data** it received  from `Host-A` exactly to `Host-B`. (It does not simply broadcast every time like hubs do.)

  Don’t be confused! Switches are capable of **unicasting**, **multicasting**, and **broadcasting**.

* Hub vs. Switch

  | Hub                                    | Switch                                        |
  | -------------------------------------- | --------------------------------------------- |
  | Layer 1 device (Physical Layer)        | Layer 2 device (Data Link Layer)              |
  | Has NO memory                          | Has memory and stores MAC Address Table       |
  | NOT intelligent                        | Intelligent                                   |
  | Floods the network due to broadcasting | Can do unicasting, multicasting, broadcasting |
  | High security risks                    | Low security risks                            |
  | Less efficient                         | More efficient                                |
  | Half duplex                            | Full Duplex (Tx/Rx at the same time)          |

### Bridge

- Bridge = Repeater + (Functionality of reading MAC address)
- Also used for interconnecting two LANs on the **same** protocol.
- Has only two ports like repeaters.

- Two types of bridges:    

  * **Transparent Bridges**

    The bridge in which the stations (or hosts) are completely unaware of the bridge’s existence. Meaning, it’s not necessary to do any configurations in the stations            

    - For a router, we give the IP address of the interface as the default gateway for all the stations or PCs. (This is not necessary in bridges.)

    Reconfiguration of the stations is unnecessary even if bridge is added or removed from the network.

  * **Source Routing Bridges**

    Routing operation is performed by the source station (or host) and the frame  specifies which route to follow.

    - Source has the routing information that designates how to get to the destination.

- Bridge vs. Router 

  - A bridge interconnects LANs that are running in the same protocol and it is a Layer 2 device, it deals with the MAC addresses only.
  - A router interconnects two different LANs that have two different protocols and it is a Layer 3 device, it deals with the IP addresses.



## Layer 3 - Network Layer

### Router

- A router is a Network Layer (Layer 3) networking device that forwards data packets between computer networks.
  - A router connects two or more **different** Local Area Networks (LANs).
  - One interface is part of one LAN, another interface is part of another LAN.
- A router is connected to at least two networks, commonly two LANs or WANs, or a LAN and its ISP’s network (i.e., Internet).
- Has memory and stores **Routing Table** in its memory
- **Default gateway** is the IP address of the first router that the packet to be sent out to the Internet is going to hit.

* Switch vs. Router

  | Switch                                       | Router                                      |
  | -------------------------------------------- | ------------------------------------------- |
  | Connect many devices together within a LAN   | Connects a LAN to other LANs                |
  | Layer 2 device (Data Link Layer)             | Layer 3 device (Network Layer)              |
  | Has memory and stores MAC Address Table      | Has memory and stores Routing Table         |
  | Forwarding decisions are made on MAC address | Forwarding decisions are made on IP address |
  | Full Duplex                                  | Full Duplex                                 |
  | LAN                                          | LAN, MAN, and WAN                           |
