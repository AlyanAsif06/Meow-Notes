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
