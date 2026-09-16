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
