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
