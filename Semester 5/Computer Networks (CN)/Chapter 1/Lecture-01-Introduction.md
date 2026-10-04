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
