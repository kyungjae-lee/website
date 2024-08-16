[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Networks</a> > Introduction to Computer Networks

# Introduction to Computer Networks



## What is a Network?

- In its simplest form, a network is nothing more than “**two connected computers sharing resources with one another**.”

- Technically speaking, it is composed of two main aspects:    
  - **Physical Connection** (wires, cables, wireless media)
  - **Logical Connection** (data transporting across the physical media)



## Some Basic Networking Rules

- The computers in a network must use the same procedures for sending and receiving data. In computer networks, we call these **communication protocols**.

- Data must be delivered uncorrupted. If it is corrupted, it is useless.
   (There are exceptions; e.g., phone calls where it is more important to have noisy connection than connection fail. So it depends on the types of communication.)

- Computers in a network must be capable of determining the **origin** and **destination** of a piece of information, i.e., it’s **IP** and **Mac Address**.



## Types of Computer Networks (by Size)

- **Personal Area Network (PAN)**

  Ultra-small networks used for personal use to share data from one device to another.

  Examples:        

  - Smartphone to laptop
  - Smartwatch to smartphone
  - Smartphone hands free car calling
  - Heart rate monitor to smartphone

- **Local Area Network (LAN)**

  A computer network within a small geographical area, such as a single room, building or group of buildings.

  Examples:        

  - Home network
  - Small office network

- **Wireless Local Area Network (WLAN)**

  A LAN that’s fully dependent on wireless connectivity.

  Examples:        

  - Most home networks are WLANs.            
    - e.g., Personal computers, smartphones, tablets wirelessly connected to home LAN.

- **Campus Area Network (CAN)**

  A computer network of multiple interconnected LANs in a limited geographical area, such as a corporate business park, government agency, or university campus.

- **Metropolitan Area Network (MAN)**

  A computer network that interconnects users with computer resources in a city.

  Larger than a CAN, but smaller than a WAN.

- **Wide Area Network (WAN)**

  A computer network that extends over a large geographical distance, typically multiple cities and countries.

  Examples:        

  - The “Internet”
  - Google offices in different states



## Network Architecture

- **Peer-to-Peer (Decentralized Architecture)**
  - All computers on the network are **peers**.        
    - No dedicated servers
    - There’s no centralized control over shared resources (no dedicated servers)
  - Any device can share its resources as it pleases
  - All computers can act as either a client or a server
  - Easy to setup, and common in homes and small business
  - Common in homes and small businesses
- **Client-Server (Centralized Architecture)**
  - The network is composed of **clients** and **servers**.        
    - Servers provide resources
    - Clients receive resources
  - Servers provide centralized control over network resources (files, printers, etc.)
  - Centralizes user accounts, security, and access controls to simplify network administration        
    - A huge benefit of the client-server architecture
  - More difficult to setup and requires an IT administrator
  - Common in medium to large businesses



## Why Build a Computer Network?

- Before computer networks, people sent and received information by hand, using the postal service. This is **slow** and can be **unreliable**.
- Computer networks enable faster, more efficient modes of communication, i.e., email, video conferencing, etc.
- Computer networks and the sharing of electronic data encourage the use of standard policies and precedures.
- Computer networks provide backup and recovery support for our data, i.e., redundancy.
- Computer networks lead to cost savings.



## Computer Networking Protocols

- Computers communicate with each other with **network protocols**.
- Protocols are **rules governing how machines exchange data and enable effective communication**.
- Everyday examples of protocols:    
  - When you call somebody, you pick up the phone, ensure there is a dial tone, and if there is, you dial the number.
  - When you drive your car, you obey the rules of the road.
- Protocols define the interfaces between the layers in the same system and with the layers of peer system.
- Protocols are **building blocks** of a network architecture.

- **Physical protocols** describe the medium (wiring), the connections (RJ-45 port), and the signal (voltage level on a wire).
- **Logical protocols** are pieces of software controlling how and when data is sent and received to computers, supporting physical protocols.

- Computer networks depend on many different types of protocols in order to work properly.
- Example of common TCP/IP suite of protocols:    
  - Web communication: HTTP
  - E-mail: POP3, SMTP, IMAP
  - File transfers: FTP



## Duplex Communication (Half vs. Full Duplex Communication)

- Network communication will occur in either full or half duplex mode:    

  - **Half Duplex**

    Can send and receive data, but not at the same time.

  - **Full Duplex**

    Can send and receive data simultaneously.



## Network Transmission Types

- The general way in which we can communicate from one system to the other systems on a network.    

  - **Unicast (One-to-One)**

  - **Multicast (One-to-Many)**        

    Communicating within the Multicasting Group

  - **Broadcast (One-to-All)**        

    Used very commonly on the Local Area Network (LAN)