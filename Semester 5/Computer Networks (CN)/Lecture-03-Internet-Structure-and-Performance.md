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
