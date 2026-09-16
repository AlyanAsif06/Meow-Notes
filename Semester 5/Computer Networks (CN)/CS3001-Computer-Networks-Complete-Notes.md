# CS 3001 — Computer Networks
## Complete Lecture Notes · Lectures 01–08 (Chapters 1 & 2)

**FAST NUCES Lahore · Fall 2026 · Instructor: Nauman Moazzam Hayat**
Textbook: Kurose & Ross, *Computer Networking: A Top-Down Approach*, 8th edition

---

### How to use these notes

- Notes follow **each deck's own index headings**, in lecture order.
- 🔢 marks a **fully worked numerical** — every step is shown and explained.
- 📐 marks a **formula**. Every lecture ends with a **consolidated formula summary table**.
- Diagrams are drawn in **text/ASCII form** so they survive copying, printing and phone screens.
- ⚠️ marks a **common exam trap**.

### Contents

| # | Lecture | Date | Main topics |
|---|---|---|---|
| 01 | Introduction | 18 Aug | Course admin, nuts-and-bolts vs services view, protocols, network edge |
| 02 | Access Networks & Switching | 20 Aug | Cable/DSL/wireless access, physical media, circuit vs packet switching, FDM/TDM |
| 03 | Internet Structure & Performance | 25 Aug | Network of networks, four delay components, traffic intensity, throughput, BDP |
| 04 | Security & Protocol Layers | 27 Aug | Malware, DoS, sniffing, spoofing, layering, encapsulation, OSI model |
| 05 | Application Layer Principles & HTTP | 01 Sep | Client-server vs P2P, sockets & ports, TCP vs UDP, URLs, persistent vs non-persistent HTTP |
| 06 | HTTP Messages, Cookies & Caching | 03 Sep | Request/response format, methods, status codes, cookies, web caches, conditional GET |
| 07 | HTTP/2, E-mail & DNS | 08 Sep | HOL blocking, SMTP/IMAP, DNS hierarchy, iterated vs recursive, RR types |
| 08 | Video Streaming, CDNs & DASH | 10 Sep | Pixels & coding, CBR/VBR, DASH, CDNs, Netflix, socket timeouts |

---

# Lecture 01 — Introduction
**CS 3001 Computer Networks · 18 August 2026 · Chapter 1 begins**

---

## Index (as per the deck)

1. Course administration
2. Why study networking?
3. What is networking?
4. Chapter 1 roadmap
5. The Internet: a "nuts and bolts" view
6. The Internet: a "services" view
7. What's a protocol?
8. Network edge: client, server, peer

---

## 1. Course administration

**Prerequisites:** Digital Logic, Signals & Systems, Computer Organization, C/C++ programming, CS 218 (Data Structures) + CL 218.

**Textbook:** *Computer Networking: A Top-Down Approach*, 8th ed., Kurose & Ross.
**Reference:** Tanenbaum (*Computer Networks*, 5th ed.), Forouzan (*Data Communications and Networking*, 4th ed.).

**Structure:** 3+1 credit hours — two 1.5-hour lectures per week + one 3-hour lab per week (separate instructor).

**Weightage:**

| Component | Count | Weight |
|---|---|---|
| Assignments | 6 | 10% |
| Quizzes | 6 (best 5 counted) | 15% |
| Midterms / Sessionals | 2 | 30% (15% + 15%) |
| Final Exam | 1 | 45% |

**Policies that actually matter:**
- Absolute grading, as per department policy for core courses.
- No retakes of quizzes or exams. Mid/final emergency relief only case-by-case with department approval (percentage of one awarded for the other).
- Deadlines are hard for both class and lab.
- Quizzes may be announced **or unannounced**.
- 80% attendance is mandatory to avoid disqualification.
- Course outline may shift 10–20% during the semester.
- Plagiarism → F grade in the course, or Disciplinary Committee.

---

## 2. Why study networking?

- Hard to name any area of CS that has produced more tangible change for the average person in the last 25 years.
- It is **"the plumbing of computing"** — invisible infrastructure everything else sits on.
- Devices are growing faster than both the human population and the number of Internet users. Growth is driven heavily by **M2M (machine-to-machine)** applications: video surveillance, health monitoring, traffic monitoring, smart meters, asset/package tracking.

---

## 3. What is networking?

Networking is not one thing. The deck walks through examples to build intuition:

- **Web-scale services:** World-Wide Web, Gmail, Facebook, Snapchat, Dropbox
- **Real-time communication:** Skype, FaceTime
- **Streaming:** Netflix, YouTube
- **Peer-to-peer:** Napster, BitTorrent
- **Distributed consensus:** Bitcoin
- **Gaming:** Fortnite Battle Royale
- **The Internet itself**
- **And the underlying technology:** Wi-Fi, LTE, SDN, BGP, MIMO, mesh networking, full-duplex, sensor networks, medical devices, datacenter networks, undersea cables, deep-space links

**End systems** span a huge range of devices: car navigator, heart pacemaker, smartphone, iPad, Linux server, MAC laptop, Windows PC.

---

## 4. Chapter 1 roadmap

This is the index for all of Chapter 1 (Lectures 01–04):

1. What is the Internet? What is a protocol?
2. Network edge: hosts, access network, physical media
3. Network core: packet/circuit switching, internet structure
4. Performance: loss, delay, throughput
5. Protocol layers, service models
6. Security
7. History

**Chapter goal:** get the "feel" and "big picture" — terminology first, depth later.

---

## 5. The Internet: a "nuts and bolts" view

This view describes the Internet by what it is *physically made of*.

**Components:**

| Component | Description |
|---|---|
| **Hosts / end systems** | Billions of connected computing devices running network apps at the Internet's **edge** |
| **Packet switches** | Forward packets (chunks of data) — i.e. **routers** and **switches** |
| **Communication links** | Fiber, copper, radio, satellite. Characterised by **transmission rate = bandwidth** |
| **Networks** | A collection of devices, routers and links **managed by one organisation** |

### Diagram — the standard Internet picture (memorise this layout)

```
                      [ mobile network ]
                             |
                             |          ___________________________
   [ home network ]----\     |         (  national or global ISP   )
                        \    |          ---------------------------
                         \   |                     |
                          [ local or regional ISP ]
                         /   |          \
                        /    |           \
   [ enterprise network ]    |            [ content provider network ]
                             |                        |
                             |                  [ datacenter network ]
```

Read it as three tiers: **edge networks** (home, enterprise, mobile) attach to a **local/regional ISP**, which attaches upward to a **national or global ISP**. **Content provider networks** (Google, Netflix) hang off the side with their own datacenters.

### "Fun" Internet-connected devices (the IoT slide)

Amazon Echo, Internet refrigerator, IP picture frame, security camera, Slingbox (remote cable TV), gaming devices, Internet phones, Tweet-a-watt energy monitor, pacemaker & monitor, web-enabled toaster + weather forecaster, sensorised bed mattress, Fitbit, AR devices, smart diapers, bikes, cars, scooters.

### Internet as a "network of networks"

- The Internet is a **network of networks** — interconnected ISPs.
- **Protocols are everywhere**: they control sending and receiving of messages. Examples: HTTP (Web), streaming video, Skype, TCP, IP, WiFi, 4G/5G, Ethernet.
- **Internet standards** are set through:
  - **RFC** — Request for Comments (the document format)
  - **IETF** — Internet Engineering Task Force (the body)

---

## 6. The Internet: a "services" view

This view describes the Internet by what it *does for applications*.

- **Infrastructure that provides services to applications:** Web, streaming video, multimedia teleconferencing, email, games, e-commerce, social media, interconnected appliances.
- **Provides a programming interface to distributed applications:** "hooks" that let sending/receiving apps connect to and use the Internet's transport service.
- Offers **service options**, analogous to the postal service (ordinary mail vs registered vs express — different guarantees, different costs).

> **Exam framing:** "nuts and bolts" = what it's *built from*. "Services" = what it *offers*. Both describe the same Internet.

---

## 7. What's a protocol?

**Definition (memorise):**

> Protocols define the **format** and **order** of messages sent and received among network entities, and the **actions taken** on message transmission and receipt.

| Human protocols | Network protocols |
|---|---|
| "What's the time?" | Between computers/devices rather than humans |
| "I have a question" | **All** communication activity in the Internet is governed by protocols |
| Introductions | |

So a protocol is a set of rules for:
- which specific messages are sent,
- which specific actions are taken when a message is received or another event occurs.

### Diagram — human protocol vs TCP/HTTP protocol, side by side

```
   HUMAN                              COMPUTER NETWORK
   -----                              ----------------
   A ---- "Hi" --------> B            A --- TCP connection request ---> B
   A <--- "Hi" --------- B            A <-- TCP connection response --- B
   A -- "Got the time?"-> B           A --- GET http://gaia.cs.umass.edu/
   A <--- "2:00" ------- B                          kurose_ross ------> B
                                      A <----------- <file> ----------- B
        |                                             |
        v  time                                       v  time
```

Both follow the same shape: **greeting → request → response**.

### Wikipedia definition (given verbatim in the deck)

The Internet is the global system of interconnected computer networks that uses the Internet protocol suite (TCP/IP) to link devices worldwide. It is a network of networks consisting of private, public, academic, business and government networks of local to global scope, linked by electronic, wireless and optical networking technologies. It carries a vast range of information resources and services — the inter-linked hypertext documents and applications of the World Wide Web, electronic mail, telephony and file sharing.

---

## 8. A closer look at Internet structure

The structure is built up in three layers of vocabulary:

1. **Network edge** — hosts: clients and servers; servers often in data centers.
2. **Access networks + physical media** — wired and wireless communication links that connect edge devices to the first router.
3. **Network core** — interconnected routers; the network of networks.

---

## 9. Network edge: client, server, peer

The network edge comprises the **millions and billions of end systems / hosts** and the applications that reside in them.

An end system (host) can either **request** service (client), **provide** service (server), or do **both interchangeably** (peer).

### Server
A service provider giving access to network resources.
- Can have multiple roles: web servers, mail servers, print servers, Remote Access Servers (RAS), Directory Servers (DNS), etc.
- **Always-on** host
- **Permanent IP address**
- Most servers reside in **large data centres**

### Client
A requestor of these services.
- May be **intermittently on**
- May have a **dynamic IP address**
- Clients **do not communicate directly with each other**

### Peer
A peer-to-peer network has **no dedicated servers**. All hosts are equal — each both provides and requests service, i.e. has both client and server functionality.
- Not an always-on server
- Arbitrary end systems communicate **directly**
- Peers are intermittently connected and **change IP addresses**
- **Complex management**
- Examples: Skype, BitTorrent, Napster

### Diagram — the three models

```
  CLIENT-SERVER                      PEER-TO-PEER

   [client]   [client]                [peer]------[peer]
        \       /                        |  \      /  |
         \     /                         |   \    /   |
        [ SERVER ]                       |    \  /    |
     (always on, fixed IP,               |     \/     |
      in a data centre)                  |     /\     |
                                         |    /  \    |
   clients never talk                  [peer]------[peer]
   to each other directly           (every node is both
                                     client and server)
```

---

## Formula summary — Lecture 01

There are **no numerical formulas** in this lecture; it is entirely conceptual. The quantitative material begins in Lecture 02.

The only "definitional equation" to carry forward:

| Term | Meaning |
|---|---|
| Transmission rate = bandwidth | Rate at which a link pushes bits out, measured in **bits per second (bps)** |
| Internet | Network of networks = interconnected ISPs |
| Protocol | format + order of messages + actions taken on transmission/receipt |

---

## Key takeaways

- The Internet can be described two ways: **nuts and bolts** (hosts, packet switches, links, networks) and **services** (an infrastructure + programming interface for distributed apps).
- Everything in the Internet is governed by **protocols**; standards come via **RFCs** from the **IETF**.
- Three structural layers of vocabulary: **edge** → **access network** → **core**.
- At the edge, a host is a **client**, a **server**, or a **peer** — and the distinguishing features are always: always-on?, IP address permanent or dynamic?, does it talk directly to its own kind?
# Lecture 02 — Access Networks, Physical Media, Network Core
**CS 3001 Computer Networks · 20 August 2026 · Chapter 1**

---

## Index (as per the deck)

1. Network edge recap (client, server, peer)
2. Access networks and physical media
   - Cable-based access (HFC)
   - Digital Subscriber Line (DSL)
   - Home networks
   - Wireless access networks
   - Enterprise networks
   - Data center networks
3. Links: physical media (twisted pair, coax, fiber, radio)
4. Switching — circuit vs packet
5. Host: sending packets — the transmission delay formula
6. The network core: forwarding and routing
7. Packet switching: store-and-forward, queueing and loss
8. Circuit switching: FDM and TDM
9. Numerical example — circuit-switched file transfer
10. Packet switching vs circuit switching

---

## 1. Network edge recap

Repeated from Lecture 01 — see **Lecture 01 §9** for the full client / server / peer breakdown. One-line recall:

- **Server** — always on, permanent IP, in a data centre, provides service.
- **Client** — intermittently on, dynamic IP, requests service, never talks to another client directly.
- **Peer** — both at once; intermittently connected, changing IPs, complex to manage (Skype, BitTorrent, Napster).

---

## 2. Access networks and physical media

**The question this section answers:** *How do you connect an end system to its edge router?*

Three categories:
- **Residential** access networks
- **Institutional** access networks (school, company)
- **Mobile** access networks (WiFi, 4G/5G)

---

### 2.1 Cable-based access (HFC)

Uses the TV cable infrastructure. Data and TV are transmitted at **different frequencies over the same shared cable**.

**Key mechanism — Frequency Division Multiplexing (FDM):** different channels are transmitted in different frequency bands on one physical cable.

```
Channel:   1   2   3   4   5   6   7   8   9
         [VID][VID][VID][VID][VID][VID][DATA][DATA][CONTROL]
         <------------- one shared coax cable ------------->
              (each channel = its own frequency band)
```

### Diagram — cable access path

```
  [ home ]                                           [ cable headend ]
     |                                                      |
  [ modem ]---[ cable splitter ]===== shared coax =====> [ CMTS ] --> ISP
                                  (fiber + coax = HFC)   (Cable Modem
                                                       Termination System)
```

**Properties:**
- **HFC = Hybrid Fiber Coax** — a network of cable and fiber attaches homes to the ISP router.
- **Asymmetric** rates:
  - Downstream: up to **40 Mbps – 1.2 Gbps**
  - Upstream: **30–100 Mbps**
- Homes **share** the access network up to the cable headend. (This is why your speed drops when the neighbourhood is streaming.)

---

### 2.2 Digital Subscriber Line (DSL)

Uses the **existing telephone line** to the telephone company's **central office**.

### Diagram — DSL access path

```
  [ home ]                                          [ central office ]
     |                                                     |
  [ modem ]---[ DSL splitter ]=== dedicated line ===> [ DSLAM ] --+--> Internet (data)
                                                                  |
                                   voice, data at different       +--> telephone network (voice)
                                   frequencies on one line
```

- **DSLAM** = DSL Access Multiplexer. It splits the traffic: data over the DSL phone line goes to the Internet; voice goes to the telephone network.
- **Dedicated** line to the central office (contrast with cable, which is shared).
- Downstream: **24–52 Mbps** (dedicated)
- Upstream: **3.5–16 Mbps** (dedicated)

> **Cable vs DSL — the one-line exam answer:** cable is **shared** and **faster**; DSL is **dedicated** and **slower**.

---

### 2.3 Home networks

```
  Wireless devices          Wired devices
        \\                       ||
      ((WiFi AP))            [ Ethernet ]
       54/450 Mbps             1 Gbps
            \                   /
             \                 /
          [ router + firewall + NAT ]
                     |
          [ cable or DSL modem ]  <-- often all combined in a single box
                     |
          to/from headend or central office
```

The access point, router/firewall/NAT, and modem are **often combined in a single box** (your home "router").

---

### 2.4 Wireless access networks

A **shared** wireless access network connects the end system to a router via a **base station**, a.k.a. an **access point**.

| | Wireless LAN (WLAN) | Wide-area cellular |
|---|---|---|
| Range | Within/around a building (~100 ft) | Provided by a cellular operator, tens of km |
| Standard | 802.11 b/g/n (WiFi) | 4G / 5G |
| Rate | 11, 54, 450 Mbps | 10's of Mbps |

---

### 2.5 Enterprise networks

```
   [ mail server ]  [ web server ]
          \             /
        [ Ethernet switch ]
                |
      [ institutional router ]
                |
        Enterprise link to ISP (Internet)
```

- Companies, universities, etc.
- Mix of **wired and wireless** link technologies, connecting a mix of **switches and routers**.
- Ethernet: wired access at **100 Mbps, 1 Gbps, 10 Gbps**
- WiFi: wireless APs at **11, 54, 450 Mbps**

---

### 2.6 Data center networks

- **High-bandwidth links (10s to 100s of Gbps)** connect hundreds to thousands of servers together, and to the Internet.

---

## 3. Links: physical media

**Definitions:**
- **bit** — propagates between transmitter/receiver pairs
- **physical link** — what lies between transmitter and receiver
- **guided media** — signals propagate in a **solid** medium: copper, fiber, coax
- **unguided media** — signals propagate **freely**, e.g. radio

### 3.1 Twisted Pair (TP)
- Two insulated copper wires
- **Category 5:** 100 Mbps, 1 Gbps Ethernet
- **Category 6:** 10 Gbps Ethernet

### 3.2 Coaxial cable
- Two **concentric** copper conductors
- **Bidirectional**
- **Broadband:** multiple frequency channels on one cable, 100's of Mbps **per channel**

### 3.3 Fiber optic cable
- Glass fiber carrying **light pulses — each pulse is a bit**
- High speed: point-to-point transmission at **10's–100's of Gbps**
- **Low error rate:** repeaters can be spaced far apart; immune to electromagnetic noise

### 3.4 Wireless radio
- Signal carried in various **bands** of the electromagnetic spectrum
- No physical wire
- **Broadcast**, and **half-duplex** (sender to receiver)
- Propagation environment effects: **reflection**, **obstruction by objects**, **interference/noise**

| Radio link type | Rate | Range / note |
|---|---|---|
| Wireless LAN (WiFi) | 10–100's Mbps | 10's of metres |
| Wide-area cellular (4G/5G) | 10's Mbps (4G) | ~10 km |
| Bluetooth | Limited rates | Short distance; cable replacement |
| Terrestrial microwave | 45 Mbps channels | Point-to-point |
| Satellite | < 100 Mbps downlink (Starlink) | **270 ms end-to-end delay** (geostationary) |

---

## 4. Switching

**Switching** = deciding the path data takes through a network — how it moves from one point to the next until it reaches its destination.

### 4.1 Circuit switching
A **dedicated path is set up and reserved for the entire conversation** *before* any actual data is sent. Traditional fixed-line telephone networks are circuit switched.

**The Bykea book analogy:** You want to send a book to a friend. The delivery service reserves a delivery vehicle *and the entire route* for the next one hour. The whole book travels in that reserved vehicle on the reserved route, in sequence. **No one else may use that vehicle or route during your booked hour — even if you are not sending anything at that moment.**

### 4.2 Packet switching
Data is **broken into smaller chunks (packets)**. Each packet finds its own way through the network, possibly via **different paths**, and is **reassembled** at the destination. **The Internet is packet switched.**

**Analogy:** Sending the same book by tearing out each page and sending them separately (some by Bykea, some by InDrive, different routes). Your friend reassembles the book when all pages arrive.

### 4.3 Comparison table (from the deck)

| | **Circuit switching** | **Packet switching** |
|---|---|---|
| **Setup** | Reserve a dedicated path first | No setup — just send |
| **How the book travels** | Whole, in one committed path | Torn into pages, sent separately, maybe via different paths |
| **Resource use** | Path is reserved even if you pause → **wastes capacity if idle** | Path is used only when data is actually flowing → **more efficient** |
| **Reassembly needed?** | **No** — arrives in order, all together | **Yes** — pages may arrive out of order and need reassembly |

> **The core idea:** circuit switching *reserves the whole road* just for your book before sending it. Packet switching *tosses individual pages into general traffic*, trusting they will each find their way and get sorted at the destination.

---

## 5. Host: sending packets — transmission delay

The host sending function:
1. Takes the application message
2. Breaks it into smaller chunks called **packets**, of length **L bits**
3. Transmits each packet into the access network at **transmission rate R**

**R** is called the *link transmission rate*, *link capacity*, or *link bandwidth* — the same thing under three names.

```
       two packets, L bits each
              [ 2 ][ 1 ]  ------>  link at rate R bits/sec
             [ host ]
```

### 📐 Formula

$$
d_{trans} = \frac{L\ (\text{bits})}{R\ (\text{bits/sec})}
$$

*"Packet transmission delay = time needed to push all L bits of the packet into the link."*

---

## 6. The network core

- A **mesh of interconnected routers**.
- **Packet-switching:** hosts break application-layer messages into packets; the network **forwards** packets from one router to the next, across links on the path from source to destination.

### Two key network-core functions

| **Forwarding** (a.k.a. "switching") | **Routing** |
|---|---|
| **Local** action | **Global** action |
| Moves arriving packets from a router's **input link** to the appropriate **output link** | Determines the **source-to-destination paths** taken by packets |
| Uses the **local forwarding table** | Uses **routing algorithms** |

### Diagram — forwarding table in one router

```
   destination address in arriving packet's header
                 |
                 v
        +------------------------+
        |  local forwarding table|
        | header value | out link|
        |    0100      |    3    |
        |    0101      |    2    |
        |    0111      |    2    |
        |    1001      |    1    |
        +------------------------+
                 |
   packet with header 0111 --> leaves on link 2
```

The **routing algorithm** (global) is what *fills in* this table. **Forwarding** (local) is what *uses* it, packet by packet.

---

## 7. Packet switching: store-and-forward

```
   L bits per packet
    [3][2][1]
   source ------ R bps ------ [router] ------ R bps ------ destination
```

**Two rules:**
1. **Packet transmission delay:** it takes **L/R** seconds to push an L-bit packet into a link at R bps.
2. **Store and forward:** the **entire packet must arrive at the router before it can be transmitted onto the next link.**

### 🔢 Worked numerical — one-hop transmission delay

**Given:** L = 10 Kbits, R = 100 Mbps.

**Step 1 — convert to base units.**
L = 10 Kbits = 10 × 10³ = 10,000 bits
R = 100 Mbps = 100 × 10⁶ = 100,000,000 bits/sec

*Why: the formula needs both quantities in raw bits and bits/sec, otherwise the prefixes don't cancel.*

**Step 2 — apply d_trans = L/R.**

$$
d_{trans} = \frac{10{,}000}{100{,}000{,}000} = 1 \times 10^{-4}\ \text{sec}
$$

**Step 3 — convert to a readable unit.**
1 × 10⁻⁴ s = 0.1 × 10⁻³ s = **0.1 msec**

**Answer: one-hop transmission delay = 0.1 msec.**

---

## 8. Packet switching: queueing and loss

```
     A ---- R = 100 Mb/s ----\                      /---- C
                              [ router ]-----------+
     B ---- R = 100 Mb/s ----/   |  R = 1.5 Mb/s    \---- D
                                 |                   \--- E
                          queue of packets waiting
                          for transmission on the
                          output link
```

**Why the queue forms:** A and B can each pump in at 100 Mb/s, but the output link only drains at 1.5 Mb/s. Work arrives faster than it can be serviced.

**Queueing occurs when work arrives faster than it can be serviced.**

If the arrival rate (bps) to a link **exceeds** the transmission rate (bps) of that link for some period of time:
- Packets will **queue**, waiting to be transmitted on the output link.
- Packets can be **dropped (lost)** if the memory (buffer) in the router fills up.

---

## 9. Circuit switching in the core

- End-to-end resources are **allocated to and reserved for** a "call" between source and destination.
- If each link in a diagram has four circuits, a call gets (say) the 2nd circuit on the top link and the 1st circuit on the right link.
- **Dedicated resources: no sharing** → circuit-like (**guaranteed**) performance.
- A circuit segment is **idle if not used by the call** — the capacity is wasted, not shared.
- Commonly used in traditional **telephone networks**.

---

## 10. Multiplexing: FDM and TDM

**Multiplexing** = sharing one physical link among multiple users/signals at the same time, so nobody needs their own dedicated physical link.

**Analogy:** one highway shared by many cars, instead of one separate highway per car.

### Frequency Division Multiplexing (FDM)
- Optical/electromagnetic frequencies are divided into **narrow frequency bands**.
- Each call gets **its own band** and can transmit at the **maximum rate of that narrow band**, continuously.

### Time Division Multiplexing (TDM)
- Time is divided into **slots**.
- Each call gets **periodic slot(s)** and transmits at the **maximum rate of the (wider) frequency band — but only during its time slot(s)**.

### Diagram — FDM vs TDM with 4 users

```
   FDM (4 users)                    TDM (4 users)
 f |====1====1====1====1===      f |####################
 r |====2====2====2====2===      r |# 1 | 2 | 3 | 4 | 1 #
 e |====3====3====3====3===      e |####################
 q |====4====4====4====4===      q |####################
   +---------------------->        +---------------------->
              time                             time

 Each user owns a narrow band      Each user owns the full band,
 ALL of the time.                  but only SOME of the time.
```

**Key consequence for TDM:** if a link of rate R is divided into N slots, each circuit gets a rate of **R/N**.

---

## 11. 🔢 Worked numerical — circuit-switched file transfer

**Question:** How long does it take to send a file of **80 Kbytes** from host A to host B over a **circuit-switched** network?
- All links are **1.536 Mbps**
- Each link uses **TDM with 24 slots/sec**
- Time to establish the end-to-end circuit is **500 msec**

---

**Step 1 — Convert the file size from bytes to bits.**

$$
80\ \text{Kbytes} = 80 \times 1000 \times 8 = 640{,}000\ \text{bits}
$$

*Why this step matters:* **networks are measured in bits, end systems in bytes**, and there are **8 bits to a byte**. Forgetting the ×8 is the single most common mistake on this question.

---

**Step 2 — Find the rate of one circuit.**

The link's full capacity is 1.536 Mbps, but TDM splits it into 24 slots, and our call only gets one slot.

$$
R_{circuit} = \frac{1.536\ \text{Mbps}}{24} = \frac{1{,}536{,}000}{24} = 64{,}000\ \text{bps}
$$

*Interpretation:* our call effectively owns a **64 kbps** channel — which is exactly one standard voice channel, and not a coincidence.

---

**Step 3 — Compute the transmission time over that circuit.**

$$
t_{transmit} = \frac{640{,}000\ \text{bits}}{64{,}000\ \text{bps}} = 10\ \text{seconds}
$$

---

**Step 4 — Add the circuit establishment time.**

In circuit switching nothing can be sent until the path is reserved. Setup = 500 msec = **0.5 s**.

$$
t_{total} = 10 + 0.5 = \mathbf{10.5\ \text{seconds}}
$$

---

**Answer: 10.5 seconds.**

> **What is happening physically:** for the first half-second, zero data moves — the network is reserving one time slot on every link along the path. Then, for exactly 10 seconds, bits flow at a perfectly steady 64 kbps, in order, with no queueing and no possibility of loss. That steadiness is what you buy with the setup delay.

---

## 12. Packet switching vs circuit switching — the capacity question

**Setup:**
- A **1 Gb/s** link
- Each user needs **100 Mb/s when "active"**
- Each user is active **10% of the time**

**Q: How many users can this network support?**

**Under circuit switching:**
Each user must be given a *reserved* 100 Mb/s circuit, whether they use it or not.

$$
N = \frac{1\ \text{Gb/s}}{100\ \text{Mb/s}} = \frac{1000}{100} = \mathbf{10\ \text{users}}
$$

**Under packet switching:**
Resources are only consumed when a user is actually active. With **35 users**, the probability that **more than 10 are active at the same time is less than 0.0004**.

So packet switching supports **35 users** at essentially the same quality of service — a **3.5× improvement** — purely by exploiting the fact that users are idle 90% of the time.

> *(The 0.0004 figure comes from a binomial probability calculation — the deck leaves this as a homework problem for those who have taken probability. The exam point is the conclusion, not the derivation.)*

---

## 13. Is packet switching a "slam dunk winner"?

**Advantages:**
- Great for **bursty** data — sometimes has data to send, other times not
- **Resource sharing**
- Simpler: **no call setup**

**Disadvantages:**
- **Excessive congestion possible:** packet delay and loss due to buffer overflow
- Needs extra protocols for **reliable data transfer** and **congestion control**

**Open question raised:** How do you provide circuit-like behaviour *with* packet switching? Answer from the deck: *"It's complicated."* The rest of the course studies techniques that try to make packet switching as circuit-like as possible.

---

## 📐 Formula summary — Lecture 02

| # | Formula | Meaning | Units |
|---|---|---|---|
| 1 | $$d_{trans} = \dfrac{L}{R}$$ | Transmission delay — time to push an L-bit packet into a link of rate R | L in bits, R in bps, result in seconds |
| 2 | $$R_{circuit} = \dfrac{R_{link}}{N}$$ | Rate of one TDM circuit when the link is divided into N slots | bps |
| 3 | $$t_{circuit\ total} = t_{setup} + \dfrac{\text{file size in bits}}{R_{circuit}}$$ | Total time for a circuit-switched transfer | seconds |
| 4 | $$N_{circuit} = \dfrac{R_{link}}{R_{per\ user}}$$ | Users supportable under circuit switching | users |

**Unit conversions you must have automatic:**
- 1 byte = **8 bits**
- 1 Kbit = 10³ bits, 1 Mbit = 10⁶ bits, 1 Gbit = 10⁹ bits
- 1 msec = 10⁻³ sec, 1 µsec = 10⁻⁶ sec
- Rule of thumb from the deck: **networks are in bits, end systems are in bytes**

---

## Key takeaways

- Access networks: **cable = shared + asymmetric + fast**; **DSL = dedicated + slower**; both carry voice/TV and data on different frequencies.
- Media: guided (TP, coax, fiber) vs unguided (radio). Fiber wins on rate and error rate; radio wins on mobility and loses to reflection/obstruction/interference.
- **Circuit switching** = reserve first, guaranteed, wasteful when idle. **Packet switching** = no setup, efficient, but congestion and loss are possible.
- The core does two distinct things: **forwarding** (local, uses the table) and **routing** (global, builds the table).
- **Store-and-forward** means a router must receive the *whole* packet before sending any of it onward — this is the reason delays accumulate per hop.
- **d_trans = L/R** is the first formula of the course, and it appears in almost every numerical from here on.
# Lecture 03 — Internet Structure & Performance (Delay, Loss, Throughput)
**CS 3001 Computer Networks · 25 August 2026 · Chapter 1**

---

## Index (as per the deck)

1. Internet structure: a "network of networks"
2. Chapter 1 roadmap → Performance section
3. Performance metrics: delay, loss, throughput
4. How packet delay and loss occur
5. Packet delay: four sources
6. The caravan analogy (two versions)
7. Extending the caravan example — the pipelining timeline
8. Numerical: segmented vs unsegmented message
9. Queueing delay and traffic intensity
10. "Real" Internet delays and routes — traceroute
11. Packet loss
12. Throughput — bandwidth vs throughput vs rate
13. Bottleneck links
14. Bandwidth-Delay Product (BDP)

---

## 1. Internet structure: a "network of networks"

Hosts connect to the Internet via **access ISPs**. Those access ISPs must themselves be **interconnected**, so that any two hosts anywhere can send packets to each other. The resulting network of networks is **very complex**, and its evolution is driven by **economics and national policies**.

The deck builds the structure up in **six steps**. Learn it as a story, not a picture.

---

### Step 1 — The naive idea: connect everyone to everyone

```
   access   access   access   access
     net      net      net      net
      \   \  /  \  /  \  /  /  /
       \   \/    \/    \/  /  /      ... every pair directly wired
        \  /\    /\    /\ /  /
      access   access   access
        net      net      net
```

**Why it fails:** connecting each access ISP to every other one directly **doesn't scale** — it requires **O(N²) connections**. With millions of access ISPs this is impossible.

---

### Step 2 — One global transit ISP

```
      access   access   access   access
        net      net      net      net
          \       |       |       /
           \      |       |      /
            +--[ GLOBAL ISP ]--+
           /      |       |      \
      access   access   access   access
```

- **Customer and provider ISPs have an economic agreement** — the access ISP *pays* the global ISP for transit. This is the key insight: Internet structure is shaped by **who pays whom**.
- Cost drops from O(N²) to O(N).

---

### Step 3 — Competitors appear

If one global ISP is a viable business, **there will be competitors**: ISP A, ISP B, ISP C.

---

### Step 4 — Competitors must interconnect → IXPs and peering

```
   access nets ---> [ ISP A ]
                        \
                       [ IXP ] ------ [ ISP B ] <--- access nets
                        /   \
                  [ ISP C ]  \___ peering link ___
```

- **IXP = Internet eXchange Point** — a physical location where multiple ISPs interconnect.
- **Peering link** — a direct link between two ISPs, typically settlement-free (neither pays the other), as opposed to a customer/provider transit link.

---

### Step 5 — Regional ISPs arise

**Regional ISPs** appear in the middle to connect access networks up to the tier-1 ISPs, rather than each access net connecting directly.

---

### Step 6 — Content provider networks

**Content provider networks** (Google, Microsoft, Akamai) run **their own network** to bring services and content close to end users — often **bypassing tier-1 and regional ISPs entirely**.

---

### The final picture — the Internet hierarchy

```
            [ Tier 1 ISP ]        [ Tier 1 ISP ]        [ Google ]
                  |    \______IXP______/    \______IXP______/
                 IXP                 |
                  |                  |
          [ Regional ISP ]     [ Regional ISP ]
            /   |   |   \        /   |   |   \
        access access access   access access access
          ISP   ISP   ISP        ISP   ISP   ISP
```

**At the "centre": a small number of well-connected large networks.**
- **Tier-1 commercial ISPs** — e.g. Level 3, Sprint, AT&T, NTT — with **national and international coverage**.
- **Content provider networks** — e.g. Google, Facebook — private networks connecting their data centers to the Internet, **often bypassing tier-1 and regional ISPs**.

---

## 2. How do we evaluate a network? — Performance metrics

Three metrics, and the rest of the lecture is these three:

1. **Delay**
2. **Loss**
3. **Throughput**

---

## 3. How do packet delay and loss occur?

- Packets **queue in router buffers**, waiting their turn for transmission.
- The **queue length grows** when the arrival rate to a link *temporarily* exceeds the output link capacity.
- **Packet loss occurs when the memory holding queued packets fills up.**

```
    A ---\                                     packet being transmitted
          \                                    (transmission delay)
           [ ROUTER ]===[ | | | ]===============>------->
          /             ^^^^^^^^^
    B ---/              packets in buffers
                        (queueing delay)

    [ free buffers ]  <-- arriving packets are DROPPED (loss)
                          if no free buffers remain
```

---

## 4. Delay: the four components

**The question:** *How long does it take to send a packet from source to destination?*

Delay consists of **four components**, which split into two natural groups:

| Component | Caused by |
|---|---|
| **Queueing delay** | Traffic mix — how busy the network is |
| **Processing delay** | Switch / router internals |
| **Transmission delay** | Link properties |
| **Propagation delay** | Link properties |

### 📐 The master delay formula

$$
d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}
$$

### Diagram — where each delay happens at one node

```
                       [ A ]
                         |
                    (1) processing
                         |
                    (2) queueing      <- packets waiting
                         |
                    (3) transmission  <- pushing bits onto the wire
                         |
              =====(4) propagation=====> [ B ]
                    (bits travelling
                     down the link)
```

### Each one in detail

**d_proc — nodal processing delay**
- Check bit errors
- Determine output link
- Typically **< microseconds**

**d_queue — queueing delay**
- Time waiting at the **output link** for transmission
- **Depends on the congestion level of the router** — this is the only one of the four that varies unpredictably

**d_trans — transmission delay**
- **L** = packet length (bits)
- **R** = link transmission rate (bps)
- $$d_{trans} = \dfrac{L}{R}$$

**d_prop — propagation delay**
- **d** = length of the physical link (metres)
- **s** = propagation speed (**~2 × 10⁸ m/sec**)
- $$d_{prop} = \dfrac{d}{s}$$

> ⚠️ **d_trans and d_prop are very different things — the classic exam trap.**
> **d_trans** depends on how *big the packet is* and how *fast the router can push bits out*. It does **not** depend on distance.
> **d_prop** depends on how *far apart the two ends are*. It does **not** depend on packet size or link rate.
> A 1 Mbit packet on a 1 m cable has a huge d_trans and near-zero d_prop. A 1-bit packet across the Atlantic has near-zero d_trans and a huge d_prop.

---

## 5. The caravan analogy

```
                 100 km              100 km
   [ten-car caravan]--[toll booth]--------[toll booth]--------[toll booth]
    (= a 10-bit packet)  (= router)
```

**Mapping:**

| Caravan | Network |
|---|---|
| A car | A **bit** |
| The caravan | A **packet** |
| Toll service | **Link transmission** |
| Toll booth | **Router** |

### Version 1 — slow cars, fast booth

**Given:** toll booth takes **12 sec** to service one car (= bit transmission time). Cars "propagate" at **100 km/hr**. Distance between booths = **100 km**.

**Q: How long until the caravan is lined up before the 2nd toll booth?**

**Step 1 — time to push the entire caravan through the first booth (transmission delay).**

$$
d_{trans} = 12\ \text{sec/car} \times 10\ \text{cars} = 120\ \text{sec} = 2\ \text{minutes}
$$

*This is exactly L/R: 10 "bits" at 12 sec each.*

**Step 2 — time for the last car to propagate from booth 1 to booth 2.**

$$
d_{prop} = \frac{d}{s} = \frac{100\ \text{km}}{100\ \text{km/hr}} = 1\ \text{hour} = 60\ \text{minutes}
$$

**Step 3 — add them.**

$$
\text{Total} = 2 + 60 = \mathbf{62\ \text{minutes}} = \mathbf{3720\ \text{sec}}
$$

**Answer: 62 minutes.** Note that the whole caravan travels together as a unit because the booth is much faster than the road — this is **store-and-forward** behaviour.

---

### Version 2 — fast cars, slow booth

**Given:** cars now propagate at **1000 km/hr**, and the toll booth now takes **one minute** to service a car.

**Q: Will cars arrive at the 2nd booth before all cars are serviced at the first booth?**

**Step 1 — propagation delay.**

$$
d_{prop} = \frac{100\ \text{km}}{1000\ \text{km/hr}} = 0.1\ \text{hour} = 6\ \text{minutes}
$$

**Step 2 — when does the first car reach booth 2?**
It is serviced at booth 1 in 1 minute, then propagates for 6 minutes:

$$
1 + 6 = 7\ \text{minutes}
$$

**Step 3 — how many cars have been serviced at booth 1 by minute 7?**
At 1 minute per car, 7 cars have been serviced — so **3 cars are still at the first booth**.

**Answer: Yes.** After 7 minutes the first car arrives at the second booth while three cars are still queued at the first.

> **What this shows:** whether the caravan stays together depends on the *ratio* of transmission speed to propagation speed. This is the physical intuition behind pipelining, which the next example makes precise.

---

## 6. Extending the caravan example — the arrival timeline

**Setup:** two links R1 and R2. Transmission delay = 12 s per bit on each link. Propagation delay = 3600 s (1 hour).

| Bit | Starts tx on R1 | Finishes tx on R1 | Propagation | Arrives at destination |
|---|---|---|---|---|
| Bit 1 | 0 s | 12 s | 3600 s | 3612 s |
| Bit 2 | 12 s | 24 s | 3600 s | 3624 s |
| Bit 3 | 24 s | 36 s | 3600 s | 3636 s |
| ... | ... | ... | ... | ... |
| Bit 9 | 96 s | 108 s | 3600 s | 3708 s |
| Bit 10 | 108 s | 120 s | 3600 s | 3720 s |

### 🔑 Bottom line

> **After the 1st bit arrives at the destination, one bit arrives every transmission-delay interval** (assuming transmission delays are the same on each link).
>
> **You can interchange "bit" with "packet" throughout.** This is the single most useful shortcut for multi-packet delay problems: compute the full end-to-end delay for the *first* packet, then add **(n−1) × d_trans** for the remaining n−1 packets.

---

## 7. 🔢 Worked numerical — segmentation and pipelining

```
            Rate = 10 Mbps              Rate = 10 Mbps
   [ A ] ---------------------- [ R ] ---------------------- [ B ]
          d_prop = 1 sec                 d_prop = 1 sec
```

**Question:** Host A sends a message **M = 10,000,000 bits** to host B, with one router R in between. Both links are **10 Mbps**. Propagation delay on both links is **1 second**. Queueing and processing delays are **zero**. Calculate total delay for the message to reach B in two scenarios.

---

### Scenario 1 — message sent as ONE packet (no segmentation)

**Step 1 — transmission delay on link 1 (A → R).**

$$
d_{trans1} = \frac{L}{R} = \frac{10{,}000{,}000}{10{,}000{,}000} = 1\ \text{sec}
$$

*A spends 1 full second pushing the message onto the first link.*

**Step 2 — transmission delay on link 2 (R → B).**
Same packet size, same link rate:

$$
d_{trans2} = 1\ \text{sec}
$$

**Step 3 — recognise the store-and-forward constraint.**
R **cannot** start sending anything until the *entire* 10 Mbit message has arrived. So the four delays are strictly **sequential** — nothing overlaps.

**Step 4 — add all four.**

$$
\text{Total} = d_{trans1} + d_{prop1} + d_{trans2} + d_{prop2} = 1 + 1 + 1 + 1 = \mathbf{4\ \text{sec}}
$$

---

### Scenario 2 — message segmented into 10 packets of 1,000,000 bits each

**Step 1 — transmission delay for ONE packet.**

$$
d_{trans} = \frac{1{,}000{,}000}{10{,}000{,}000} = 0.1\ \text{sec}
$$

*Each packet is 10× smaller, so each takes 10× less time to push out.*

**Step 2 — total delay for the FIRST packet to reach B.**

$$
d_{trans1} + d_{prop1} + d_{trans2} + d_{prop2} = 0.1 + 1 + 0.1 + 1 = 2.2\ \text{sec}
$$

*This is the "pipeline fill" time — the cost of getting the assembly line started.*

**Step 3 — delay for the remaining 9 packets.**
By the bottom-line rule from §6: once the first packet has arrived, **one more packet arrives every d_trans**. While R was forwarding packet 1, A was already transmitting packet 2 — the two links work **in parallel**.

$$
9 \times d_{trans} = 9 \times 0.1 = 0.9\ \text{sec}
$$

**Step 4 — total.**

$$
\text{Total} = 2.2 + 0.9 = \mathbf{3.1\ \text{sec}}
$$

---

### 🔑 Bottom line

> Due to **store & forward**, without segmentation (Scenario 1) the router had to **sit idle** waiting for the entire 10 Mbit message to arrive before it could forward anything — **no overlapping**.
>
> Once the message is segmented into multiple smaller packets (Scenario 2), the router can start forwarding the earlier packets **while A is still transmitting the later packets in parallel**. **This is called pipelining.**

**Saving: 4 − 3.1 = 0.9 seconds, a 22.5% improvement, purely from segmentation.**

---

## 8. Queueing delay revisited — traffic intensity

**Variables:**
- **a** = average packet arrival rate (packets/sec)
- **L** = packet length (bits)
- **R** = link bandwidth (bit transmission rate, bps)

### 📐 Traffic intensity

$$
\text{Traffic intensity} = \frac{L \cdot a}{R} = \frac{\text{arrival rate of bits}}{\text{service rate of bits}}
$$

*Numerator: L bits per packet × a packets per second = bits arriving per second.
Denominator: R bits per second the link can drain.*

### The three regimes

| Condition | Average queueing delay | Interpretation |
|---|---|---|
| **La/R ≈ 0** | Small | Link is mostly idle; packets rarely wait |
| **La/R → 1** | **Large** | Link is saturated; delay blows up |
| **La/R > 1** | **Infinite** | More work is arriving than can ever be serviced — the queue grows without bound |

### Diagram — the classic hockey-stick curve

```
  avg      |                                    |
queueing   |                                   /
 delay     |                                  /
           |                                 /
           |                               /
           |                            _/
           |                       ___/
           |________________----/
           +------------------------------------|----->
           0                                    1
                     traffic intensity = La/R
```

> **Read the curve like this:** delay is nearly flat and negligible until you approach ~70–80% utilisation, then it rises almost vertically. This is why networks are engineered to run *well below* capacity — the last 20% of capacity costs you enormously in delay.

---

## 9. "Real" Internet delays and routes — traceroute

**traceroute** (`tracert` on Windows) measures delay from a source to each router along the end-to-end path.

**How it works — for each router *i* on the path:**
1. Sends **three packets** that will reach router *i* (by setting the **time-to-live (TTL)** field to *i*)
2. Router *i* returns the packets to the sender
3. The sender **measures the time interval between transmission and reply**

```
   source --3 probes--> [R1]
   source -----3 probes-----> [R2]
   source ----------3 probes-------> [R3]  ...
```

### Reading a real traceroute (gaia.cs.umass.edu → www.eurecom.fr)

Three things the deck wants you to spot:

| Observation | Explanation |
|---|---|
| Each line shows **3 delay measurements** | These are **all-inclusive** delays — processing + queueing + transmission + propagation |
| A jump from ~22 ms (hop 7) to ~104 ms (hop 8) | A **trans-oceanic link** — large **propagation delay** |
| Delays sometimes **decrease** at a later hop (e.g. hop 11 → 12) | The **queueing delay at the earlier router may be greater** than at the later one. Delay is not monotonic along a path. |
| A line showing `* * *` | **No response** — the probe was lost, or the router is not replying |

*Try traceroutes from other countries at www.traceroute.org; graphical front-ends like PingPlotter exist.*

---

## 10. Packet loss

- The queue (a.k.a. buffer) preceding a link has **finite capacity**.
- A packet arriving at a **full queue is dropped (lost)**.
- A lost packet **may be retransmitted** by the previous node, by the source end system, **or not at all**.

```
         buffer (waiting area)
   A ---\  [ | | | | | |FULL]     packet being transmitted
         [ ROUTER ]================>
   B ---/     ^
              |
       packet arriving to a full buffer is LOST
```

---

## 11. Throughput

**The question:** *At what rate is the destination receiving data from the source?*

### Bandwidth vs Throughput vs Rate

These are used interchangeably in casual conversation but have **distinct meanings** in networking.

| | **Bandwidth** | **Throughput** | **Rate** |
|---|---|---|---|
| **Definition** | The **maximum data capacity** of a link or channel | The **actual** amount of data successfully transmitted in a given time | General term for the speed at which data is sent/received (similar to bandwidth) |
| **Units** | bps (e.g. 100 Mbps) | bps — but **often lower** than the theoretical bandwidth | bps |
| **Analogy** | The **width** of a highway — how many cars *could* travel side by side (2-lane vs 3-lane motorway) | How many cars **actually** make it through per second, given traffic jams and accidents | — |
| **Key point** | A **theoretical upper limit** — not what you actually get | The **effective** bandwidth achieved, reduced by congestion, protocol overhead, packet loss and retransmissions | — |

### Two kinds of throughput
- **Instantaneous** — rate at a given point in time
- **Average** — rate over a longer period of time

### The fluid-in-a-pipe picture

```
  [ server ]   pipe that can carry     pipe that can carry
  sends bits   fluid at rate Rs         fluid at rate Rc     [ client ]
  (fluid) -->  ====================>  ====================>
   file of F         Rs bits/sec             Rc bits/sec
      bits
```

---

## 12. Bottleneck link

### 📐 Two-link throughput

$$
\text{Average end-to-end throughput} = \min(R_s, R_c)
$$

| Case | Result | Why |
|---|---|---|
| **R_s < R_c** | Throughput = **R_s** | The server can't push bits any faster; the second pipe is never full |
| **R_s > R_c** | Throughput = **R_c** | The second pipe can't drain fast enough; bits back up |

**Definition — bottleneck link:** the link on the end-to-end path that **constrains** the end-to-end throughput.

> **Concrete case:** if A → R runs at 20 Mbps and R → B runs at 10 Mbps, then **R has created a bottleneck**, because it has a lower rate than A. Data arrives at R faster than R can forward it, and the end-to-end throughput is capped at 10 Mbps regardless of how fast A can send.

### Network scenario — many connections sharing a backbone

```
      Rs    Rs    Rs
        \   |   /
         \  |  /
          [ R ]          <- shared backbone link, rate R
         /  |  \
        /   |   \
      Rc    Rc    Rc

   10 connections fairly share the backbone link R
```

### 📐 Per-connection throughput with a shared backbone

$$
\text{throughput} = \min\left(R_c,\ R_s,\ \frac{R}{10}\right)
$$

Generalised for **n** fairly-sharing connections:

$$
\text{throughput} = \min\left(R_c,\ R_s,\ \frac{R}{n}\right)
$$

**In practice, R_c or R_s is often the bottleneck** — the backbone is usually over-provisioned, so the constraint is at the edges.

---

## 13. Bandwidth-Delay Product (BDP)

**Definition:** The BDP is the product of a link's **rate/capacity** (in bits per second) and its **round-trip delay time, RTT** (in seconds).

The result — an amount of data measured in **bits (or bytes)** — is equivalent to:
- the **maximum amount of data on the network circuit at any given time**,
- i.e. data that has been **transmitted but not yet acknowledged**,
- i.e. the **maximum number of bits that can be inserted into the pipe (link) in a given interval of time**.

### The motorway analogy

| Quantity | Motorway meaning |
|---|---|
| **Bandwidth** | Width of the motorway — how many lanes (how many cars can *enter* per second) |
| **Delay** | Time to cover the length of the motorway (how long a car takes from A to B) |
| **BDP** | **Total cars in transit on the motorway at once** = (cars entering per second) × (seconds each car spends on the road) |

### 📐 Formula

$$
\text{BDP} = \text{Bandwidth} \times \text{Delay (RTT)}
$$

---

### 🔢 Worked example 1 — the car version (units check)

**Given:** Bandwidth = 5 cars/sec, delay = 10 sec.

$$
\text{BDP} = 5 \times 10 = \mathbf{50\ \text{cars}}
$$

*Note how the units cancel: (cars/sec) × (sec) = cars. Same mechanism as (bits/sec) × (sec) = bits.*

---

### 🔢 Worked example 2 — moderate-speed satellite network

**Given:** 512 kbit/s, RTT = 900 ms.

**Step 1 — put both quantities in base units.**
B = 512 × 10³ bits/s
D = 900 ms = 900 × 10⁻³ s = 0.9 s

**Step 2 — multiply.**

$$
B \times D = (512 \times 10^3) \times (900 \times 10^{-3}) = 460{,}800\ \text{bits}
$$

**Step 3 — express in convenient units.**
460,800 bits = **460.8 kbit**
460,800 ÷ 8 = 57,600 bytes = **57.6 kB**

**Answer: 460.8 kbit = 57.6 kB in flight at any instant.**

*Why satellites have huge BDP:* the rate is modest, but the 900 ms RTT (geostationary orbit is ~36,000 km up, and the signal makes that trip four times) means a lot of data is "on the wire" unacknowledged at any moment.

---

### 🔢 Worked example 3 — residential DSL

**Given:** 2 Mbit/s, RTT = 50 ms.

**Step 1 — base units.**
B = 2 × 10⁶ bits/s
D = 50 × 10⁻³ s = 0.05 s

**Step 2 — multiply.**

$$
B \times D = (2 \times 10^6) \times (50 \times 10^{-3}) = 100 \times 10^3 = 100{,}000\ \text{bits}
$$

**Step 3 — convert.**
100,000 bits = **100 kbit**
100,000 ÷ 8 = 12,500 bytes = **12.5 kB**

**Answer: 100 kbit = 12.5 kB.**

> **Compare the two:** DSL has **4× the bandwidth** of the satellite link but **1/18 the delay**, so its BDP is **4.6× smaller**. This matters enormously in Chapter 3 — a sender must be able to keep at least a BDP worth of unacknowledged data in flight, or the link sits idle.

---

## 📐 Formula summary — Lecture 03

| # | Formula | Meaning |
|---|---|---|
| 1 | $$d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}$$ | Total delay at one node |
| 2 | $$d_{trans} = \dfrac{L}{R}$$ | L = packet length (bits), R = link rate (bps) |
| 3 | $$d_{prop} = \dfrac{d}{s}$$ | d = link length (m), s = propagation speed ≈ **2 × 10⁸ m/sec** |
| 4 | $$d_{end\text{-}to\text{-}end} = \sum_{\text{all links}} (d_{trans} + d_{prop}) + \sum_{\text{all routers}} (d_{proc} + d_{queue})$$ | For a multi-hop path |
| 5 | $$\text{Delay for } n \text{ packets} = d_{\text{first packet end-to-end}} + (n-1)\,d_{trans}$$ | The pipelining shortcut |
| 6 | $$\text{Traffic intensity} = \dfrac{L \cdot a}{R}$$ | a = avg packet arrival rate. ≈0 → small delay; →1 → large; >1 → infinite |
| 7 | $$\text{Throughput (2 links)} = \min(R_s, R_c)$$ | Bottleneck rule |
| 8 | $$\text{Throughput (n shared)} = \min\!\left(R_c, R_s, \dfrac{R}{n}\right)$$ | n connections fairly sharing backbone R |
| 9 | $$\text{BDP} = \text{Bandwidth} \times \text{RTT}$$ | Bits in flight / transmitted-but-unacknowledged |

**Constants and conversions:**
- Propagation speed **s ≈ 2 × 10⁸ m/sec** (≈ ⅔ the speed of light in a vacuum)
- 1 byte = 8 bits; always divide bits by 8 to report kB/MB
- ms = 10⁻³ s, µs = 10⁻⁶ s

---

## Key takeaways

- Internet structure is **hierarchical and economic**: access ISPs → regional ISPs → tier-1 ISPs, stitched together by **IXPs** and **peering links**, with **content provider networks** cutting across the hierarchy.
- The **four delay components** — and the fact that **d_trans depends on packet size and link rate, while d_prop depends on distance** — is the highest-yield concept in Chapter 1.
- **Segmentation enables pipelining**, which is why the Internet sends packets rather than whole messages.
- **Traffic intensity La/R** predicts queueing delay: harmless near 0, catastrophic near 1.
- **Throughput is set by the bottleneck link**, min() over the path.
- **BDP = bandwidth × RTT** = how much data is "in flight" — high-bandwidth, high-delay links (satellite) have large BDPs.

---

## Course admin announced in this lecture

- **Assignment #1 (Chapter 1)** — uploaded on Google Classroom after the 27 Aug lecture. **Due Thursday 3 September 2026, during the lecture.** Handwritten **hard copy** submitted directly to the instructor.
- **Quiz #1 (Chapter 1)** — in class **Thursday 3 September 2026**, during lecture time. **No retake. Be on time.**
# Lecture 04 — Network Security & Protocol Layers
**CS 3001 Computer Networks · 27 August 2026 · Chapter 1 (concludes)**

---

## Index (as per the deck)

1. Bandwidth-Delay Product (recap)
2. Network security
   - Malware and botnets
   - Denial of Service (DoS)
   - Packet interception (sniffing)
   - Fake identity (IP spoofing)
   - Lines of defence
3. Protocol layers and reference models
   - Air travel analogy
   - Why layering?
   - The layered Internet protocol stack
   - Services, layering and encapsulation
   - Logical vs physical communication
   - Protocols at different layers
   - Encapsulation: an end-to-end view
4. Chapter 1 summary
5. Additional slides: ISO/OSI reference model, Wireshark

---

## 1. Bandwidth-Delay Product — recap

This slide is **repeated verbatim from the end of Lecture 03**, with one clarification added: the delay used is specifically the **round-trip delay time, i.e. RTT**.

**See Lecture 03 §13** for the full derivation and all three worked examples (car analogy, satellite, DSL). The formula, restated:

$$
\text{BDP} = \text{Bandwidth (bits/sec)} \times \text{RTT (sec)} = \text{bits in flight}
$$

Quick recall of the two numerical results:
- Satellite: 512 kbit/s × 900 ms = **460.8 kbit = 57.6 kB**
- Residential DSL: 2 Mbit/s × 50 ms = **100 kbit = 12.5 kB**

---

## 2. Network security

### The framing

The Internet was **not originally designed with (much) security in mind**.
- Original vision: *"a group of mutually trusting users attached to a transparent network."*
- Internet protocol designers have been playing **"catch-up"** ever since.
- **Security considerations exist in all layers.**

**Three things we now need to think about:**
1. How bad guys can attack computer networks
2. How we can defend networks against attacks
3. How to design new architectures that are **immune** to attacks

---

### 2.1 Bad guys: malware

Devices attach to the Internet to receive/send data — and with the good stuff (Instagram, search results, streaming music, movies, games) comes the malicious stuff, called **malware**.

**Properties of malware:**
- Can **enter and infect** attached devices
- Can **self-replicate**, and therefore **spread exponentially fast**
- Can **delete files**, **install spyware**, and **collect sensitive & private information** (passwords, etc.) and share it with the bad guys

### Botnets — the vocabulary chain

| Term | Meaning |
|---|---|
| **Botnet** | A network of thousands of similarly compromised devices |
| **Bots / zombies** | The individual compromised devices in the botnet |
| **Bot-herder** | The bad guy(s) who control the botnet |
| **Command-and-control server** | The server the bot-herder uses to send **hidden orders** to all the bots |

**All of this may be invisible to the actual user(s) of the compromised device(s).**

**What botnets are leveraged for:**
- Spam e-mail distribution
- **Distributed Denial-of-Service (DDoS) attacks**
- Mining cryptocurrency on your device

```
                [ bot-herder ]
                       |
           [ command-and-control server ]
              /        |        \
         [bot]      [bot]      [bot]   ... thousands
        (zombie)   (zombie)   (zombie)
              \        |        /
                  all fire at
                  --> [ TARGET ]
```

---

### 2.2 Bad guys: denial of service

**Definition:** Attackers make resources (server, bandwidth) **unavailable to legitimate traffic** by overwhelming the resource with **bogus (fake) traffic**.

**The three-step attack:**
1. **Select target**
2. **Break into hosts** around the network (see: botnet)
3. **Send packets to the target server** from the compromised hosts, to crash the server

### The three categories of DoS attack

| Category | Mechanism |
|---|---|
| **Vulnerability attack** | Stopping a service or crashing a device by sending **well-crafted messages in the right sequence** to the target host. (Few packets, precisely aimed.) |
| **Bandwidth flooding** | The attacker sends a **very large number of packets** to the target host — so many that the **links become clogged**, making it impossible for legitimate packets to reach the target. (Attacks the *pipe*.) |
| **Connection flooding** | The attacker establishes a **large number of half-open or fully open TCP connections** with the target host. The host becomes so bogged down with bogus connections that it **stops accepting legitimate connections**. (Attacks the *server's connection table*.) |

> **Distinguishing them in an exam:** vulnerability attack = *exploit a bug*. Bandwidth flooding = *fill the link*. Connection flooding = *fill the connection state table*.

---

### 2.3 Bad guys: packet interception ("packet sniffing")

**How it works:**
- Requires **broadcast media** — shared Ethernet, wireless
- A **promiscuous network interface** reads and records **all packets passing by** (including passwords!)

```
   [ A ]                  [ C ]  <-- promiscuous NIC, records everything
     ^                      ^
     |                      |
     +---- shared medium ---+----------------- [ B ]
              src:B  dest:A  payload
                       ^
              C is not the destination, but it sees
              and records the packet anyway
```

**Wireshark**, the software used in the end-of-chapter labs, is a (free) packet sniffer.

---

### 2.4 Bad guys: fake identity — IP spoofing

**Definition:** injection of a packet with a **false source address**.

```
   [ A ]  <---- src:B  dest:A ----  [ C ]
                 payload
                                    C sends the packet but writes B's
   [ B ]  (did nothing)             address in the "source" field.
                                    A believes it came from B.
```

> **Sniffing vs spoofing:** sniffing is a **read** attack (confidentiality); spoofing is a **write** attack (authentication/integrity).

---

### 2.5 Lines of defence

| Defence | What it provides | Note from the deck |
|---|---|---|
| **Authentication** | Proving you are who you say you are | Cellular networks provide **hardware identity via the SIM card**; there is **no such hardware assist in the traditional Internet** |
| **Confidentiality** | Via **encryption** | |
| **Integrity checks** | **Digital signatures** prevent/detect tampering | |
| **Access restrictions** | **Password-protected VPNs** | |
| **Firewalls** | Specialised **"middleboxes"** in access and core networks | **Off-by-default**: filter incoming packets to restrict senders, receivers, applications. Also **detect and react to DoS attacks** |

*(Much more on security in Chapter 8.)*

---

## 3. Protocol layers and reference models

### The problem

Networks are complex, with many "pieces": **hosts, routers, links of various media, applications, protocols, hardware, software.**

**Question:** Is there any hope of organising the structure of a network — and/or our discussion of networks?

**Answer: yes — layering.**

---

### 3.1 The air travel analogy

End-to-end transfer of a person plus baggage:

```
   ticket (purchase)    <--- ticketing service --->    ticket (complain)
   baggage (check)      <--- baggage service   --->    baggage (claim)
   gates (load)         <--- gate service      --->    gates (unload)
   runway takeoff       <--- runway service    --->    runway landing
   airplane routing     <--- routing service   --->    airplane routing
                    airplane routing (intermediate)
```

**Layers: each layer implements a service**
- via its **own internal-layer actions**
- **relying on services provided by the layer below**

Notice that the *ticketing* layer at the departure airport talks conceptually to the *ticketing* layer at the arrival airport — even though physically, everything has to go down through gates and runways and back up again. That is exactly how protocol layers work.

---

### 3.2 Why layering?

An approach to designing and discussing complex systems:

1. **Explicit structure** allows identification of the system's pieces and their relationships → gives us a **layered reference model for discussion**.
2. **Modularisation eases maintenance and updating** of the system.
   - A change in a layer's **service implementation is transparent** to the rest of the system.
   - E.g., a change in **gate procedure doesn't affect the rest** of the airline system.

---

### 3.3 The layered Internet protocol stack (5 layers)

```
   +----------------+
   |  application   |  supporting network applications
   +----------------+      HTTP, IMAP, SMTP, DNS
   |   transport    |  process-to-process data transfer
   +----------------+      TCP, UDP
   |    network     |  routing of datagrams from source to destination
   +----------------+      IP, routing protocols
   |      link      |  data transfer between neighbouring network elements
   +----------------+      Ethernet, 802.11 (WiFi), PPP
   |    physical    |  bits "on the wire"
   +----------------+
```

| Layer | Job | Protocols |
|---|---|---|
| **Application** | Supporting network applications | HTTP, IMAP, SMTP, DNS |
| **Transport** | **Process-to-process** data transfer | TCP, UDP |
| **Network** | **Routing of datagrams** from source to destination | IP, routing protocols |
| **Link** | Data transfer between **neighbouring** network elements | Ethernet, 802.11 (WiFi), PPP |
| **Physical** | **Bits "on the wire"** | — |

---

### 3.4 Services, layering and encapsulation

This is the core mechanism of the whole chapter. Read it **top to bottom at the source**.

**Step 1 — Application layer.**
The application **exchanges messages (M)** to implement some application service, using the services of the transport layer.

**Step 2 — Transport layer.**
The transport-layer protocol transfers **M** (e.g. reliably) from **one process to another**, using services of the network layer.
- It **encapsulates** the application-layer message **M** with a transport-layer header **Ht** to create a **transport-layer segment**: `[Ht | M]`
- **Ht** is used by the transport protocol to implement its service.

**Step 3 — Network layer.**
The network-layer protocol transfers the transport-layer segment `[Ht | M]` from **one host to another**, using link-layer services.
- It **encapsulates** the segment with a network-layer header **Hn** to create a **network-layer datagram**: `[Hn | Ht | M]`
- **Hn** is used by the network layer to implement its service.

**Step 4 — Link layer.**
The link-layer protocol transfers the datagram `[Hn | Ht | M]` from **host to neighbouring host**, using network-layer services.
- It **encapsulates** the datagram with a link-layer header **Hl** to create a **link-layer frame**: `[Hl | Hn | Ht | M]`

### Diagram — encapsulation building up and stripping down

```
   SOURCE                                      DESTINATION

   application   M                    M            application
                  |                   ^
   transport    [Ht| M]            [Ht| M]         transport
                  |                   ^
   network   [Hn|Ht| M]         [Hn|Ht| M]         network
                  |                   ^
   link    [Hl|Hn|Ht| M]    [Hl|Hn|Ht| M]          link
                  |                   ^
   physical       +------ bits -------+            physical
```

### The vocabulary — the four PDU names (examinable)

| Layer | Name of its unit (PDU) | Contents |
|---|---|---|
| Application | **message** | M |
| Transport | **segment** | Ht + M |
| Network | **datagram** | Hn + Ht + M |
| Link | **frame** | Hl + Hn + Ht + M |

### The Matryoshka doll analogy

Encapsulation works exactly like **Russian nesting dolls** (matryoshka / babushka dolls): message → segment → datagram → frame. Each layer's unit is wrapped inside the next layer's unit, and unwrapped in reverse order at the destination.

---

### 3.5 Logical vs physical communication

**Logical communication — each layer interacts with its *peer's* corresponding layer:**

```
   Application  <------------------------------>  Application
    Transport   <------------------------------>   Transport
     Network    <----->  Network   <----------->    Network
     Datalink   <----->  Datalink  <----------->    Datalink
     Physical   <----->  Physical  <----------->    Physical

      Host A              Router                     Host B
```

**Physical communication — data actually goes *down* to the physical network, then *up* to the relevant layer:**

```
   Application                                  Application
    Transport                                    Transport
     Network  ------->  Network  ------->         Network
     Datalink ------->  Datalink ------->         Datalink
     Physical ------->  Physical ------->         Physical

      Host A             Router                     Host B
```

> **The critical observation:** the **router only goes up to the network layer.** It does not need transport or application layers, because its job is only to forward datagrams. This is why "complexity is at the network's edge."

---

### 3.6 Protocols at different layers

| Layer | Name | Protocols |
|---|---|---|
| **L7** | Application | SMTP, HTTP, DNS, NTP |
| **L4** | Transport | **TCP**, **UDP** |
| **L3** | Network | **IP** |
| **L2** | Data link | Ethernet, FDDI, PPP |
| **L1** | Physical | optical, copper, radio, PSTN/DSL |

> 🔑 **There is just one network-layer protocol!**
> This is the famous **"narrow waist"** of the Internet: many choices above (apps and transports), many choices below (link and physical technologies), but exactly **one IP** in the middle. Everything must speak IP, and that is what makes the Internet a single network rather than many.

---

### 3.7 Encapsulation: an end-to-end view

```
   SOURCE                 SWITCH                ROUTER            DESTINATION
   application                                                    application
     |  M                                                             ^ M
   transport                                                      transport
     | [Ht|M]                                                        ^ [Ht|M]
   network                                       network          network
     | [Hn|Ht|M]                             [Hn|Ht|M]              ^ [Hn|Ht|M]
   link              link                       link               link
     | [Hl|Hn|Ht|M]   [Hl|Hn|Ht|M]          [Hl|Hn|Ht|M]            ^
   physical         physical                  physical            physical
```

**What each device does:**

| Device | Highest layer it processes | What it does |
|---|---|---|
| **Switch** | **Link (L2)** | Strips and rebuilds the **frame** header only. Never looks at Hn. |
| **Router** | **Network (L3)** | Strips the frame, examines the **datagram** header Hn to make a forwarding decision, then re-encapsulates in a new frame. |
| **Host** | **Application (L5/L7)** | Peels all the way up to M. |

---

## 4. Chapter 1 summary

Everything covered in Chapter 1:

- Internet overview
- What's a protocol?
- Network edge, access network, core
  - Packet-switching versus circuit-switching
  - Internet structure
- Performance: loss, delay, throughput
- Layering, service models
- Security
- History

**You now have:** context, overview, vocabulary and a "feel" for networking. More depth and detail follows.

---

## 5. Additional slides

### 5.1 The ISO/OSI reference model — seven layers

```
   +----------------+
   |  application   |
   +----------------+
   |  presentation  |  <-- NOT in the Internet stack
   +----------------+
   |    session     |  <-- NOT in the Internet stack
   +----------------+
   |   transport    |
   +----------------+
   |    network     |
   +----------------+
   |      link      |
   +----------------+
   |    physical    |
   +----------------+
```

**Two layers not found in the Internet protocol stack:**

| Layer | Purpose |
|---|---|
| **Presentation** | Allows applications to **interpret the meaning of data** — e.g. **encryption, compression, machine-specific conventions** |
| **Session** | **Synchronisation, checkpointing, recovery** of data exchange |

**The Internet stack is "missing" these layers.** These services, **if needed, must be implemented in the application**.

The deck poses the open question: **are they needed?** (The Internet's answer, in practice: not as separate layers — TLS does encryption at the application layer, and HTTP cookies do session state.)

> **Exam mapping:** OSI 7 layers = Internet 5 layers + presentation + session, inserted between application and transport.

---

### 5.2 Wireshark

```
   [ application: www browser, email client ]
                     |
          +----------+----------+
          |     application     |
          |         OS          |
   packet |  Transport (TCP/UDP)|
  analyzer|     Network (IP)    |
     ^    |   Link (Ethernet)   |
     |    |      Physical       |
  [ packet capture (pcap) ] <--- copy of ALL Ethernet frames sent/received
```

**How it works:** the **packet capture (pcap)** library takes a **copy of all Ethernet frames sent/received**, and the **packet analyzer** decodes and displays them layer by layer.

---

## 📐 Formula summary — Lecture 04

This lecture is **conceptual — there are no new numerical formulas.** The only quantitative content is the BDP recap:

| # | Formula | Meaning |
|---|---|---|
| 1 | $$\text{BDP} = \text{Bandwidth} \times \text{RTT}$$ | Bits transmitted but not yet acknowledged (see Lecture 03 §13 for worked examples) |

**Structural "formulas" to memorise instead:**

| Concept | The thing to memorise |
|---|---|
| Internet stack (top→bottom) | **A**pplication, **T**ransport, **N**etwork, **L**ink, **P**hysical |
| OSI stack (top→bottom) | Application, **Presentation**, **Session**, Transport, Network, Link, Physical |
| Encapsulation chain | M → `[Ht\|M]` → `[Hn\|Ht\|M]` → `[Hl\|Hn\|Ht\|M]` |
| PDU names | message → segment → datagram → frame |
| Device depth | Switch = L2, Router = L3, Host = all layers |
| DoS categories | Vulnerability / Bandwidth flooding / Connection flooding |

---

## Key takeaways

- The Internet's security problems are **architectural**: it was built for mutually trusting users, and security is retrofitted at every layer.
- Learn the **botnet vocabulary chain** (malware → bot/zombie → botnet → bot-herder → command-and-control) and the **three DoS categories** — these are reliable short-answer questions.
- **Sniffing** = passive read attack; **spoofing** = active write attack.
- **Layering** exists for two reasons: explicit structure for discussion, and modularity so a change in one layer is transparent to the rest.
- **Encapsulation** is the mechanism that makes layering work: each layer adds its own header, and the PDU names change accordingly (message/segment/datagram/frame).
- **There is exactly one network-layer protocol (IP)** — the narrow waist.
- **Routers stop at L3, switches stop at L2** — only hosts go all the way up.

---

## Course admin repeated in this lecture

- **Assignment #1 (Chapter 1)** — due **Thursday 3 September 2026**, handwritten hard copy, during the lecture.
- **Quiz #1 (Chapter 1)** — **Thursday 3 September 2026**, during lecture time, **with your own section only**. No retake.
# Lecture 05 — Application Layer: Principles, Sockets, and HTTP
**CS 3001 Computer Networks · 1 September 2026 · Chapter 2 begins**

---

## Index — Chapter 2 roadmap (as per the deck)

This is the index for all of Chapter 2 (Lectures 05–08):

1. **Principles of network applications**
2. **Web and HTTP**
3. E-mail, SMTP, IMAP
4. The Domain Name System (DNS)
5. P2P applications
6. Video streaming and content distribution networks
7. Socket programming with UDP and TCP

**Our goals for the chapter:**
- Conceptual and implementation aspects of application-layer protocols — transport-layer service models, client-server paradigm, peer-to-peer paradigm
- Learn about protocols by examining popular application-layer protocols and infrastructure: **HTTP, SMTP/IMAP, DNS, video streaming systems & CDNs**
- Programming network applications: the **socket API**

---

## Covered in this lecture

1. Some network apps
2. Creating a network app
3. Client-server and peer-to-peer architectures
4. Processes communicating; port numbers; addressing; sockets
5. Addressing in TCP/IP
6. What an application-layer protocol defines
7. What transport service does an app need?
8. Internet transport protocols: TCP and UDP; securing TCP with TLS
9. Web and HTTP: objects, URLs
10. HTTP overview; stateless; two connection types
11. Non-persistent HTTP and its response time
12. Persistent HTTP

---

## 1. Some network apps

Social networking, Web, text messaging, e-mail, multi-user network games, streaming stored video (YouTube, Hulu, Netflix), P2P file sharing, voice over IP (Skype), real-time video conferencing (Zoom), Internet search, remote login.

---

## 2. Creating a network app

**You write programs that:**
- Run on **(different) end systems**
- **Communicate over a network**
- E.g. web server software communicating with browser software

**You do NOT need to write software for network-core devices.**
- **Network-core devices do not run user applications.**
- Applications live on end systems, which allows for **rapid app development and propagation**.

```
   [ end system ]                          [ end system ]
   +------------+                          +------------+
   | application|  <-- you write this -->  | application|
   | transport  |                          | transport  |
   |  network   |                          |  network   |
   | data link  |                          | data link  |
   |  physical  |                          |  physical  |
   +------------+                          +------------+
         \                                      /
          \___ [router] ___ [router] ___ [router]
                  ^ only has network/link/physical
                  ^ NO application layer
```

> **This is the single most important architectural fact of Chapter 2:** all application intelligence sits at the edge. It is the reason a new app (say, a new chat protocol) can be deployed worldwide overnight without touching a single router.

---

## 3. Application architectures

### 3.1 Client-server paradigm

**Server:**
- **Always-on** host
- **Permanent IP address**
- Often in **data centers, for scaling**

**Clients:**
- Contact and communicate with the server
- May be **intermittently connected**
- May have **dynamic IP addresses**
- **Do not communicate directly with each other**

**Examples:** HTTP, IMAP, FTP

### 3.2 Peer-to-peer architecture

- **No always-on server**
- **Arbitrary end systems directly communicate**
- Peers request service from other peers, and **provide service in return** to other peers
- **Self-scalability** — new peers bring **new service capacity** as well as **new service demands**
- Peers are **intermittently connected and change IP addresses** → **complex management**

**Example:** P2P file sharing (BitTorrent)

> **Why self-scalability matters:** in client-server, every new user adds load and no capacity. In P2P, every new user adds *both*. This is why BitTorrent gets *faster* as a file becomes more popular, while a web server gets slower.

### 3.3 Network edge recap (client, server, peer)

Repeated from Lectures 01–02. See **Lecture 01 §9** for the full text. Examples of peers: **Skype, BitTorrent, Napster**.

---

## 4. Processes communicating

**Process:** a program running within a host.

- Within the **same host**, two processes communicate using **inter-process communication** (defined by the OS).
- Processes in **different hosts** communicate by **exchanging messages**.

**Clients and servers, at the process level:**
- **Client process:** the process that **initiates** communication
- **Server process:** the process that **waits to be contacted**

> **Note:** applications with **P2P architectures have both client processes and server processes** — the roles are per-*process*, not per-*machine*.

---

### 4.1 The key question: how do we distinguish between two or more processes running on the same host?

## → **Port Numbers**

---

### 4.2 Addressing processes

- To receive messages, a process must have an **identifier**.
- A host device has a unique **32-bit IP address**.

**Q: Does the IP address of the host on which a process runs suffice for identifying the process?**
**A: No** — many processes can be running on the same host.

**Therefore: the identifier includes BOTH the IP address AND the port number** associated with the process on that host. That combination is a **socket**.

**Example port numbers:**

| Service | Port |
|---|---|
| HTTP server | **80** |
| Mail server (SMTP) | **25** |
| HTTPS | **443** |

**To send an HTTP message to the gaia.cs.umass.edu web server:**
- IP address: `128.119.245.12`
- Port number: `80`

---

### 4.3 Process ID (PID) vs Port Number — the distinction

| | **Process ID (PID)** | **Port Number** |
|---|---|---|
| What it is | A unique identifier assigned to a **running process in an operating system** | A **communication endpoint in a network** |
| Used for | **Tracking and managing** individual processes; managing resources; allowing interaction with the process | Identifying a **specific process or service** so that network communication can reach it |
| Scope | Specific to a **single operating system instance** | Used for network communication **between multiple systems** |

**The worked scenario from the deck:**

1. A web server runs on your computer. The OS assigns it a **PID** to track it and manage its resources.
2. When the web server process starts, it **binds itself to a port number** (e.g. port 80). It will now listen for incoming requests on that port.
3. A client (web browser) initiates a connection to **your computer's IP address, on port 80**.
4. The OS receives the incoming request on port 80 and **forwards it to the web server process associated with that port**.
5. The web server process — identified internally by its PID — receives the request, processes it, and sends back the webpage over the same connection.

**In summary:** the **PID identifies the running instance** of the process on your computer; the **port number allows incoming network requests to be directed to the correct process**.

---

### 4.4 Sockets

**A socket = a combination of port number and IP address.**

- A process **sends/receives messages to/from its socket**.
- A socket is **analogous to a door**:
  - The sending process **shoves the message out the door**.
  - The sending process **relies on the transport infrastructure on the other side of the door** to deliver the message to the socket at the receiving process.
  - **Two sockets are involved: one on each side.**

### Diagram — the socket as a door

```
   +-------------+                          +-------------+
   | application |                          | application |
   |   process   |                          |   process   |   <-- controlled by
   +-----[door]--+                          +--[door]-----+       app developer
   |  transport  |                          |  transport  |
   |   network   | <===== Internet =====>   |   network   |   <-- controlled
   |     link    |                          |     link    |       by the OS
   |   physical  |                          |   physical  |
   +-------------+                          +-------------+
```

**The dividing line:** everything **above** the socket is controlled by the **application developer**. Everything **below** the socket is controlled by the **OS**. The application developer's only choice below the line is *which transport protocol to use* (TCP or UDP) and a few parameter settings.

---

## 5. Addressing in TCP/IP

### Diagram — the three address types

```
                    +-------------+
                    |  Addresses  |
                    +-------------+
                           |
        +------------------+------------------+
        |                  |                  |
  +-----------+      +-----------+      +-----------+
  | Physical  |      |    IP     |      |   Port    |
  |  address  |      |  address  |      |  number   |
  +-----------+      +-----------+      +-----------+
```

### TCP/IP layers and their addresses

```
   +-----------------------------+
   | Application  |  Processes   |
   +-----------------------------+
   | Transport    | TCP  |  UDP  |  ===>  [ Port address ]
   +-----------------------------+
   | Network      | IP and other |  ===>  [ IP address ]
   |              |  protocols   |
   +-----------------------------+
   | Data link    |  Underlying  |
   |              |  physical    |  ===>  [ Physical address ]
   | Physical     |  networks    |
   +-----------------------------+
```

| Layer | Address type | Identifies |
|---|---|---|
| **Transport** | **Port address** | Which **process** on the host |
| **Network** | **IP address** | Which **host** on the Internet |
| **Data link / Physical** | **Physical (MAC) address** | Which **interface** on the local network |

> **Read it as a postal address narrowing down:** physical address = which house on the street; IP address = which house globally; port number = which person inside the house.

---

## 6. An application-layer protocol defines:

1. **Types of messages exchanged** — e.g. request, response
2. **Message syntax** — what fields are in messages, and how the fields are delineated
3. **Message semantics** — the meaning of the information in the fields
4. **Rules for when and how processes send and respond to messages**

### Open vs proprietary protocols

| **Open protocols** | **Proprietary protocols** |
|---|---|
| Defined in **RFCs** — everyone has access to the protocol definition | Definition is **not public** |
| Allows for **interoperability** | Locked to one vendor |
| E.g. **HTTP, SMTP** | E.g. **Skype, Zoom** |

---

## 7. What transport service does an app need?

Four dimensions:

### Data integrity
- Some apps (file transfer, web transactions) require **100% reliable** data transfer.
- Other apps (audio) can **tolerate some loss**.

### Timing
- Some apps (Internet telephony, interactive games) require **low delay** to be "effective".

### Throughput
- Some apps (multimedia) require a **minimum amount of throughput** to be "effective".
- Other apps — **"elastic apps"** — make use of **whatever throughput they get**.

### Security
- Encryption, data integrity, etc.

---

### Transport service requirements of common apps

| Application | Data loss | Throughput | Time sensitive? |
|---|---|---|---|
| File transfer/download | **No loss** | Elastic | No |
| E-mail | **No loss** | Elastic | No |
| Web documents | **No loss** | Elastic | No |
| Real-time audio/video | **Loss-tolerant** | audio: 5 Kbps–1 Mbps; video: 10 Kbps–5 Mbps | **Yes, 10's of msec** |
| Streaming audio/video | **Loss-tolerant** | Same as above | **Yes, few secs** |
| Interactive games | **Loss-tolerant** | Kbps+ | **Yes, 10's msec** |
| Text messaging | **No loss** | Elastic | **Yes and no** |

> **Read the table by column:** notice that *only* the multimedia rows tolerate loss, and *only* the real-time rows demand tens-of-milliseconds timing. That pattern is exactly what drives the TCP/UDP choice in the next section.

---

## 8. Internet transport protocol services

### TCP service
- **Reliable transport** between sending and receiving process
- **Flow control:** the sender won't overwhelm the receiver
- **Congestion control:** throttles the sender when the network is overloaded
- **Connection-oriented:** setup required between client and server processes
- **Does NOT provide:** timing, minimum throughput guarantee, security

### UDP service
- **Unreliable data transfer** between sending and receiving process
- **Does NOT provide:** reliability, flow control, congestion control, timing, throughput guarantee, security, **or connection setup**

**Q: Why bother? Why is there a UDP?**
Because *everything* TCP adds costs time. For an app that would rather drop a frame than wait for a retransmission (voice, video, games), TCP's guarantees are a liability, not a feature.

---

### Internet applications and their transport protocols

| Application | Application-layer protocol | Transport protocol |
|---|---|---|
| File transfer/download | FTP [RFC 959] | **TCP** |
| E-mail | SMTP [RFC 5321] | **TCP** |
| Web documents | HTTP [RFC 7230, 9110] | **TCP** |
| Internet telephony | SIP [RFC 3261], RTP [RFC 3550], or proprietary | **TCP or UDP** |
| Streaming audio/video | HTTP [RFC 7230], DASH | **TCP** |
| Interactive games | WOW, FPS (proprietary) | **UDP or TCP** |

---

## 9. Securing TCP

### The problem with vanilla TCP & UDP sockets
- **No encryption**
- **Cleartext passwords** sent into the socket traverse the Internet **in cleartext (!)**

### Transport Layer Security (TLS)
Provides:
- **Encrypted TCP connections**
- **Data integrity**
- **End-point authentication**

### Where TLS lives

**TLS is implemented in the application layer.**
- Apps use **TLS libraries**, which **use TCP in turn**.
- Cleartext sent into the "socket" **traverses the Internet encrypted**.

```
   +------------------+
   |   application    |
   |   [ TLS library ]|  <-- encryption happens HERE
   +------------------+
   |       TCP        |  <-- TCP itself is unchanged
   +------------------+
```

*(More in Chapter 8.)*

---

## 10. Web and HTTP

### Quick review of terms

- A **web page** consists of **objects**, each of which can be stored on **different web servers**.
- An **object** can be an HTML file, a JPEG image, a Java applet, an audio file, etc.
- A web page consists of a **base HTML file** which **includes several referenced objects**, each addressable by a **URL**.

```
   www.someschool.edu / someDept/pic.gif
   \_______________/   \_____________/
      host name           path name
```

---

### 10.1 Uniform Resource Locator (URL)

```
protocol://host-name[:port]/directory-path/resource
```

| Part | Meaning |
|---|---|
| **protocol** | http, ftp, https, smtp, rtsp, etc. |
| **hostname** | DNS name (domain name), or IP address |
| **port** | Defaults to the protocol's standard port — **http: 80**, **https: 443** |
| **directory path** | Hierarchical, reflecting the file system on the server side |
| **resource** | Identifies the desired resource |

---

## 11. HTTP overview

**HTTP = HyperText Transfer Protocol** — the Web's application-layer protocol.

**Client/server model:**
- **Client:** the browser that **requests, receives (using HTTP) and "displays"** Web objects
- **Server:** the Web server that **sends (using HTTP) objects in response** to requests

```
   [ PC running Firefox ]  ----\
                                \
                                 ---->  [ server running Apache Web server ]
                                /
   [ iPhone running Safari ]---/
```

### HTTP uses TCP

1. Client **initiates a TCP connection** (creates a socket) to the server, **port 80**
2. Server **accepts the TCP connection** from the client
3. **HTTP messages** (application-layer protocol messages) are **exchanged** between the browser (HTTP client) and the Web server (HTTP server)
4. **TCP connection closed**

### HTTP is "stateless"

**The server maintains no information about past client requests.**

> **Aside — why statelessness is a design win:** protocols that maintain "state" are complex.
> - Past history (state) must be maintained.
> - If the server or client **crashes**, their views of "state" may be **inconsistent** and must be **reconciled**.
>
> By keeping HTTP stateless, none of this recovery machinery is needed. (Lecture 06 shows how **cookies** put state back in, without changing the protocol.)

---

## 12. HTTP connections: two types

| **Non-persistent HTTP (1.0)** | **Persistent HTTP (1.1)** |
|---|---|
| 1. TCP connection opened | TCP connection opened to a server |
| 2. **At most one object** sent over the TCP connection | **Multiple objects** can be sent over a **single** TCP connection between client and that server |
| 3. TCP connection closed | TCP connection closed (later) |
| Downloading multiple objects **required multiple connections** | |

---

### 12.1 Non-persistent HTTP — the six-step example

**Scenario:** user enters URL `www.someSchool.edu/someDepartment/home.index`, which contains text plus references to **10 JPEG images**.

**1a.** HTTP client **initiates a TCP connection** to the HTTP server process at `www.someSchool.edu` on **port 80**.

**1b.** HTTP server at `www.someSchool.edu`, **waiting for a TCP connection at port 80**, "accepts" the connection and **notifies the client**.

**2.** HTTP client **sends an HTTP request message** (containing the URL) into the TCP connection socket. The message indicates that the client wants the object `someDepartment/home.index`.

**3.** HTTP server **receives the request message**, **forms a response message** containing the requested object, and **sends the message into its socket**.

**4.** HTTP server **closes the TCP connection**.

**5.** HTTP client **receives the response message** containing the HTML file, **displays the HTML**. While **parsing the HTML file**, it **finds 10 referenced JPEG objects**.

**6.** **Steps 1–5 are repeated for each of the 10 JPEG objects.**

```
   client                              server
     |---- 1a. TCP connection req ----->|  1b. accept
     |<--- TCP connection response -----|
     |---- 2. HTTP GET home.index ----->|  3. form response
     |<--- HTTP response (HTML) --------|
     |                              4. close TCP
     |  5. parse HTML, find 10 JPEGs
     |
     |  6. repeat ALL of the above, 10 more times
     v time
```

---

### 12.2 Non-persistent HTTP: response time

**RTT (definition):** the **time for a small packet to travel from client to server and back**.

**HTTP response time, per object, breaks into three parts:**
1. **One RTT** to initiate the TCP connection
2. **One RTT** for the HTTP request and the first few bytes of the HTTP response to return
3. **Object/file transmission time**

```
   client                              server
     |--- initiate TCP connection --->|
     |<-------------------------------|   } RTT #1
     |--- request file -------------->|
     |<-------------------------------|   } RTT #2
     |<==== file being transmitted ===|   } transmission time
     |  file received
     v time
```

### 📐 Formula

$$
\text{Non-persistent HTTP response time (per object)} = 2 \cdot RTT + \text{file transmission time}
$$

Where file transmission time = **L/R** (the familiar d_trans).

### 🔢 Extending it to a full page

For a base HTML file **plus N referenced objects**, fetched one at a time over non-persistent HTTP:

$$
\text{Total} = (N + 1) \times \left(2 \cdot RTT + \frac{L}{R}\right)
$$

**Worked mini-example.** Suppose RTT = 100 ms, the page has a base HTML file + 10 objects, and transmission time per object is negligible.

**Step 1 — cost per object.** 2 × 100 ms = 200 ms.
**Step 2 — number of objects.** 1 base file + 10 images = 11 objects.
**Step 3 — multiply.** 11 × 200 ms = **2200 ms = 2.2 seconds** — just in round trips, before a single byte of actual content is counted.

That number is the entire motivation for persistent HTTP.

---

### 12.3 Non-persistent HTTP shortcomings

- Most web pages have **multiple objects** — e.g. an HTML file and a bunch of embedded images.
- Retrieving them naively means **one item at a time**.
- A **brand new TCP connection per requested object** — even for a small object. This means **significant TCP resources must be allocated at both server and client side (TCP buffers)**.
- **Burden on web servers**, which are servicing multiple simultaneous clients.
- Each object suffers a delivery delay of **2 RTTs** (one to establish the TCP connection, one to request and receive the object).

---

## 13. Persistent HTTP (HTTP 1.1)

**Non-persistent HTTP issues, restated:**
- Requires **2 RTTs per object**
- **OS overhead** for each TCP connection
- Browsers often open **multiple parallel TCP connections** to fetch referenced objects in parallel (a workaround, not a fix)

**Persistent HTTP (HTTP/1.1):**
- Server **leaves the connection open** after sending a response
- **Subsequent HTTP messages** between the same client/server are sent **over the open connection**
- Client **sends requests as soon as it encounters a referenced object** → this is the **non-pipelined** mode
- **As little as ONE RTT for all the referenced objects** → this is the **pipelined** mode, **cutting response time in half**

### Comparison of the three modes

| Mode | RTTs for base file + N objects |
|---|---|
| **Non-persistent** | (N+1) × 2 RTT |
| **Persistent, non-pipelined** | 1 RTT (setup) + (N+1) × 1 RTT |
| **Persistent, pipelined** | 1 RTT (setup) + 1 RTT (base) + **1 RTT for all N objects** |

> Using the earlier numbers (RTT = 100 ms, 10 objects): non-persistent ≈ 2200 ms, persistent non-pipelined ≈ 1200 ms, persistent pipelined ≈ **300 ms**.

---

## 📐 Formula summary — Lecture 05

| # | Formula | Meaning |
|---|---|---|
| 1 | $$\text{socket} = (\text{IP address},\ \text{port number})$$ | The identifier a process needs to receive messages |
| 2 | $$RTT = \text{round-trip time for a small packet, client}\to\text{server}\to\text{client}$$ | Definition |
| 3 | $$t_{\text{non-persistent, per object}} = 2 \cdot RTT + \frac{L}{R}$$ | One RTT for TCP setup + one RTT for request/first bytes + transmission time |
| 4 | $$t_{\text{non-persistent, page}} = (N+1)\left(2 \cdot RTT + \frac{L}{R}\right)$$ | Base HTML file + N referenced objects |
| 5 | $$t_{\text{persistent, pipelined}} \approx 1\,RTT_{\text{setup}} + 1\,RTT_{\text{base}} + 1\,RTT_{\text{all objects}} + \sum \frac{L}{R}$$ | "As little as one RTT for all referenced objects" |

**Standard port numbers to memorise:**

| Service | Port |
|---|---|
| HTTP | **80** |
| HTTPS | **443** |
| SMTP (mail server) | **25** |

**IP address size: 32 bits.**

---

## Key takeaways

- **Applications run only at the edge** — network-core devices have no application layer. This is why the Internet can evolve so fast.
- **Client-server** scales by adding servers; **P2P** is **self-scaling** because every new peer adds capacity as well as demand.
- A process is addressed by a **socket = (IP address, port number)**. IP finds the *host*; port finds the *process*.
- **PID ≠ port**: PID is OS-internal bookkeeping; port is the network-visible endpoint.
- Apps differ along four axes — **data integrity, timing, throughput, security** — and that determines whether they choose **TCP** (reliable, flow- and congestion-controlled, connection-oriented) or **UDP** (none of the above, but no setup cost either).
- **TLS runs in the application layer**, not in TCP.
- **HTTP is stateless**, uses **TCP port 80**, and comes in **non-persistent (1.0)** and **persistent (1.1)** flavours. Non-persistent costs **2 RTT per object**; persistent + pipelining gets a whole page in roughly **one extra RTT**.
# Lecture 06 — HTTP Messages, Cookies, and Web Caching
**CS 3001 Computer Networks · 3 September 2026 · Chapter 2**

---

## Index (as per the deck)

1. Types of HTTP messages
2. HTTP request message — general format
3. HTTP request message — worked example
4. Other HTTP request methods: POST, HEAD, PUT, GET-with-data
5. HTTP response message
6. HTTP response status codes
7. Trying out HTTP for yourself (netcat)
8. How does a stateless protocol keep state? → **Cookies**
9. Cookies: four components, example, uses, privacy
10. Cookie-based tracking; first-party vs third-party cookies; GDPR
11. Web caches (proxy servers)
12. **Caching example — the full numerical**
13. Problem with web caches: stale objects
14. Browser caching: the Conditional GET

---

## 1. Types of HTTP messages

There are exactly **two**:

| Type | Direction |
|---|---|
| **HTTP Request** | Client → Server |
| **HTTP Response** | Server → Client |

---

## 2. HTTP request message — general format

```
 +---------+--+-----+--+---------+--+--+
 | method  |sp| URL |sp| version |cr|lf|   <-- REQUEST LINE
 +---------+--+-----+--+---------+--+--+
 | header field name | value |cr|lf|        }
 +-------------------+-------+--+--+        }  HEADER LINES
 |        ~ ~ ~ (more headers) ~ ~ ~ |      }
 +-------------------+-------+--+--+        }
 | header field name | value |cr|lf|        }
 +-------------------+-------+--+--+
 |cr|lf|                                    <-- BLANK LINE = end of headers
 +--+--+
 |        entity body (OPTIONAL)       |    <-- BODY
 +-------------------------------------+
```

**Where:**
- `sp` = space
- `cr` = carriage return character
- `lf` = line-feed character
- `\r\n` = cr + lf, the line terminator

**The three parts (client-to-server communication):**

| Part | Contains |
|---|---|
| **Request line** | **method**, **resource**, and **protocol version** |
| **Request headers** | Provide information or **modify the request** |
| **Body** | **Optional** data (e.g. to POST data to the server) |

---

## 3. HTTP request message — worked example

HTTP request messages are in **ASCII (human-readable format)**.

```
GET /index.html HTTP/1.1\r\n                           <-- request line
Host: www-net.cs.umass.edu\r\n                         }
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X     }
  10.15; rv:80.0) Gecko/20100101 Firefox/80.0 \r\n     }
Accept: text/html,application/xhtml+xml\r\n            }  header lines
Accept-Language: en-us,en;q=0.5\r\n                    }
Accept-Encoding: gzip,deflate\r\n                      }
Connection: keep-alive\r\n                             }
\r\n                                                   <-- carriage return +
                                                           line feed at the
                                                           START of a line
                                                           indicates END of
                                                           header lines
```

**A second, simpler example from the deck:**

```
GET /somedir/page.html HTTP/1.1      <-- request line
Host: www.someschool.edu             }
User-agent: Mozilla/4.0              }  header lines
Connection: close                    }
Accept-language: fr                  }
(blank line)                         <-- CRLF indicates end of message
```

> **Reading the headers:** `Host` names the server (needed because one IP can host many sites). `Connection: close` asks for non-persistent behaviour; `keep-alive` asks for persistent. `Accept-*` headers tell the server what formats, languages and encodings the client can handle.

---

## 4. HTTP request methods

### GET
The normal "fetch me this object" method.

**GET can also send data to the server:** include user data in the **URL field** of the HTTP GET request message, **following a `?`**:

```
www.somesite.com/animalsearch?monkeys&banana
```

### POST
**Used to send data to the server in the entity body.**
- A web page often includes **form input**.
- User input is sent from client to server in the **entity body** of the HTTP POST request message.

### HEAD
**Requests the headers (only)** that would be returned if the specified URL were requested with an HTTP GET.
- **Identical to HTTP GET, except the server will not return a message body** as part of the HTTP response.
- It sends **only the HTTP headers** and ends immediately after the headers section.
- *Use case:* checking whether a resource exists, or whether it has changed, without downloading it.

### PUT
**Used to send data to the server.**
- **Uploads a new file (object) to the server in place of an existing one.**
- **Completely replaces** the file that exists at the specified URL with the content in the entity body of the request message.

### 🔑 POST vs PUT — the exam one-liner

> **POST is used for CREATING new resources, while PUT is used for UPDATING existing resources.**

### Summary table

| Method | Where the data goes | Purpose |
|---|---|---|
| **GET** | In the **URL**, after `?` | Retrieve an object (can carry small data) |
| **POST** | In the **entity body** | **Create** a new resource / submit form data |
| **HEAD** | — (no data) | Get **headers only**, no body |
| **PUT** | In the **entity body** | **Update/replace** the resource entirely |

---

## 5. HTTP response message

```
HTTP/1.1 200 OK                                     <-- STATUS LINE
Date: Tue, 08 Sep 2020 00:53:20 GMT                 }
Server: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips   }
  PHP/7.4.9 mod_perl/2.0.11 Perl/v5.16.3            }
Last-Modified: Tue, 01 Mar 2016 18:57:50 GMT        }  header lines
ETag: "a5b-52d015789ee9e"                           }
Accept-Ranges: bytes                                }
Content-Length: 2651                                }
Content-Type: text/html; charset=UTF-8              }
\r\n                                                <-- blank line
data data data data data ...                        <-- DATA (e.g. the
                                                        requested HTML file)
```

**The three parts (server-to-client communication):**

| Part | Contains |
|---|---|
| **Status line** | **protocol version**, **status code**, **status phrase** |
| **Response headers** | Provide information |
| **Body** | **Optional** data — e.g. the requested HTML file |

**A second example from the deck:**

```
HTTP/1.1 200 OK                          <-- status line
Connection close                         }
Date: Thu, 06 Aug 2006 12:00:15 GMT      }
Server: Apache/1.3.0 (Unix)              }  header lines
Last-Modified: Mon, 22 Jun 2006 ...      }
Content-Length: 6821                     }
Content-Type: text/html                  }
(blank line)
data data data data data ...             <-- e.g. requested HTML file
```

**Useful headers to recognise:**
- `Content-Length` — size of the body in bytes
- `Content-Type` — MIME type of the body
- `Last-Modified` — used by the **conditional GET** (§14)

---

## 6. HTTP response status codes

The status code appears in the **1st line** in the server-to-client response message.

| Code | Phrase | Meaning |
|---|---|---|
| **200** | **OK** | Request succeeded; the requested object is later in this message |
| **301** | **Moved Permanently** | Requested object moved; the new location is specified later in this message, in the **`Location:` field** |
| **400** | **Bad Request** | Request message **not understood** by the server |
| **404** | **Not Found** | Requested document **not found** on this server |
| **505** | **HTTP Version Not Supported** | — |

> **The class digit tells you who is at fault:** 2xx = success, 3xx = redirection, **4xx = client error**, **5xx = server error**.

---

## 7. Trying out HTTP (client side) for yourself

**Step 1 — netcat to a web server:**

```
% nc -c -v gaia.cs.umass.edu 80
```
- Opens a **TCP connection to port 80** (the default HTTP server port) at `gaia.cs.umass.edu`
- Anything typed in will be **sent to port 80** at that host

**Step 2 — type in a GET HTTP request:**

```
GET /kurose_ross/interactive/index.php HTTP/1.1
Host: gaia.cs.umass.edu
```
- By typing this in and **hitting carriage return twice**, you send this **minimal but complete** GET request to the HTTP server.
- *(The second CRLF is what signals "end of headers" — without it the server keeps waiting.)*

**Step 3 — look at the response message** sent by the HTTP server. (Or use **Wireshark** to look at a captured HTTP request/response.)

---

## 8. Question: How does a stateless protocol keep state?

## → **Cookies**

---

## 9. Maintaining user/server state: cookies

**Recall:** the HTTP GET/response interaction is **stateless**.
- **No notion of multi-step exchanges** of HTTP messages to complete a web "transaction"
- No need for client/server to **track the "state"** of a multi-step exchange
- **All HTTP requests are independent of each other**
- No need for client/server to **"recover" from a partially-completed-but-never-completely-completed transaction**

**Contrast with a stateful protocol:** the client makes two changes to X, **or none at all**. The awkward question a stateful protocol must answer: *what happens if the network connection or the client crashes at time t′, halfway through?* That is exactly the complexity HTTP avoids.

---

### 9.1 The four components of cookies

Web sites and the client browser use **cookies** to maintain some state between transactions.

1. **Cookie header line of the HTTP response message** (from server to client)
2. **Cookie header line in the next HTTP request message** (from client to server)
3. **Cookie file kept on the user's host**, managed by the user's browser
4. **Back-end database at the web site**

---

### 9.2 Worked example — Susan / Amazon

**Setup:** Susan uses a browser on her laptop and visits a specific e-commerce site **for the first time**.

**When the initial HTTP request arrives at the site, the site creates:**
- A **unique ID (a.k.a. "cookie")**
- An **entry in the back-end database** for that ID

**Subsequent HTTP requests** from Susan to this site **will contain the cookie ID value**, allowing the site to **"identify" Susan**.

### Diagram — the full cookie exchange over time

```
 CLIENT                                          AMAZON SERVER
 cookie file
 +-----------+
 | ebay 8734 |  ---- usual HTTP request msg ---->  server creates
 +-----------+                                     ID 1678 for user
                                                          |
             <--- usual HTTP response          create entry in
                  set-cookie: 1678  ----             backend database
 +-------------+
 | ebay 8734   |
 | amazon 1678 |  --- usual HTTP request msg --->
 +-------------+      cookie: 1678               access DB ->
                                                 cookie-specific action
             <--- usual HTTP response msg ----

 ===== one week later =====

 +-------------+
 | ebay 8734   |  --- usual HTTP request msg --->
 | amazon 1678 |      cookie: 1678               access DB ->
 +-------------+                                 cookie-specific action
             <--- usual HTTP response msg ----
        v time                                        v time
```

> **The crucial point:** HTTP itself is *still* stateless. Every request is still independent. The state lives in the **cookie file on the client** and the **database on the server** — the protocol just carries an identifier between them.

---

### 9.3 What cookies can be used for

- **Authorization**
- **Shopping carts**
- **Recommendations**
- **User session state** (e.g. web e-mail)

### The challenge: how to keep state?

Two answers, and cookies use the second:
1. **At protocol endpoints:** maintain state at sender/receiver over multiple transactions
2. **In messages:** **cookies in HTTP messages carry state**

### Aside — cookies and privacy

- Cookies permit sites to **learn a lot about you on their site**.
- **Third-party persistent cookies (tracking cookies)** allow a **common identity (cookie value) to be tracked across multiple web sites**.

---

## 10. Cookie-based tracking

### 10.1 Example: displaying a NY Times web page

```
 1  GET base html file from nytimes.com
 2  <-- reply
 3  (browser finds an embedded ad reference)
 4  GET ad from AdX.com
 5  <-- reply
 6
 7  display composed page  (NY Times page WITH embedded ad displayed)
```

The browser makes requests to **two different servers** to build **one** page. That is the hook that third-party tracking uses.

---

### 10.2 First-party vs third-party cookies

```
                  nytimes.com (sports)
   USER  --HTTP GET-->                     first-party cookie
         <--HTTP reply, Set-cookie: 1634-- (from the website you CHOSE
                                            to visit — it provides the
   browser stores: NY Times: 1634           base HTML file)
                        |
                        |  (embedded ad triggers a second GET)
                        v
         --HTTP GET, Referrer: NY Times Sports-->  AdX.com
         <--HTTP reply, Set-cookie: 7493--------
                                                  third-party cookie
   browser stores: AdX: 7493                      (from a website you did
                                                   NOT choose to visit)

   AdX's database now holds:  7493: NY Times sports, 2/15/22
```

| | **First-party cookie** | **Third-party cookie** |
|---|---|---|
| Set by | The website you **chose to visit** (provides the base HTML file) | A website you **did not choose to visit** |
| Example | nytimes.com sets cookie 1634 | AdX.com sets cookie 7493 |
| Tracks | Behaviour **on that one site** | Behaviour **across many sites** |

---

### 10.3 How tracking accumulates — three snapshots from the deck

**Snapshot 1 — visiting nytimes.com sports (15 Feb)**
- nytimes.com stores `1634: sports, 2/15/22`
- AdX.com stores `7493: NY Times sports, 2/15/22`

**Snapshot 2 — visiting socks.com (16 Feb)**
socks.com also carries AdX ads. The browser sends `cookie: 7493` with `Referrer: socks.com` to AdX.
- AdX.com now stores:
  - `7493: NY Times sports, 2/15/22`
  - `7493: socks.com, 2/16/22`

**AdX therefore:**
- **Tracks your web browsing over all sites that carry AdX ads**
- **Can return targeted ads based on browsing history**

**Snapshot 3 — one day later, visiting nytimes.com arts (17 Feb)**
- nytimes.com stores `1634: sports, 2/15/22` and `1634: arts, 2/17/22`
- AdX.com now stores three entries — and **returns an ad for socks!** on the NY Times page.

### What cookies can be used to do — the summary slide

- Track user behaviour on a given website → **first-party cookies**
- Track user behaviour **across multiple websites** → **third-party cookies**, **without the user ever choosing to visit the tracker site (!)**
- **Tracking may be invisible to the user:** rather than a *displayed* ad triggering the HTTP GET to the tracker, it could be an **invisible link**.

**Third-party tracking via cookies:**
- **Disabled by default in Firefox and Safari** browsers
- **To be disabled in Chrome** browser in 2023

---

### 10.4 GDPR and cookies

The EU **General Data Protection Regulation**, **recital 30 (May 2018)**, in summary: natural persons may be associated with online identifiers such as IP addresses and cookie identifiers; these leave traces which, combined with unique identifiers and other server-held information, may be used to **create profiles of natural persons and identify them**.

### 🔑 The consequence

> **When cookies can identify an individual, cookies are considered personal data, subject to GDPR personal data regulations.**
>
> The result: the user has **explicit control over whether or not cookies are allowed** — which is why every site now shows you a cookie consent banner.

---

## 11. Web caches (proxy servers)

**Goal:** satisfy client requests **without involving the origin server**.

**How it works:**
- The user **configures their browser to point to a (local) web cache**.
- The browser sends **all HTTP requests to the cache**.
  - **If the object is in the cache:** the cache **returns the object to the client**.
  - **Else:** the cache **requests the object from the origin server**, **caches the received object**, then **returns the object to the client**.

```
   [ client ] ---\
                  \
                   [ WEB CACHE ] <---- Internet ----> [ ORIGIN SERVER ]
                  /
   [ client ] ---/
```

### The dual role

> **A web cache acts as BOTH client and server:**
> - **Server** for the original requesting client
> - **Client** to the origin server
>
> The server tells the cache about the object's **allowable caching in the response header**.

### Why web caching?

1. **Reduce response time for client requests** — the cache is **closer to the client**
2. **Reduce traffic on an institution's access link**
3. **The Internet is dense with caches** — this **enables "poor" content providers to more effectively deliver content**

---

## 12. 🔢 The caching example — full numerical

### Scenario (constant for all three parts)

```
                                                    +-----------------+
   [ hosts ]                                        |     public      |
       |                                            |    Internet     |
   1 Gbps LAN                                       |                 |
       |                                            +--------+--------+
  [ institutional network ]                                  |
       |                                              origin servers
  1.54 Mbps access link                                RTT = 2 sec
       |                                                     |
       +-----------------------------------------------------+
```

**Given:**
- **Access link rate: 1.54 Mbps**
- **RTT from institutional router to origin server: 2 sec**
- **Web object size: 100 Kbits**
- **Average request rate from browsers to origin servers: 15 requests/sec**
- Therefore **average data rate to browsers: 1.50 Mbps**
- **LAN rate: 1 Gbps**

*(Check that last derivation: 100 Kbits/object × 15 objects/sec = 1,500,000 bits/sec = 1.50 Mbps. This is just traffic intensity's numerator, L·a.)*

---

### Part 1 — Performance with NO cache

**Step 1 — LAN utilisation.**

$$
\text{LAN utilisation} = \frac{1.50\ \text{Mbps}}{1\ \text{Gbps}} = \frac{1.5 \times 10^6}{1 \times 10^9} = 0.0015 = \mathbf{0.15\%}
$$

Equivalently, using traffic intensity:

$$
\frac{L \cdot a}{R} = \frac{100\text{K} \times 15}{1\ \text{Gbps}} = 0.0015 = 0.15\%
$$

*The LAN is essentially empty. It contributes microseconds of delay.*

**Step 2 — access link utilisation.**

$$
\text{Access link utilisation} = \frac{1.50\ \text{Mbps}}{1.54\ \text{Mbps}} = 0.97 = \mathbf{97\%}
$$

Equivalently:

$$
\frac{L \cdot a}{R} = \frac{100\text{K} \times 15}{1.54\ \text{Mbps}} = 0.97 = 97\%
$$

*Recall from Lecture 03 §8: as traffic intensity → 1, average queueing delay → ∞. At **97%**, we are deep in the vertical part of the hockey-stick curve.*

**Step 3 — end-to-end delay.**

$$
\text{end-end delay} = \text{Internet delay} + \text{access link delay} + \text{LAN delay}
$$
$$
= 2\ \text{sec} + \textbf{minutes} + \mu\text{secs}
$$

### ⚠️ **Problem: large queueing delays at high utilisation.**

The access link delay is measured in **minutes**, not milliseconds. The 1.54 Mbps access link is the bottleneck and it is saturated.

---

### Part 2 — Option 1: buy a faster access link

**Change:** upgrade the access link from **1.54 Mbps → 154 Mbps** (100× faster).

**Step 1 — new access link utilisation.**

$$
\text{utilisation} = \frac{1.50\ \text{Mbps}}{154\ \text{Mbps}} = 0.0097 = \mathbf{0.97\%}
$$

*The utilisation dropped from **0.97 → 0.0097**, i.e. from 97% to under 1%.*

**Step 2 — LAN utilisation is unchanged.** Still **0.0015**.

**Step 3 — new end-to-end delay.**

$$
= \text{Internet delay} + \text{access link delay} + \text{LAN delay}
$$
$$
= 2\ \text{sec} + \textbf{msecs} + \mu\text{secs} \approx \mathbf{2\ \text{sec}}
$$

*The "minutes" term collapses to "msecs" — because we are now far from the knee of the delay curve.*

**Step 4 — the catch.**

### 💰 **Cost: a faster access link is expensive!**

And note the floor: **the 2 sec Internet RTT does not improve at all.** No matter how fast you make your access link, you can never do better than 2 seconds, because that is the round trip to the origin server.

---

### Part 3 — Option 2: install a web cache

**Change:** keep the **1.54 Mbps** access link, but install a **local web cache** on the institutional network.

### 💰 **Cost: a web cache is cheap!**

**Assumption: cache hit rate is 0.4** — i.e. 40% of requests are served by the cache.

---

**Step 1 — split the traffic.**
- **40% of requests are served by the cache**, with **low (msec) delay** — they never touch the access link.
- **60% of requests are satisfied at the origin** — these must cross the access link.

**Step 2 — compute the rate that actually reaches the access link.**

$$
\text{rate to browsers over access link} = 0.6 \times 1.50\ \text{Mbps} = \mathbf{0.9\ \text{Mbps}}
$$

*Why: only the 60% miss traffic crosses the link; the 40% hit traffic is served locally.*

**Step 3 — compute the new access link utilisation.**

$$
\text{utilisation} = \frac{0.9}{1.54} = \mathbf{0.58} = 58\%
$$

*This means **low (msec) queueing delay at the access link**. We have moved from 97% (vertical part of the delay curve) down to 58% (still flat part).*

**Step 4 — compute the average end-to-end delay.**

Use a weighted average over the two cases:

$$
\text{avg delay} = 0.6 \times (\text{delay from origin servers}) + 0.4 \times (\text{delay when satisfied at cache})
$$

Substituting:

$$
= 0.6 \times (2 + \sim\text{msec for access link \& LAN}) + 0.4 \times (\sim\mu\text{sec for LAN})
$$

$$
= 0.6 \times (2.01) + 0.4 \times (\sim\mu\text{secs})
$$

$$
= 1.206 + \approx 0 \approx \mathbf{1.2\ \text{seconds}}
$$

---

### 🔑 The punchline

> **The cache gives a LOWER average end-to-end delay than the 154 Mbps link (1.2 s vs ~2 s) — and it is cheaper too!**

**Why the cache beats the faster link:** the faster link can only reduce the *access link* delay; it is still stuck paying the 2 sec Internet RTT on **every single request**. The cache eliminates the Internet RTT entirely for **40% of requests**. You cannot buy your way around the speed of light, but you can avoid making the trip.

### Summary table of the three options

| | Access link rate | Access link utilisation | Avg end-to-end delay | Cost |
|---|---|---|---|---|
| **No cache** | 1.54 Mbps | **0.97 (97%)** | 2 sec + **minutes** | — |
| **Option 1: faster link** | 154 Mbps | 0.0097 (0.97%) | ~**2 sec** | **Expensive** |
| **Option 2: web cache (hit rate 0.4)** | 1.54 Mbps | 0.58 (58%) | ~**1.2 sec** | **Cheap** |

---

## 13. Problem with a web cache (proxy server)

### ⚠️ **The copy of the object in the web cache may be STALE.**

The origin server may have updated the object since the cache stored it. This motivates the conditional GET.

---

## 14. Browser caching: the Conditional GET

**Goal:** don't send the object if the browser has an **up-to-date cached version**.
- **No object transmission delay** (and no use of network resources)

**The mechanism:**

**Client side:** specify the date of the browser-cached copy in the HTTP request:
```
If-modified-since: <date>
```

**Server side:** the response contains **no object** if the browser-cached copy is up-to-date:
```
HTTP/1.0 304 Not Modified
```

### Diagram — both cases

```
  CASE 1: object NOT modified before <date>

   client                                        server
     |--- HTTP request msg ------------------------->|
     |    If-modified-since: <date>                  |
     |<-- HTTP response -----------------------------|
     |    HTTP/1.0 304 Not Modified                  |
     |    (NO object body — saves bandwidth)         |


  CASE 2: object WAS modified after <date>

   client                                        server
     |--- HTTP request msg ------------------------->|
     |    If-modified-since: <date>                  |
     |<-- HTTP response -----------------------------|
     |    HTTP/1.0 200 OK                            |
     |    <data>       (full object sent)            |
```

| Server's answer | Status code | Body included? |
|---|---|---|
| Object **not modified** since `<date>` | **304 Not Modified** | **No** |
| Object **modified** after `<date>` | **200 OK** | **Yes** |

> **Connection back to §5:** the `<date>` the client puts in `If-modified-since` is exactly the `Last-Modified` value the server sent in the original response header. That is what that header is for.

---

## 📐 Formula summary — Lecture 06

| # | Formula | Meaning |
|---|---|---|
| 1 | $$\text{utilisation} = \dfrac{\text{avg data rate}}{\text{link rate}}$$ | Fraction of a link's capacity in use |
| 2 | $$\text{traffic intensity} = \dfrac{L \cdot a}{R}$$ | Same quantity, from Lecture 03. L = object size (bits), a = request rate (req/sec), R = link rate (bps) |
| 3 | $$\text{avg data rate} = L \times a$$ | e.g. 100 Kbits × 15/sec = 1.50 Mbps |
| 4 | $$\text{rate over access link with cache} = (1 - h) \times (\text{avg data rate})$$ | h = cache **hit rate**; (1−h) = **miss rate** |
| 5 | $$\text{avg end-end delay} = (1-h)\,(d_{\text{origin}}) + h\,(d_{\text{cache}})$$ | Weighted average over hits and misses |
| 6 | $$d_{\text{origin}} = \text{Internet RTT} + d_{\text{access}} + d_{\text{LAN}}$$ | The three delay terms in the caching example |

**Numbers from the worked example worth remembering as a sanity check:**
- No cache: utilisation **0.97**, delay **2 sec + minutes**
- Faster link (154 Mbps): utilisation **0.0097**, delay **≈ 2 sec**, expensive
- Cache (h = 0.4): utilisation **0.58**, delay **≈ 1.2 sec**, cheap

---

## Key takeaways

- HTTP has exactly **two message types** (request, response), each with three parts: **line + headers + optional body**, separated by **CRLF**, with a **blank line marking the end of headers**.
- **GET** retrieves (can carry data after `?`); **POST** creates (data in body); **PUT** updates/replaces; **HEAD** returns headers only.
- Status codes: **200** OK, **301** Moved Permanently, **400** Bad Request, **404** Not Found, **505** Version Not Supported. **4xx = client's fault, 5xx = server's fault.**
- **Cookies** let a **stateless protocol keep state** by carrying an identifier in messages; four components = **response header, request header, client-side file, server-side database**.
- **First-party** cookies track you on one site; **third-party** cookies track you **across** sites without you choosing to visit the tracker. GDPR treats identifying cookies as **personal data**.
- A **web cache acts as both client and server**, and reduces **response time**, **access-link traffic**, and content-provider cost.
- The caching numerical is the highest-value calculation in this lecture: know how to get **utilisation**, the **(1−h) scaling of access-link traffic**, and the **weighted-average delay**.
- **Conditional GET** (`If-modified-since` → `304 Not Modified`) is how caches avoid serving stale content without re-downloading everything.
# Lecture 07 — HTTP/2 & HTTP/3, E-mail (SMTP/IMAP), and DNS
**CS 3001 Computer Networks · 8 September 2026 · Chapter 2**

---

## Index (as per the deck)

1. HTTP/2 — goals and HOL blocking
2. HTTP/2 → HTTP/3
3. E-mail: three major components
4. SMTP (RFC 5321)
5. Scenario: Alice sends e-mail to Bob
6. Sample SMTP interaction
7. SMTP observations vs HTTP
8. Mail message format (RFC 2822)
9. Retrieving e-mail: mail access protocols (IMAP, HTTP)
10. DNS: Domain Name System — services and structure
11. DNS: a distributed, hierarchical database
12. Root, TLD, authoritative and local DNS servers
13. DNS name resolution: iterated vs recursive queries
14. Caching DNS information
15. DNS records (RRs): A, NS, CNAME, MX
16. DNS protocol messages
17. Getting your info into the DNS
18. DNS security

---

## 1. HTTP/2

**Key goal: decreased delay in multi-object HTTP requests.**

### The problem HTTP/1.1 left behind

HTTP/1.1 introduced **multiple, pipelined GETs over a single TCP connection**. But:
- The server responds **in-order** — **FCFS (first-come-first-served)** scheduling to GET requests.
- With FCFS, a **small object may have to wait for transmission behind large object(s)**. This is called **head-of-line (HOL) blocking**.
- **Loss recovery** (retransmitting lost TCP segments) **stalls object transmission**.

### HTTP/2 [RFC 7540, 2015] — increased flexibility at the server

- **Methods, status codes, and most header fields are unchanged** from HTTP/1.1.
- **Transmission order of requested objects is based on client-specified object priority** — *not necessarily FCFS*.
- **Push unrequested objects to the client** (server push).
- **Divide objects into frames**, and **schedule frames to mitigate HOL blocking**.

---

### 1.1 Diagram — HOL blocking in HTTP/1.1

Client requests 1 large object (e.g. a video file) and 3 smaller objects.

```
   CLIENT  ---- GET O4, GET O3, GET O2, GET O1 ---->  SERVER

   Server sends, in order requested:

   time --->  [============== O1 ==============][O2][O3][O4]

   Client receives:
        O1 arrives (finally), then O2, O3, O4

   PROBLEM: objects delivered in the order requested —
            O2, O3, O4 all WAIT behind O1.
```

### 1.2 Diagram — HTTP/2 mitigating HOL blocking

Objects are divided into **frames**, and frame transmission is **interleaved**.

```
   CLIENT  ---- GET O4, GET O3, GET O2, GET O1 ---->  SERVER

   Server sends interleaved frames:

   time --->  [O1][O2][O1][O3][O1][O4][O1][O1][O1]...

   Client receives:
        O2, O4, O3 complete quickly; O1 completes last

   RESULT: Total time remains the SAME as before, but
           O2, O3, O4 are delivered quickly and
           O1 is only slightly delayed.
```

> **The key insight:** HTTP/2 does not make the link faster. It **reorders who waits**. Three small objects finishing early and one large object finishing slightly late is a far better user experience than three small objects each waiting for the large one.

---

## 2. HTTP/2 → HTTP/3

**HTTP/2 over a single TCP connection means:**
- **Recovery from packet loss still stalls all object transmissions.** (TCP guarantees in-order delivery to the application, so one lost segment blocks everything behind it — HOL blocking has simply moved from the application layer down into TCP.)
- As in HTTP/1.1, browsers therefore still have an **incentive to open multiple parallel TCP connections** to reduce stalling and increase overall throughput.
- **No security over a vanilla TCP connection.**

### HTTP/3
- Adds **security**
- Adds **per-object error- and congestion-control** (more pipelining)
- Runs **over UDP**

*(More on HTTP/3 in the transport layer chapter.)*

### Evolution summary

| Version | Connections | Key feature | Remaining problem |
|---|---|---|---|
| **HTTP/1.0** | One TCP connection **per object** | Simple | 2 RTT per object |
| **HTTP/1.1** | One persistent connection, pipelined GETs | Fewer RTTs | **FCFS → HOL blocking** |
| **HTTP/2** | One TCP connection, **framed & interleaved** | Priority, server push, mitigates app-level HOL | **TCP-level HOL** on loss; no security |
| **HTTP/3** | Over **UDP** | Security + per-object error/congestion control | — |

---

## 3. E-mail: three major components

1. **User agents**
2. **Mail servers**
3. **Simple Mail Transfer Protocol: SMTP**

### 3.1 User Agent

- A.k.a. the **"mail reader"**
- **Composing, editing, reading** mail messages
- E.g. **Outlook, iPhone mail client**
- **Outgoing and incoming messages are stored on the server**

### 3.2 Mail servers

- **Mailbox** — contains **incoming messages** for a user
- **Message queue** — of **outgoing (to be sent)** mail messages
- **SMTP protocol between mail servers** is used to send email messages
  - **Client:** the **sending** mail server
  - **"Server":** the **receiving** mail server

### Diagram — the e-mail architecture

```
    [user agent]                                    [user agent]
          \                                              /
           \                                            /
       [ MAIL SERVER ] ----SMTP----> [ MAIL SERVER ] ---
        /      ^                        /     \
       /   +--------------+            /       \
 [user agent]| outgoing   |      [user agent] [user agent]
             | message    |
             | queue      |
             +------------+
             | user       |
             | mailbox    |
             +------------+
```

> **Note the asymmetry that matters:** SMTP is used **between mail servers** and **from user agent to the sender's mail server**. It is **not** used to retrieve mail — that needs a separate access protocol (§9).

---

## 4. SMTP — RFC 5321

- Uses **TCP** to **reliably** transfer an email message from client (the mail server **initiating** the connection) to server, on **port 25**.
- **Direct transfer:** sending server (acting like a client) → receiving server. **No intermediate mail servers.**

### The three phases of transfer

1. **SMTP handshaking (greeting)**
2. **SMTP transfer of messages**
3. **SMTP closure**

### Command/response interaction (like HTTP)
- **Commands: ASCII text**
- **Response: status code and phrase**

### Diagram — SMTP connection establishment

```
  "client" SMTP server                       "server" SMTP server
        |--- initiate TCP connection ------------->|
        |<-----------------------------------------|  } RTT
        |    TCP connection initiated              |
        |<---------------- 220 --------------------|  }
        |----------------- HELLO ----------------->|  } handshaking
        |<-------------- 250 Hello ----------------|  }
        |                                          |
        |----------- SMTP transfers -------------->|
        v time                                     v time
```

---

## 5. Scenario: Alice sends e-mail to Bob

```
  1        2          3            4           5         6
 [UA] -> [Alice's mail server] -> [Bob's mail server] -> [UA]
          (SMTP)        (SMTP over TCP)        (mailbox)
```

1. **Alice uses her UA to compose** an e-mail message "to" `bob@someschool.edu`.
2. **Alice's UA sends the message to her mail server using SMTP**; the message is placed in the **message queue**.
3. The **client side of SMTP at Alice's mail server opens a TCP connection** with Bob's mail server.
4. **The SMTP client sends Alice's message over the TCP connection.**
5. **Bob's mail server places the message in Bob's mailbox.**
6. **Bob invokes his user agent to read the message.**

> **Note step 3:** Alice's mail server acts as the SMTP **client** even though it is a server. "Client" in SMTP means "the one who initiated this connection."

---

## 6. Sample SMTP interaction

```
    S: 220 hamburger.edu
    C: HELO crepes.fr
    S: 250  Hello crepes.fr, pleased to meet you
    C: MAIL FROM: <alice@crepes.fr>
    S: 250 alice@crepes.fr... Sender ok
    C: RCPT TO: <bob@hamburger.edu>
    S: 250 bob@hamburger.edu ... Recipient ok
    C: DATA
    S: 354 Enter mail, end with "." on a line by itself
    C: Do you like ketchup?
    C: How about pickles?
    C: .
    S: 250 Message accepted for delivery
    C: QUIT
    S: 221 hamburger.edu closing connection
```

### The same exchange, annotated (from the SMTP Interaction diagram)

| # | Sending mail server (SMTP client) | Command | # | Receiving mail server (SMTP server) | Reply |
|---|---|---|---|---|---|
| 1 | "Hi there, I want to send an email." | `HELO` | 2 | "Got it! Let's do this." | `250 OK` |
| 3 | "Here's who that email is from." | `MAIL FROM:` | 4 | "That sender looks good to me." | `250 OK` |
| 5 | "Here's who this email is going to." | `RCPT TO:` | 6 | "Yep, that recipient looks fine to me." | `250 OK` |
| 7 | "Alright, here's the message content." | `DATA` | 8 | "Got it!" | `354` |
| 9 | "That was all the message content." | `.` | 10 | "Cool! The email is on its way!" | `250 OK` |
| 11 | "That's it! We're done." | `QUIT` | 12 | "I'm closing the connection." | `221` |

### The SMTP status codes to know

| Code | Meaning |
|---|---|
| **220** | Service ready (server greeting) |
| **250** | Requested action OK / completed |
| **354** | Start mail input; end with `.` on a line by itself |
| **221** | Service closing transmission channel |

---

## 7. SMTP: observations

### Comparison with HTTP

| | **HTTP** | **SMTP** |
|---|---|---|
| Direction of initiative | **Client PULL** | **Client PUSH** |
| Command/response | ASCII, with status codes | ASCII, with status codes |
| Object packaging | **Each object encapsulated in its own response message** | **Multiple objects sent in a multipart message** |

> **"Pull vs push" is the fundamental difference.** In HTTP, the machine that *wants* the data starts the conversation. In SMTP, the machine that *has* the data starts it.

### Other SMTP properties

- **SMTP uses persistent connections.**
- **SMTP requires the message (header & body) to be in 7-bit ASCII.**
- **SMTP server uses `CRLF.CRLF` to determine the end of the message.** (That is: a line containing only a period.)

---

## 8. Mail message format — RFC 2822

The deck draws a careful parallel:

| Protocol for exchanging | Syntax of the thing exchanged |
|---|---|
| **SMTP**, defined in **RFC 5321** | **RFC 2822** defines the syntax for the e-mail message itself |
| *(like)* **RFC 7231 defines HTTP** | *(like)* **HTML defines the syntax for web documents** |

### Message structure

```
   +----------------------+
   |   To:                |
   |   From:              |   HEADER lines
   |   Subject:           |
   +----------------------+
   |   (blank line)       |   <-- separator
   +----------------------+
   |                      |
   |   the "message"      |   BODY
   |   ASCII characters   |
   |   only               |
   +----------------------+
```

### ⚠️ The classic confusion

> The `To:`, `From:`, `Subject:` lines **within the body of the email message** are **different from the SMTP `MAIL FROM:` and `RCPT TO:` commands!**
>
> - **SMTP commands** (`MAIL FROM:`, `RCPT TO:`) are the *envelope* — what the mail servers use to actually route and deliver the message.
> - **RFC 2822 headers** (`From:`, `To:`) are printed *on the letter inside* — what the user sees.
>
> They usually match, but nothing forces them to. This mismatch is exactly how e-mail spoofing works.

---

## 9. Retrieving e-mail: mail access protocols

```
  [ user  ]--SMTP-->[ sender's  ]--SMTP-->[ receiver's ]--e-mail-->[ user  ]
  [ agent ]         [ e-mail    ]         [ e-mail     ]  access   [ agent ]
                    [ server    ]         [ server     ]  protocol
                                                          (IMAP, HTTP)
```

- **SMTP:** **delivery/storage** of e-mail messages **to the receiver's server**
- **Mail access protocol:** **retrieval from the server**

### IMAP — Internet Mail Access Protocol [RFC 3501]
- **Messages are stored on the server**
- IMAP provides **retrieval, deletion, and folders** of stored messages **on the server**

### HTTP
- **Gmail, Hotmail, Yahoo!Mail**, etc. provide a **web-based interface on top of**:
  - **SMTP** (to **send**)
  - **IMAP** (or POP) (to **retrieve**)

> **The whole pipeline in one line:** SMTP pushes the mail *to* the recipient's server; IMAP pulls it *off* that server; HTTP is just the web wrapper around both.

---

## 10. DNS: Domain Name System

### The motivation

| People have many identifiers | Internet hosts and routers have |
|---|---|
| SSN, name, passport # | **IP address (32 bit)** — used for **addressing datagrams** |
| | **"Name"**, e.g. `cs.umass.edu` — used **by humans** |

**Q: How to map between IP address and name, and vice versa?**

### Definition

**Domain Name System (DNS):**
- A **distributed database** implemented in a **hierarchy of many name servers**
- An **application-layer protocol**: hosts and DNS servers communicate to **resolve names** (address/name translation)

> **Note the design philosophy:** this is a **core Internet function, implemented as an application-layer protocol** — keeping **complexity at the network's "edge"**. The same principle you saw in Lecture 05.

---

### 10.1 DNS services

1. **Hostname-to-IP-address translation**
2. **Host aliasing** — canonical and alias names
3. **Mail server aliasing**
4. **Load distribution** — replicated web servers: **many IP addresses correspond to one name**

### 10.2 Q: Why not centralize DNS?

**A: It doesn't scale!** Four reasons:

1. **Single point of failure**
2. **Traffic volume**
3. **Distant centralized database** (everyone far away suffers)
4. **Maintenance**

**Scale evidence:**
- **Comcast DNS servers alone: 600 billion (600B) DNS queries/day**
- **Akamai DNS servers alone: 2.2 trillion (2.2T) DNS queries/day**

### 10.3 Thinking about the DNS

- **Humongous distributed database:** ~**1 billion records**, each simple
- **Handles many trillions of queries/day**, with **many more reads than writes**
- **Performance matters:** almost every Internet transaction interacts with DNS — **msecs count!**
- **Organizationally, physically decentralized:** millions of different organizations are responsible for their own records
- **"Bulletproof":** reliability, security

---

## 11. DNS: a distributed, hierarchical database

```
                    +---------------------+
                    |  Root DNS Servers   |          <--- ROOT
                    +---------------------+
                     /         |          \
          +-----------+  +-----------+  +-----------+
          | .com DNS  |  | .org DNS  |  | .edu DNS  |  <--- TOP LEVEL
          |  servers  |  |  servers  |  |  servers  |       DOMAIN (TLD)
          +-----------+  +-----------+  +-----------+
            /      \        /     \       /       \
      yahoo.com  amazon.com  pbs.org   nyu.edu  umass.edu
      DNS srvrs  DNS srvrs   DNS srvrs DNS srvrs DNS srvrs  <--- AUTHORITATIVE
```

### Client wants the IP address for `www.amazon.com` — first approximation

1. Client queries a **root server** to find the **.com DNS server**
2. Client queries the **.com DNS server** to get the **amazon.com DNS server**
3. Client queries the **amazon.com DNS server** to get the **IP address for www.amazon.com**

> **Read the name right-to-left.** `www.amazon.com` is resolved from the *rightmost* label inwards: `.com` first, then `amazon`, then `www`. The hierarchy in the name mirrors the hierarchy of servers.

---

## 12. The four kinds of DNS server

### 12.1 Root name servers

- **Official, contact-of-last-resort** by name servers that **cannot resolve a name**
- **13 logical root name "servers" worldwide**, each **"server" replicated many times** (~**200 servers in the US**)
- **Incredibly important Internet function** — the Internet couldn't function without it
- **DNSSEC** provides security (**authentication, message integrity**)
- **ICANN** (Internet Corporation for Assigned Names and Numbers) **manages the root DNS domain**

### 12.2 Top-Level Domain (TLD) servers

- Responsible for **.com, .org, .net, .edu, .aero, .jobs, .museums**, and **all top-level country domains**: **.cn, .uk, .fr, .ca, .jp, .pk**, etc.
- **Network Solutions:** authoritative registry for **.com** and **.net** TLD
- **Educause:** **.edu** TLD

### 12.3 Authoritative DNS servers

- An **organization's own DNS server(s)**, providing **authoritative hostname-to-IP mappings** for that organization's named hosts
- Can be **maintained by the organization or by a service provider**

### 12.4 Local DNS name server (Default Name Server)

- When a host makes a DNS query, **it is sent to its local DNS server**.
- The local DNS server returns a reply, answering either:
  - **from its local cache** of recent name-to-address translation pairs (**possibly out of date!**), or
  - by **forwarding the request into the DNS hierarchy** for resolution
- **Each ISP has a local DNS name server.** To find yours:
  - **macOS:** `% scutil --dns`
  - **Windows:** `> ipconfig /all`

### ⚠️ Critical exam point

> **The local DNS server does NOT strictly belong to the DNS server hierarchy.**
>
> Root, TLD and authoritative servers form the hierarchy. The local DNS server sits *outside* it, acting as a proxy/cache on the client's behalf.

---

## 13. DNS name resolution: iterated vs recursive

**Example for both:** a host at `engineering.nyu.edu` wants the IP address for `gaia.cs.umass.edu`.

### 13.1 Iterated query

```
                                              [ root DNS server ]
                                              2 /      \ 3
                                               /        \
                                              /     [ TLD DNS server ]
   requesting host  --1-->  [ local DNS  ] --4------>/
   engineering.nyu.edu      [ server     ] <--5-----/
                     <--8-- [ dns.nyu.edu]
                                              6 \      / 7
                                                 \    /
                                     [ authoritative DNS server ]
                                         dns.cs.umass.edu
```

**How it works:**
- The **contacted server replies with the NAME OF A SERVER to contact**.
- **"I don't know this name, but ask this server."**
- The **local DNS server does all the legwork**, making a separate query at each level.

**Message count: 8** (1 query in, 6 in the middle, 1 reply back).

---

### 13.2 Recursive query

```
                                              [ root DNS server ]
                                              2 /      ^ 3
                                               /       |
                                              /   [ TLD DNS server ]
   requesting host  --1-->  [ local DNS  ]---/      4 \   ^ 5
   engineering.nyu.edu      [ server     ]             \  |
                     <--8-- [ dns.nyu.edu] <--7--  6 <--\ |
                                                          \|
                                     [ authoritative DNS server ]
                                         dns.cs.umass.edu
```

**How it works:**
- **Puts the burden of name resolution on the contacted name server.**
- Each server, instead of saying "ask someone else", goes and asks on your behalf, then passes the answer back down the chain.

**The concern raised in the deck:** **heavy load at the upper levels of the hierarchy?** The root and TLD servers end up doing everyone's work, which is exactly why in practice the Internet uses iterated queries from the local DNS server upward.

### Comparison

| | **Iterated** | **Recursive** |
|---|---|---|
| Who does the work | The **local DNS server** | The **contacted server** at each level |
| Server's reply | "**Ask this server instead**" | "**Here is the answer**" |
| Load on root/TLD | **Light** | **Heavy** |
| In practice | **This is what is actually used** above the local DNS server | Used between the host and its local DNS server |

---

## 14. Caching DNS information

- **Once (any) name server learns a mapping, it caches the mapping**, and **immediately returns the cached mapping** in response to a query.
- **Caching improves response time.**
- **Cache entries time out (disappear) after some time — the TTL.**
- **TLD servers are typically cached in local name servers** — which means **root servers are often bypassed entirely**.

### The downside

- **Cached entries may be out-of-date.**
- If a named host **changes its IP address**, this **may not be known Internet-wide until all TTLs expire**.
- Therefore DNS is a **best-effort name-to-address translation**.

---

## 15. DNS records (RRs)

**DNS is a distributed database storing resource records (RR)** — RFC 1035.

### 📐 RR format

$$
(\text{name},\ \text{value},\ \text{type},\ \text{ttl})
$$

**TTL** specifies the time to leave the resource record — it determines the time when the resource **should be removed from cache**.

### ⚠️ **The meaning of `name` and `value` depends on `type`.**

---

### Type A — Address Mapping Record (RFC 1035)
- **name** is a **hostname**
- **value** is an **IP address**
- Used to **map (point) a domain name to an IP address**
- E.g. `(relay1.bar.foo.com, 145.37.93.126, A)`

### Type NS — Name Server Record (RFC 1035)
- **name** is a **domain**
- **value** is the **hostname of the authoritative name server** for this domain
- NS records specify **which DNS server is authoritative for this domain**
- E.g. `(foo.com, dns.foo.com, NS)`

### Type CNAME — Canonical Name Record (RFC 1035)
- **name** is an **alias (mnemonic) name** for some **"canonical" (the real) name**
- **value** is the **canonical name (real/actual name)**
- E.g. `www.ibm.com` is really `servereast.backup2.ibm.com`
- Used to **map (point) a domain name to ANOTHER domain name**. For example, if your website is `example.com` but you have also registered `examples.com`, then `examples.com` can be redirected towards `example.com` via this record.
- E.g. `(foo.com, relay1.bar.foo.com, CNAME)`

### Type MX — Mail Exchange Record (RFC 1035)
- **name** is an **alias name** for some **"canonical" (the real) name**
- **value** is the **canonical name of the mail server** associated with the alias name
- **Same as CNAME but for a mail server**
- **Used by SMTP to locate the mail server name for that domain** — and therefore **the mail server name must ALSO have a Type A record**
- E.g. `(foo.com, mail.bar.foo.com, MX)`

---

### 15.1 DNS RR Summary table

| Type | Name | Value | Example | Application |
|---|---|---|---|---|
| **A** — provides hostname to IP translation | Hostname | IP address of the host specified in Name | `www.3schools.com, 10.0.0.1, A, 10` | **Host-to-IP translation** |
| **NS** | Domain name | Hostname of the **authoritative server** for the domain specified in name | `Foo.com, dns.foo.com, NS, 10` | **To get IP of authoritative server** |
| **CNAME** | Alias hostname | Canonical hostname | `Fb.com, www.facebook.com, CNAME, 10` | **Host aliasing** |
| **MX** | Alias mail server name | Canonical mail server name | `Hotmail.com, 123.hotmail.com, MX, 10` | **Mail server aliasing** |

*(The trailing `10` in each example is the **TTL**.)*

> **The mnemonic:** **A** = name→**A**ddress. **NS** = domain→**N**ame **S**erver. **CNAME** = alias→**C**anonical **NAME**. **MX** = domain→**M**ail e**X**changer.

---

## 16. DNS protocol messages

**DNS query and reply messages both have the SAME format.**

```
        <--- 2 bytes ---> <--- 2 bytes --->
       +-----------------+-----------------+
       | identification  |      flags      |   } 12-byte
       +-----------------+-----------------+   } HEADER
       |  # questions    |  # answer RRs   |   } SECTION
       +-----------------+-----------------+   }
       | # authority RRs | # additional RRs|   }
       +-----------------+-----------------+
       |   questions (variable # of questions)  |
       +---------------------------------------+
       |    answers (variable # of RRs)         |
       +---------------------------------------+
       |    authority (variable # of RRs)       |
       +---------------------------------------+
       |  additional info (variable # of RRs)   |
       +---------------------------------------+
```

### 16.1 Header section — the first 12 bytes

**Identification field:**
- The first field is a **16-bit number that identifies the query**.
- This identifier is **copied into the reply message**, allowing the client to **match received replies with sent queries**.

**Flags field — four one-bit flags to know:**

| Flag | Meaning |
|---|---|
| **query/reply** | Indicates whether the message is a **query (0)** or a **reply (1)** |
| **authoritative** | Set **in a reply** when the DNS server **is an authoritative server** for the queried name |
| **recursion-desired** | Set when a client (host or DNS server) **desires that the DNS server perform recursion** when it doesn't have the record |
| **recursion-available** | Set **in a reply** if the DNS server **supports recursion** |

**The four "number-of" fields:** indicate the **number of occurrences of the four types of data sections that follow the header**.

### 16.2 The four data sections

| Section | Contents |
|---|---|
| **Question** | Information about the query being made: **(i) a name field** containing the name being queried, and **(ii) a type field** indicating the type of question being asked about the name |
| **Answer** | In a reply from a DNS server, the **resource records for the name that was originally queried** |
| **Authority** | **Records of other authoritative servers** |
| **Additional** | **Other helpful records** — "additional helpful info that may be used" |

---

## 17. Getting your info into the DNS

**Example: a new startup, "Network Utopia".**

### Step 1 — Register at a DNS registrar

You should **register the name `networkutopia.com` at a DNS registrar** (e.g. Network Solutions, GoDaddy).
- **Provide names and IP addresses of your authoritative name server** (primary and secondary).
- The **registrar inserts type NS and type A RRs into the .com TLD server** (and a CNAME RR if an alias name also exists):

```
(networkutopia.com, dns1.networkutopia.com, NS)
(dns1.networkutopia.com, 212.212.212.1, A)
(nwutopia.com, networkutopia.com, CNAME)
```

*Why both an NS and an A record:* the NS record names your name server, but a **name is useless without an address** — so the A record supplies the IP of that name server. This pairing is called a **glue record**.

### Step 2 — Create your authoritative server locally

Create the authoritative server at IP address **212.212.212.1**, containing:

**Type A record for the web server** — if the DNS query is initiated by **HTTP for a web server** (assuming the web server IP is `212.212.212.2`):
```
(networkutopia.com, 212.212.212.2, A)
(www.networkutopia.com, 212.212.212.2, A)
```
*Both records exist so the web server is reached whether the user types `networkutopia.com` **or** `www.networkutopia.com`.*

**Type MX record for mail** — if the DNS query is initiated by **SMTP for a mail server** (assuming the mail server IP is `212.212.212.3`):
```
(networkutopia.com, mail.networkutopia.com, MX)
(mail.networkutopia.com, 212.212.212.3, A)
```
*Again the pair: the MX names the mail server, and the A record gives that name an address — which is why the notes stress that **the mail server name must also have a Type A record**.*

---

## 18. DNS security

### DDoS attacks

**Bombard the root servers with traffic:**
- **Not successful to date.**
- Why: **traffic filtering**, and **local DNS servers cache the IPs of TLD servers**, allowing the **root server to be bypassed**.

**Bombard the TLD servers:**
- **Potentially more dangerous** — because they are less easily bypassed by caching.

### Spoofing attacks

- **Intercept DNS queries, returning bogus replies**
- This is **DNS cache poisoning**
- Defence: **RFC 4033: DNSSEC** authentication services

---

## 📐 Formula summary — Lecture 07

This lecture contains **no arithmetic numericals**, but it contains several **exact structural quantities** that are frequently examined:

| # | Quantity / format | Value |
|---|---|---|
| 1 | **DNS RR format** | $$(\text{name},\ \text{value},\ \text{type},\ \text{ttl})$$ |
| 2 | **SMTP port** | **25** |
| 3 | **DNS message header size** | **12 bytes** |
| 4 | **DNS identification field** | **16 bits** (2 bytes) |
| 5 | **Every field in the DNS header** | **2 bytes** each (identification, flags, and the four "number-of" fields = 6 × 2 = 12 bytes) |
| 6 | **IP address size** | **32 bits** |
| 7 | **Number of logical root name servers** | **13** (~200 physical servers in the US) |
| 8 | **Iterated query message count** (deck example) | **8** messages |
| 9 | **End-of-message marker, SMTP** | `CRLF.CRLF` — a line containing only `.` |
| 10 | **SMTP message encoding requirement** | **7-bit ASCII** (header and body) |

**RFC numbers appearing in this lecture:**

| RFC | Defines |
|---|---|
| **RFC 7540 (2015)** | HTTP/2 |
| **RFC 5321** | SMTP |
| **RFC 2822** | E-mail message syntax |
| **RFC 3501** | IMAP |
| **RFC 1035** | DNS resource records (A, NS, CNAME, MX) |
| **RFC 4033** | DNSSEC |

---

## Key takeaways

- **HTTP/2** fixes application-level **HOL blocking** by **framing and interleaving** objects and honouring **client-specified priority**; it does not speed up the link, it changes who waits.
- **HTTP/3** moves to **UDP** to escape TCP's own HOL blocking on loss, and adds security.
- E-mail = **user agents + mail servers + SMTP**. SMTP is **push**, **port 25**, **persistent**, **7-bit ASCII**, ends with `CRLF.CRLF`.
- **SMTP envelope commands (`MAIL FROM:`, `RCPT TO:`) ≠ RFC 2822 header lines (`From:`, `To:`)**.
- **IMAP** (or HTTP webmail) does retrieval; SMTP only does delivery.
- **DNS is a distributed, hierarchical database + an application-layer protocol.** Hierarchy = **root → TLD → authoritative**. The **local DNS server is NOT part of the hierarchy**.
- **Iterated** = "ask this server instead" (local server does the work). **Recursive** = contacted server does the work (heavy load upward).
- **Caching with TTL** is what makes DNS fast and what makes it **best-effort** rather than exact.
- Know the **four RR types cold**: A (name→IP), NS (domain→name server), CNAME (alias→canonical name), MX (domain→mail server). **MX and NS targets always need a matching A record.**

---

## Course admin announced in this lecture

- **Assignment #2 (Chapter 2)** — uploaded on Google Classroom after the 10 September lecture. **Due Thursday 17 September 2026**, during the lecture. Handwritten **hard copy** to the instructor.
- **Quiz #2 (Chapter 2)** — in class **Thursday 17 September 2026**, during lecture time. **Own FLEX-registered section only. No retake. Be on time.**
# Lecture 08 — Video Streaming, DASH, CDNs, and Socket Timeouts
**CS 3001 Computer Networks · 10 September 2026 · Chapter 2 (concludes)**

---

## Index (as per the deck)

1. Video streaming and CDNs: context
2. Multimedia: video — frames, pixels, resolution
3. Spatial and temporal coding
4. CBR vs VBR
5. HTTP streaming of stored video
6. Streaming stored video: challenges, playout delay and buffering
7. Streaming multimedia: **DASH**
8. Content Distribution Networks (CDNs)
9. CDN content access via DNS — the NetCinema/KingCDN example
10. Case study: Netflix; OTT
11. **Worked numerical: the DASH exercise, parts (a)–(e)**
12. Chapter 2 summary
13. Additional slides: socket timeouts, `settimeout()`, `try/except`

---

## 1. Video streaming and CDNs: context

- **Streaming video traffic is a major consumer of Internet bandwidth**: Netflix, YouTube and Amazon Prime accounted for **80% of residential ISP traffic (2020)**.
- **Challenge — scale:** how to reach **~1 billion users**?
- **Challenge — heterogeneity:** different users have **different capabilities** (wired vs mobile; bandwidth-rich vs bandwidth-poor).
- **Solution:** a **distributed, application-level infrastructure**.

---

## 2. Multimedia: video

- The underlying medium is **pre-recorded videos placed on servers**. Users send requests to those servers to view the videos **on demand**.
- **Video = a sequence of images (frames) displayed at a constant or variable bit rate** — thousands of still digital frames flashed back-to-back at lightning speed to trick your brain.

### Frame rates (FPS)

| FPS | Standard for |
|---|---|
| **24 FPS** | **Hollywood movies** |
| **30 FPS** | **TV news, YouTube videos, standard streaming** |
| **60 FPS** | **Live sports and video games** |

### 2.1 What is a pixel?

If you zoom far into a digital image (frame), you find that:
- The **smallest building block** of a digital image (and thus a video) is a **pixel**. The word comes from **Pix** (from *Picture*) + **EL** (from *Element*).
- A pixel is **a single tiny square of one solid colour**. Millions of these coloured squares pack tightly together, and your eyes blend them into a smooth, detailed picture.

### 2.2 How a pixel is represented

A single pixel is represented as a **triple in the form [R, G, B]**.

**Example:** `[255, 0, 0]` = **pure red**, where **255 is the intensity (brightness)** of red (255 being maximum brightness, 0 being off).

In binary, the same pixel is written as:
```
[11111111, 00000000, 00000000]
```

### 📐 **Therefore: a standard RGB single pixel = 24 bits**

*(3 colour channels × 8 bits per channel = 24 bits = 3 bytes.)*

### 2.3 Resolution

**Resolution:** how many pixels an image has **horizontally × vertically**.

> **More pixels (i.e. higher resolution) = a sharper image.**

| Standard | Resolution | Total pixels |
|---|---|---|
| **Full HD** | 1920 × 1080 | **2,073,600** (over two million) |
| **4K** | 3840 × 2160 | **8,294,400** (over eight million) |

### 2.4 A digital image is a grid

**Example — a tiny 2×2 image = 4 pixel triples.** This means the image is 2 pixels wide and 2 pixels tall:

```
   [255, 0, 0] , [0, 255, 0]         <-- Row 0: red pixel, green pixel
   [0, 0, 255] , [255, 255, 255]     <-- Row 1: blue pixel, white pixel
```

### 2.5 Uncompressed frames

- An **uncompressed digital image (frame)** — a single still photograph — is an **array of pixels**.
- Each pixel is **represented by bits**, stored in memory as colour values in a grid. E.g. a **white pixel is stored as [255, 255, 255]**.
- **An uncompressed digital frame is thus just a grid of numbers, and a video is many uncompressed frames played one after another.**

### 2.6 Compression

- A video **can be compressed, thus trading off video quality with bit rate**.
- **4K streaming requires a bitrate of more than 10 Mbps.**

### 🔑 The key performance statement

> **The most important performance measure for streaming video is average end-to-end throughput.**
>
> The network must provide an average throughput to the streaming application that is **at least as large as the bitrate of the compressed video**.

---

## 3. Coding: spatial and temporal

**Coding** = use **redundancy within and between images** to **decrease the number of bits** used to encode an image.

Two kinds:

### Spatial coding (WITHIN an image)

> Instead of sending **N values of the same colour** (e.g. all purple), send only **two values**: the **colour value (purple)** and the **number of repeated values (N)**.

```
   Raw:      [purple][purple][purple][purple][purple][purple]   = 6 x 24 bits
   Spatial:  (purple, 6)                                        = 1 colour + 1 count
```

### Temporal coding (FROM ONE IMAGE TO THE NEXT)

> Instead of sending the **complete frame at i+1**, send only the **differences from frame i**.

```
   frame i           frame i+1
   +---------+       +---------+
   |  scene  |  -->  |  same   |   send only the small region
   |         |       |scene but|   that CHANGED, not the whole
   |    O    |       |   O->   |   frame
   +---------+       +---------+
```

> **Why this works:** consecutive frames in a video are almost identical (1/24th of a second apart), so the difference between them is tiny compared to the frame itself. This is where the bulk of video compression gains come from.

---

## 4. CBR vs VBR

### CBR — Constant Bit Rate ("the steady stream")

- **Video encoding rate is fixed.**
- Uses **the exact same amount of data every second**, no matter what is happening on screen.
- Thus, a 10-second scene of a **completely black screen uses the exact same file size** as a 10-second scene of an **exploding firework show**.
- **Best for live streaming like Zoom calls.**

### VBR — Variable Bit Rate ("the smart stream")

- **Video encoding rate changes as the amount of spatial and temporal coding changes.**
- Adjusts the data amount **second-by-second based on how complex the visual scene is**.
- A simple scene (a person standing against a still background) uses **less data** than a high-speed car-chase scene.
- **Best for on-demand videos like Netflix, YouTube.**

### CBR vs VBR — summary table

| Feature | **CBR (Constant)** | **VBR (Variable)** |
|---|---|---|
| **Data flow** | Perfectly steady | Constantly fluctuating |
| **Video quality** | **Drops during fast action** | **Stays consistently sharp** |
| **Final file size** | Predictable but bulky | Optimized and efficient |
| **Best used for** | **Live broadcasting (Zoom calls)** | **Netflix, YouTube** |

### Encoding standards and their rates

| Standard | Typical rate |
|---|---|
| **MPEG 1** (CD-ROM) | **1.5 Mbps** |
| **MPEG 2** (DVD) | **3–6 Mbps** |
| **MPEG 4** (often used on the Internet) | **64 Kbps – 12 Mbps** |

> **Why CBR suits live and VBR suits on-demand:** live streaming must fit a *predictable* pipe in real time — a sudden VBR spike would cause a stall you cannot buffer around. On-demand video is buffered ahead, so spikes are absorbed and you gain quality for free on the simple scenes.

---

## 5. HTTP streaming of stored video

### The simple scenario

```
   [ video server ]  ------- Internet -------  [ client ]
   (stored video)                              (buffers,
                                                then plays)
```

**How it works, step by step:**

1. In HTTP streaming, the **video is simply stored at an HTTP server as an ordinary file with a specific URL**.
2. When a user wants to see the video, the client **establishes a TCP connection with the server** and **issues an HTTP GET request** for that URL.
3. The server then **sends the video file, within an HTTP response message, as quickly as the underlying network protocols and traffic conditions will allow**.
4. On the client side, the **bytes are collected in a client application buffer**. Once the **number of bytes in this buffer exceeds a predetermined threshold**, the **client application begins playback**.
5. Thus, the video streaming application is **displaying video as it is receiving and buffering frames** corresponding to later parts of the video.

### Main challenges (shortcomings) of plain HTTP streaming

- **All clients receive the SAME encoding of the video**, although the server-to-client bandwidth **will vary for different clients**, or can **vary over time even for the same client** — with changing congestion levels (in-house, access network, network core, video server).
- **Packet loss and delay due to congestion will delay playout, or result in poor video quality.**

---

## 6. Streaming stored video: timing, challenges and buffering

### The three timelines (assuming fixed network delay)

```
   1. video recorded          2. video sent
      (e.g. 30 frames/sec)       across network
            |                  (fixed delay in
            |                   this example)
            v                         v
   ==========================================================
                                            3. video received,
                                               played out at client
                                               (30 frames/sec)
                                                    |
                                                    v
   ==========================================================
```

**At any instant:** the client is **playing out an early part of the video while the server is still sending a later part**. That overlap is what "streaming" means.

### Challenges

**Continuous playout constraint:** during client video playout, **playout timing must match the original timing**.
- **But network delays are variable (jitter)** — so a **client-side buffer** is needed to satisfy the continuous playout constraint.

**Other challenges:**
- **Client interactivity:** pause, fast-forward, rewind, jump through the video
- **Video packets may be lost, retransmitted**

### Playout delay and buffering

```
   constant bit rate         client video          constant bit rate
   video transmission        reception             video playout at client
        /                       /                      /
       /                   ___/                   ___/
      /                ___/  ^                ___/
     /             ___/      |            ___/
    /          ___/     buffered      ___/
   /       ___/          video    ___/
  /    ___/    ^                ___/
 /  __/        |            ___/
 |<---- client playout ---->|
        delay                              time --->
        (compensates for the
         VARIABLE network delay)
```

> **Client-side buffering and playout delay compensate for network-added delay and jitter.** The buffer is a shock absorber: the network delivers unevenly, the buffer drains evenly.

---

## 7. Streaming multimedia: DASH

**DASH = Dynamic, Adaptive Streaming over HTTP**

### 7.1 Server side

- **Divides the video file into multiple chunks**
- **Each chunk is encoded at multiple different rates** (thus different quality levels)
- **Different rate encodings are stored in different files** (different versions)
- **Files are replicated in various CDN nodes**
- A **manifest file** (stored at the HTTP server) **provides URLs for the different chunks (versions) with each one's bit rate**

### 7.2 Client side

- The client **first requests the manifest file** and learns about the various versions.
- **Periodically estimates available server-to-client bandwidth.**
- **Consulting the manifest, requests one chunk at a time:**
  - **Chooses the maximum coding rate sustainable given the current bandwidth**
  - **Can choose different coding rates at different points in time** (depending on available bandwidth at the time) **and from different servers**
- This feature is **particularly important for mobile users**, as their **bandwidth availability may fluctuate as they move**.

### 7.3 "Intelligence" at the client

The client determines **three things**:

| Decision | Detail |
|---|---|
| **WHEN to request a chunk** | So that **buffer starvation or overflow does not occur** |
| **WHAT encoding rate to request** | **Higher quality when more bandwidth is available** |
| **WHERE to request the chunk from** | Can request from a URL/server that is **"close" to the client** or that **has high available bandwidth** |

### 🔑 The summary equation

> **Streaming video = encoding + DASH + playout delay & buffering**

```
  SERVER                                    CLIENT
  chunk 1: [1Mbps][2Mbps][4Mbps][8Mbps]       |
  chunk 2: [1Mbps][2Mbps][4Mbps][8Mbps]  <--  | 1. GET manifest
  chunk 3: [1Mbps][2Mbps][4Mbps][8Mbps]       | 2. measure bandwidth
  chunk 4: [1Mbps][2Mbps][4Mbps][8Mbps]       | 3. GET best chunk
      ...                                     | 4. repeat per chunk
  + manifest file (URLs + bit rates)          |
```

---

## 8. Content Distribution Networks (CDNs)

**Challenge:** how to stream content (selected from **millions of videos**) to **hundreds of thousands of simultaneous users**?

### Option 1 — a single, large "mega-server"

The most straightforward option, and it fails on four counts:
- **Single point of failure**
- **Point of network congestion**
- **Long (and possibly congested) path to distant clients**, resulting in **long freezing delays**
- **A popular video might be sent many times over the same links** — so the Internet video company is **paying the ISP for sending the same bytes of data into the Internet over and over again**, and **wasting bandwidth**

### ➡️ **Quite simply: this solution doesn't scale.**

---

### Option 2 — store/serve multiple copies at multiple geographically distributed sites (a CDN)

The CDN attempts to **direct each user request to a CDN location that can provide the best user experience for that user**.

**The CDN may be:**
- A **private CDN** — e.g. **Google CDN distributes YouTube videos**
- A **third-party CDN** — e.g. **Akamai, Limelight, Level-3**

---

### 8.1 Two main server placement philosophies

| | **Enter deep** | **Bring home** |
|---|---|---|
| **Strategy** | Push CDN servers **deep into many access networks** (thousands of locations) | A **smaller number (10's) of larger clusters** placed in **IXPs near access nets** |
| **Rationale** | **Close to users** — improves user-perceived delay and throughput by **decreasing the number of links and routers** between the end user and the CDN server | **Bigger server clusters at fewer sites** |
| **Trade-off** | **Managing the clusters becomes challenging** due to the highly distributed design | **Simpler management**, but **possibly at the expense of higher delay and lower throughput** to the end user |
| **Used by** | **Akamai** — 240,000 servers deployed in > 120 countries (2015); Google Search | **Limelight**, YouTube videos |

> **The trade-off in one line:** enter deep buys **performance** at the cost of **manageability**; bring home buys **manageability** at the cost of **performance**.

*(Akamai is an American company specializing in content delivery networks, cybersecurity, DDoS mitigation and cloud services, headquartered in Cambridge, Massachusetts.)*

---

## 9. CDN content access: a closer look

### 🔑 **Most CDNs take advantage of DNS to intercept and redirect requests.**

### Example — NetCinema employs KingCDN

**Setup:** a content provider, **NetCinema**, employs a third-party company, **KingCDN**, to distribute its videos to its customers.

- **Bob (client) requests video** `http://netcinema.com/6Y7B23V`
- **The video is actually stored in the CDN at** `http://KingCDN.com/NetC6y&B23V`

### The six steps

```
                                  [ netcinema.com web page ]
                                            |
  1. Bob gets URL for video                 |
     http://netcinema.com/6Y7B23V  <--------+
                |
  2. resolve http://netcinema.com/6Y7B23V via Bob's LOCAL DNS server
                |
                v
        [ Bob's local DNS server ]
                |
  3. netcinema's AUTHORITATIVE DNS returns a CNAME for
     http://KingCDN.com/NetC6y&B23V
                |
  4. Bob's local DNS now queries KingCDN's authoritative DNS
                |
  5. KingCDN authoritative DNS returns the IP of a nearby KingCDN server
                |
  6. Bob requests the video from the KingCDN server,
     streamed via HTTP
```

| Step | What happens |
|---|---|
| **1** | Bob gets the URL for the video `http://netcinema.com/6Y7B23V` from the netcinema.com web page |
| **2** | Resolve `http://netcinema.com/6Y7B23V` via Bob's local DNS |
| **3** | **netcinema's authoritative DNS returns a CNAME** for `http://KingCDN.com/NetC6y&B23V` |
| **4** | The query is passed to **KingCDN's authoritative DNS** |
| **5** | KingCDN's DNS responds |
| **6** | **Request the video from the KingCDN server, streamed via HTTP** |

> **This is why CNAME records matter** (Lecture 07 §15). The CDN redirect is implemented purely as a DNS alias — the client never knows it has been redirected, and NetCinema never has to change its URLs.

---

## 10. Case study: Netflix

### How does Netflix work? (high level)

- Netflix **stores copies of content** (e.g. *MadMen*) at its **worldwide OpenConnect CDN nodes**.
- A **subscriber requests content**, and the **service provider returns a manifest**.
  - **Using the manifest, the client retrieves content at the highest supportable rate.**
  - **May choose a different rate or copy if the network path is congested.**

### The four-stage flow

```
   [ Amazon cloud ]                     upload copies of multiple
   Netflix registration,   ----------->  versions of video to      [ CDN server ]
   accounting servers                    CDN servers               [ CDN server ]
         ^  ^                                                      [ CDN server ]
         |1 |                                                            ^
         |  |   2. Bob browses Netflix video                             |
   [ Bob ]  +---3. Manifest file returned for specific video             |
         |                                                               |
         +-----4. DASH server selected, contacted, streaming begins------+
```

**1. User logs in — Amazon Cloud Servers**
- Netflix stores **accounts, authentication, billing, profiles in Amazon AWS**.
- When Bob signs in, **AWS handles login + session management**.

**2 & 3. User browses & selects a movie — Netflix Control Servers**
- Netflix's **application servers return a manifest file** (**`.mpd` for DASH**).
- **The manifest lists:**
  - **Available video qualities** (the **bitrate ladder**)
  - **URLs of video segments**
  - **Which CDN nodes hold the content**

**4. Device contacts the CDN — Netflix OpenConnect CDN**
- Netflix stores **many copies of each video on OpenConnect edge servers worldwide**.
- The **device chooses the closest or least congested CDN node**.

**Adaptive streaming begins (DASH)**
- Video is **downloaded in small segments (2–10 seconds)**.
- **Before each segment, the player checks bandwidth.** It chooses:
  - **Highest bitrate if the network is good**
  - **Lower bitrate if congestion occurs**

---

## 11. OTT — "over the top"

**OTT** = **delivering video over the public Internet** and **not through direct cable TV or satellite (dish TV)**.

The Internet is used as **host-to-host communication as a service** — nothing more.

### OTT challenges: coping with a congested Internet from the "edge"

**OTT providers do not control the Internet, thus congestion affects streaming.** Two open questions:
- **What content to place in which CDN node?**
- **From which CDN node to retrieve content? At which rate?**

---

## 12. 🔢 The DASH worked numerical — parts (a) to (e)

### The question

A video of **duration 240 seconds** is prepared for HTTP adaptive streaming (DASH) and stored on a CDN. The video is encoded into **4 representations**:

| | Rep 1 | Rep 2 | Rep 3 | Rep 4 |
|---|---|---|---|---|
| **Bitrate** | **1 Mbps** | **2 Mbps** | **4 Mbps** | **8 Mbps** |

The video is **split into segments of 4 seconds each**.

**Manifest file (MPD) assumptions:**
- The MPD lists **all 4 representations**
- For each representation, the **URLs of all segments**
- **Each segment URL entry in the manifest takes 60 bytes**
- **Each representation has a fixed 200-byte header** in the manifest (codec info, resolution, etc.)

**DASH client adaptation rule:**
> For each new segment, the client measures the **average download throughput of the previous segment** and chooses the **highest representation whose bitrate is ≤ 0.8 × measured throughput**.

Measured throughput after each segment download:

| After segment | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| **Throughput (Mbps)** | **5** | **3** | **9** | **1.5** | **6** |

**The client starts with Rep 2 for Segment 1** (no prior measurement).

---

### Part (a) — How many segments does each representation have?

**Step 1 — understand the structure.**
The timeline is partitioned into equal **4-second chunks**. Every representation covers the **same 240 seconds**, just at a different quality — so they all have the **same number of segment positions**.

**Step 2 — divide.**

$$
\frac{240\ \text{seconds}}{4\ \text{seconds per segment}} = \mathbf{60\ \text{segments per representation}}
$$

**Answer: 60 segments each, for Rep 1, Rep 2, Rep 3 and Rep 4.**

*Each representation contains the same 60 segment positions — this is what makes switching between them mid-stream possible.*

---

### Part (b) — For Rep 3 (4 Mbps), how many bits and bytes does one segment contain?

**Step 1 — identify the two quantities.**
- Rep 3 bitrate = **4 Mbps** = 4 × 10⁶ bits/sec
- Segment duration = **4 seconds**

**Step 2 — multiply rate by time to get total bits.**

$$
4 \times 10^6\ \text{bits/s} \times 4\ \text{s} = 16 \times 10^6\ \text{bits}
$$

*(Bits/second × seconds = bits. The seconds cancel.)*

**Step 3 — convert bits to bytes.**
Conversion: **8 bits = 1 byte**

$$
\frac{16 \times 10^6}{8} = 2 \times 10^6\ \text{bytes}
$$

**Step 4 — express in readable units.**
Using **decimal units** (1 MB = 10⁶ bytes):

$$
2 \times 10^6\ \text{bytes} = \mathbf{2\ MB}
$$

### ✅ **Answer: one Rep 3 segment = 16 Mbits = 2 MB.**

---

### Part (c) — What is the total size (in bytes) of the manifest file?

**Step 1 — work out the cost of ONE representation.**

Each representation has 60 segments (from part a), and each segment URL entry costs 60 bytes:

$$
60\ \text{URLs} \times 60\ \text{bytes each} = 3{,}600\ \text{bytes}
$$

**Step 2 — add the fixed header for that representation.**

$$
3{,}600 + 200 = 3{,}800\ \text{bytes per representation}
$$

**Step 3 — multiply by the number of representations.**

$$
3{,}800 \times 4 = \mathbf{15{,}200\ \text{bytes}}
$$

**Step 4 — express approximately.**

$$
15{,}200\ \text{bytes} \approx \mathbf{15\ KB}
$$

### ✅ **Answer: 15,200 bytes ≈ 15 KB.**

> **Sanity check on why this matters:** the manifest is ~15 KB, while a *single* 4-second Rep 3 segment is 2,000 KB. The manifest costs essentially nothing relative to the video — which is why DASH can afford to describe every segment of every quality level up front.

---

### Part (d) — Which representation does the client choose for segments 1 to 5?

**The rule:** selection cap = **0.8 × previous measured throughput**. Choose the **highest representation whose bitrate ≤ that cap**.

*(The 0.8 factor keeps **20% throughput headroom** — a safety margin so a small dip in bandwidth doesn't immediately starve the buffer.)*

---

**Segment 1**
- **Input:** none — the client is told to **start at Rep 2 (given)**.
- **Choice: Rep 2 → 2 Mbps**

---

**Segment 2**
- **Input:** throughput measured after S1 = **5 Mbps**
- **Step 1 — compute the cap:** 0.8 × 5 = **4.0 Mbps**
- **Step 2 — find the highest rep ≤ 4.0:** ladder is 1, 2, 4, 8. Rep 3 = 4 Mbps, and **4 ≤ 4.0 ✓**. Rep 4 = 8 Mbps ✗.
- **Choice: Rep 3 → 4 Mbps**

---

**Segment 3**
- **Input:** throughput after S2 = **3 Mbps**
- **Step 1 — cap:** 0.8 × 3 = **2.4 Mbps**
- **Step 2 — highest rep ≤ 2.4:** Rep 3 = 4 ✗. Rep 2 = 2, and **2 ≤ 2.4 ✓**.
- **Choice: Rep 2 → 2 Mbps** *(the client steps DOWN because bandwidth dropped)*

---

**Segment 4**
- **Input:** throughput after S3 = **9 Mbps**
- **Step 1 — cap:** 0.8 × 9 = **7.2 Mbps**
- **Step 2 — highest rep ≤ 7.2:** Rep 4 = 8 ✗ (8 > 7.2). Rep 3 = 4 ✓.
- **Choice: Rep 3 → 4 Mbps**

---

**Segment 5**
- **Input:** throughput after S4 = **1.5 Mbps**
- **Step 1 — cap:** 0.8 × 1.5 = **1.2 Mbps**
- **Step 2 — highest rep ≤ 1.2:** Rep 2 = 2 ✗. Rep 1 = 1, and **1 ≤ 1.2 ✓**.
- **Choice: Rep 1 → 1 Mbps**

---

### ✅ Answer table

| Segment | Input for this decision | Safe cap (0.8 × throughput) | Choice | Bitrate |
|---|---|---|---|---|
| **1** | Start at Rep 2 (given) | — | **Rep 2** | 2 Mbps |
| **2** | After S1: 5 Mbps | 4.0 Mbps | **Rep 3** | 4 Mbps |
| **3** | After S2: 3 Mbps | 2.4 Mbps | **Rep 2** | 2 Mbps |
| **4** | After S3: 9 Mbps | 7.2 Mbps | **Rep 3** | 4 Mbps |
| **5** | After S4: 1.5 Mbps | 1.2 Mbps | **Rep 1** | 1 Mbps |

### ⚠️ Note explicitly made in the deck

> **Rep 4 is never selected** — even 0.8 × 9 Mbps = **7.2 Mbps**, which is **below 8 Mbps**. The 20% headroom rule means you would need a measured throughput of **at least 10 Mbps** before Rep 4 becomes eligible.

*(Also note: the 6 Mbps result measured after Segment 5 would inform the choice for **Segment 6** — 0.8 × 6 = 4.8, so Segment 6 would be Rep 3.)*

---

### Part (e) — What average encoding rate was actually used over these 5 segments?

**Step 1 — identify what to average.**

### ⚠️ **Average the SELECTED REPRESENTATION BITRATES — not the measured throughputs.**

This is the trap in this question. The measured throughputs (5, 3, 9, 1.5, 6) describe the *network*; the question asks about the *video quality actually delivered*.

**Step 2 — list the chosen bitrates from part (d).**

| S1 | S2 | S3 | S4 | S5 |
|---|---|---|---|---|
| 2 Mbps | 4 Mbps | 2 Mbps | 4 Mbps | 1 Mbps |

**Step 3 — sum them.**

$$
2 + 4 + 2 + 4 + 1 = 13\ \text{Mbps}
$$

**Step 4 — divide by the number of segments.**

$$
\frac{13}{5} = \mathbf{2.6\ \text{Mbps}}
$$

### ✅ **Answer: average encoding rate over the five downloaded segments = 2.6 Mbps.**

*(This is a valid simple average because **all segments have equal duration (4 s)**. If segment durations differed, you would need a duration-weighted average instead.)*

---

## 13. Chapter 2 summary

**Our study of the network application layer is now complete.**

**Application architectures:**
- Client-server
- P2P

**Application service requirements:**
- Reliability, bandwidth, delay

**Internet transport service model:**
- **Connection-oriented, reliable: TCP**
- **Unreliable, datagrams: UDP**

**Specific protocols:**
- HTTP
- SMTP, IMAP
- DNS
- P2P: BitTorrent

**Also covered:** video streaming, CDNs, socket programming (TCP, UDP sockets).

### Most importantly: learned about protocols!

**Typical request/reply message exchange:**
- Client requests info or service
- Server responds with data, status code

**Message formats:**
- **Headers:** fields giving info about data
- **Data:** the info (payload) being communicated

### Important themes running through the whole chapter

| Theme | Where you saw it |
|---|---|
| **Centralized vs decentralized** | Client-server vs P2P; centralized DNS vs hierarchical DNS |
| **Stateless vs stateful** | HTTP statelessness vs cookies |
| **Scalability** | P2P self-scalability; CDN vs mega-server; DNS hierarchy |
| **Reliable vs unreliable message transfer** | TCP vs UDP |
| **"Complexity at network edge"** | Apps run only at end systems; DNS as an application-layer protocol |

---

## 14. Additional slides — socket programming with timeouts

*(Instructor's note: these slides are included here because the socket programming assignment needs timers, and `try/except` is the easiest way to do this. Learning it here makes the RDT programming assignment in Chapter 3 easier.)*

### 14.1 Waiting for multiple events

Sometimes a program must **wait for one of several events to happen**:
- Wait for **either (i) a reply from the other end of the socket, or (ii) a timeout: timer**
- Wait for **replies from several different open sockets**: `select()`, multithreading

**Timeouts are used extensively in networking.**

### 14.2 Using timeouts with a Python socket

```
   socket() -> connect() -> send() -> settimeout() -> recv() ---> (message received)
                                                        |
                                                     timeout
                                                        |
                                                        v
                                                 handle timeout
```

### 14.3 How `socket.settimeout()` works

**Case A — `s.settimeout(30)`**

```
   timer starts!          no packet arrives in 30 secs           TIMEOUT
   s.recv() ------------------------------------------------>  interrupt
                                                                s.recv() and
                                                                raise timeout
                                                                exception
```

**Case B — `s.settimeout(10)`, with a successful receive first**

```
   timer starts!   message received      timer starts!
                   & timer STOPS!                       no packet in 10 secs
   s.recv() -----------> s.recv() ---------------------------------> TIMEOUT
                                                                      interrupt
                                                                      s.recv() and
                                                                      raise timeout
                                                                      exception
```

### 🔑 Key property

> **`settimeout()` sets a timeout on ALL FUTURE socket operations of that specific socket** — not just the next one. The timer **starts when `recv()` is called** and **stops when a message is received**.

---

### 14.4 The Python try-except block

Execute a block of code, and handle **"exceptions"** that may occur when executing that block.

```python
try:
    <do something>
    # Executing this try code block may cause exception(s) to catch.
    # If an exception is raised, execution jumps DIRECTLY into the
    # except code block.

except <exception>:
    <handle the exception>
    # This except code block is only executed if an <exception>
    # occurred in the try code block.
    # (Note: an except block is REQUIRED with a try block.)
```

---

### 14.5 Toy example — the shepherd boy

**The story:**
- A shepherd boy tends his master's sheep.
- If he sees a wolf, he can **send a message to the villagers for help using a TCP socket**.
- The boy found it fun to **connect to the server without sending any messages**. The villagers don't think so.
- They decided that if the boy **connects to the server and doesn't send the wolf location within 10 seconds, for three times**, they will **stop listening to him forever**.

### The Python TCP server (the villagers)

```python
from socket import *

serverPort = 12000
serverSocket = socket(AF_INET, SOCK_STREAM)

serverSocket.bind(('', serverPort))
serverSocket.listen(1)
counter = 0

while counter < 3:
    connectionSocket, addr = serverSocket.accept()
    connectionSocket.settimeout(10)        # set a 10-second timeout on ALL
                                           # future socket operations
    try:
        wolf_location = connectionSocket.recv(1024).decode()
        # timer starts when recv() is called and will raise a timeout
        # exception if there is no message within 10 seconds

        send_hunter(wolf_location)         # a villager function
        connectionSocket.send('hunter sent')

    except timeout:                        # catch socket timeout exception
        counter += 1

    connectionSocket.close()
```

**Walking through the logic:**

| Line | What it does |
|---|---|
| `socket(AF_INET, SOCK_STREAM)` | Create a **TCP** socket (`SOCK_STREAM` = TCP; `AF_INET` = IPv4) |
| `bind(('', serverPort))` | Bind to **port 12000** on all interfaces |
| `listen(1)` | Start listening; queue up to 1 pending connection |
| `while counter < 3` | Villagers give the boy **three chances** |
| `accept()` | Blocks until a client connects; returns a **new connection socket** |
| `settimeout(10)` | Arms the **10-second timer on all future ops** of this connection socket |
| `recv(1024)` | Timer starts here. If nothing arrives in 10 s → **timeout exception** |
| `except timeout: counter += 1` | Catches the timeout and **burns one of the three chances** |
| `close()` | Closes the connection either way, then loops back |

> **What this teaches for Chapter 3:** this is the exact pattern you will use to implement **reliable data transfer** — send a packet, arm a timer, and if the ACK doesn't come back before the timer fires, retransmit.

---

## 📐 Formula summary — Lecture 08

| # | Formula | Meaning |
|---|---|---|
| 1 | $$\text{RGB pixel size} = 3 \times 8 = 24\ \text{bits}$$ | One pixel = [R, G, B], 8 bits (0–255) per channel |
| 2 | $$\text{Resolution} = \text{width} \times \text{height (in pixels)}$$ | Full HD = 1920×1080 = 2,073,600 px; 4K = 3840×2160 = 8,294,400 px |
| 3 | $$\text{Uncompressed frame size (bits)} = \text{width} \times \text{height} \times 24$$ | Grid of pixels × bits per pixel |
| 4 | $$\text{Number of segments} = \dfrac{\text{video duration}}{\text{segment duration}}$$ | e.g. 240 s ÷ 4 s = **60 segments** |
| 5 | $$\text{Segment size (bits)} = \text{bitrate} \times \text{segment duration}$$ | e.g. 4 Mbps × 4 s = **16 Mbits** |
| 6 | $$\text{Segment size (bytes)} = \dfrac{\text{Segment size (bits)}}{8}$$ | 16 Mbits ÷ 8 = **2 MB** (decimal, 1 MB = 10⁶ bytes) |
| 7 | $$\text{MPD size} = R \times \big[(S \times b_{URL}) + b_{header}\big]$$ | R = #representations, S = #segments, b = bytes. e.g. 4 × [(60×60) + 200] = **15,200 bytes** |
| 8 | $$\text{Selection cap} = 0.8 \times \text{measured throughput}$$ | Choose the **highest rep with bitrate ≤ cap** (20% headroom) |
| 9 | $$\text{Avg encoding rate} = \dfrac{\sum \text{selected bitrates}}{\text{number of segments}}$$ | e.g. (2+4+2+4+1)/5 = **2.6 Mbps** — average the **selected** rates, not the measured throughputs |
| 10 | $$\text{required throughput} \geq \text{compressed video bitrate}$$ | The core streaming constraint |

**Constants to remember:**
- **1 RGB pixel = 24 bits = 3 bytes**
- **8 bits = 1 byte**; **1 MB = 10⁶ bytes** (decimal units, as used in this deck)
- **FPS standards: 24 (film) / 30 (TV, YouTube) / 60 (sports, games)**
- **4K needs > 10 Mbps**
- **DASH segment length in practice: 2–10 seconds** (Netflix); **4 seconds** in the exam question
- **Streaming = 80% of residential ISP traffic (2020)**

---

## Key takeaways

- **The most important performance measure for streaming video is average end-to-end throughput**, and it must be **≥ the compressed video's bitrate**.
- Compression comes from **spatial coding** (redundancy *within* a frame) and **temporal coding** (differences *between* frames).
- **CBR = steady, predictable, quality drops on action → live.** **VBR = fluctuating, efficient, quality stays sharp → on-demand.**
- Plain HTTP streaming's flaw is that **everyone gets the same encoding**; **DASH** fixes it by **chunking + multiple encodings + a manifest + client-side adaptation**.
- **All the intelligence in DASH is at the client** — when, what rate, and where to fetch each chunk.
- A **single mega-server doesn't scale**; **CDNs** replicate content geographically, using either **enter deep** (performance) or **bring home** (manageability).
- **CDNs redirect requests using DNS CNAME records** — the client never knows.
- The **DASH numerical** is the highest-value calculation in this lecture: segments = duration/segment length, segment size = bitrate × duration, manifest size = reps × (segments × URL bytes + header), selection = highest rep ≤ 0.8 × throughput, and the final average uses **selected bitrates**, not measured throughputs.
- `settimeout()` + `try/except timeout` is the pattern that underpins every reliable-transfer protocol you will write in Chapter 3.

---

## Course admin repeated in this lecture

- **Assignment #2 (Chapter 2)** — uploaded after this lecture. **Due Thursday 17 September 2026**, handwritten hard copy, during the lecture.
- **Quiz #2 (Chapter 2)** — **Thursday 17 September 2026**, during lecture time, **own FLEX-registered section only**. No retake.
