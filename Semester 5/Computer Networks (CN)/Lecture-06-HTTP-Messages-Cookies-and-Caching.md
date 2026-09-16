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
