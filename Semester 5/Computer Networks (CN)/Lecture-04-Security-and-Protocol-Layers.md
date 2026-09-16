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
