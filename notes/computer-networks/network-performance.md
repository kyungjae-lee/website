[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Networks</a> > Network Performance

# Network Performance



One important issue in networking is the performance of the network − how good is it?

Network performance is measured in following fundamental ways:

* Bandwidth
* Throughput
* Latency
* Jitter



## Bandwidth (Capability)

* Maximum amount of data that can be transmitted per second (capability)

* The bandwidth of a network is given by the number of bits that can be transmitted over the network in a certain period of time.

- Bandwidth for wired network (in **bps**)    

  e.g., Gigabit Ethernet can provide a bandwidth of 1Gbps.

- Bandwidth for wireless network (in **Hertz**)    

  A range of frequencies used to transmit signals which is measured in hertz.



## Throughput (Reality)

- Actual amount of data that passes through the medium (reality)

- The throughput is a measure of how fast we can actually send data through a network.

- Throughput vs. Bandwidth

  A link may have a bandwidth of B bps, but we can only send T bps through this link. (Always T < B)

* We may have a link with a bandwidth of 1 Mbps, but the devices connected to the end of the link may handle only 200 Kbps. This means that we cannot send more than 200 Kbps through this link.



## Latency (Delay)

* The latency or delay defines how long it takes for an entire message to completely arrive at the destination from the time the first bit is sent out from the source (to the time the last bit is consumed by the destination).

* Latency is made of 4 components:    

  1. Transmission delay
  2. Propagation delay
  3. Queuing delay
  4. Processing delay

  Latency = Transmission delay + Propagation delay + Queuing delay + Processing delay



## Jitter

* Jitter refers to inter-packet gap, i.e., variances in latencies between packets.

* Increases or shrinks as packets travel through the network (devices)

  Some packets may be sped up, some may be slowed down compared to their initial transmit rate.

* Types of applications that are susceptible to jitters:

  - Real-time applications such as VoIP, video streaming, conference call, etc.

  - This is because it can’t buffer much and if there’s a jitter, it can do only so much to reduce that inter-packet gap.

* Types of applications that are NOT susceptible to jitters:

  Non-real-time applications such as downloading a file, loading LinkedIn page, etc.

* DO NOT confuse jitter with bandwidth.

  High bandwidth connection can reduce jitter.

* Example:

  ```plain
  Case 1: 0, 1, 2, 3 ---> 20, 21, 22, 23    (Consistent delay: 20)
  Case 2: 0, 1, 2, 3 ---> 21, 23, 21, 28    (Inconsistent delays: 21, 22, 19, 25)
  ```

  > `Case 1` shows **low jitter** → **less variation** in delays `Case 2` shows **high jitter** → **greater variation** in delays.