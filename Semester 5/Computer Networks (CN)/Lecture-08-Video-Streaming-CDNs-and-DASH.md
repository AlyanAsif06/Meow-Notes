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
