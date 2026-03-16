# 100 Essential React Interview Questions in 2026

<div>
<p align="center">
<a href="https://devinterview.io/questions/web-and-mobile-development/">
<img src="https://firebasestorage.googleapis.com/v0/b/dev-stack-app.appspot.com/o/github-blog-img%2Fweb-and-mobile-development-github-img.jpg?alt=media&token=1b5eeecc-c9fb-49f5-9e03-50cf2e309555" alt="web-and-mobile-development" width="100%">
</a>
</p>

#### You can also find all 100 answers here 👉 [Devinterview.io - WebSockets](https://devinterview.io/questions/web-and-mobile-development/websocket-interview-questions)

<br>

## 1. What is WebSocket protocol and how does it differ from HTTP?

### WebSocket vs. HTTP: 2026 Technical Audit

**WebSocket** (RFC 6455) and **HTTP/3** (RFC 9114) represent distinct paradigms in transport and application-layer communication. While HTTP/3 over QUIC has narrowed the latency gap for request-response cycles, WebSocket remains the standard for stateful, persistent, full-duplex streams.

### Core Differences

*   **Communication Model**: HTTP is inherently **request-response**. Even with HTTP/2 and HTTP/3 multiplexing, the client dictates data exchange. WebSockets implement **Full-Duplex** communication over a single TCP/TLS connection, allowing either party to push data asynchronously.
*   **Connection Lifecycle**: HTTP is primarily **stateless**. While HTTP/3 maintains connection persistence (0-RTT), it remains transactional. WebSockets undergo an **HTTP 101 Switching Protocols** handshake, upgrading the connection to a persistent binary stream that remains open until explicitly terminated.
*   **Protocol Overhead**: HTTP/1.1 requires repetitive header transmission. While HTTP/2+ uses **HPACK/QPACK** header compression to mitigate this, WebSockets outperform in high-frequency, low-payload scenarios because frames after the handshake incur only a **2-to-14 byte overhead**.
*   **Data Framing**: WebSockets utilize a distinct framing protocol that allows interleaved control frames (ping/pong) and data frames (text/binary) without re-initiating a request cycle.

### Operation Mechanism

*   **HTTP**: Operates on a transactional basis. The client sends a request; the server processes the state and returns a response. Modern HTTP/3 utilizes **QUIC** (UDP-based) to solve head-of-line blocking at the transport layer, significantly improving performance for traditional web content.
*   **WebSockets**: Leverages a long-lived TCP connection. By maintaining the pipe, the server avoids the overhead of repeated TLS handshakes for every message. This reduces the latency of message delivery to $O(1)$ post-handshake, making it deterministic compared to the jitter inherent in sequential HTTP requests.

### Protocol Stack Integration

*   **HTTP**: Sits at the **Application Layer** (Layer 7). HTTP/3 leverages QUIC, which integrates transport layer features into the protocol stack.
*   **WebSockets**: Initiates via an **HTTP/1.1 upgrade header**, then transitions to a custom protocol implementation. In 2026, **WebTransport** (built on HTTP/3) is increasingly used for use cases requiring unreliable or unordered data streams, though WebSocket remains the ubiquitous choice for reliable ordered communication.

### Best-Candidate Use Cases

*   **HTTP/3**: Optimal for document fetching, RESTful APIs, and idempotent data retrieval where the overhead of maintaining state is unnecessary.
*   **WebSockets**: Mandatory for low-latency, state-dependent environments:
    *   **Real-time Collaborative Engines**: Multi-user editing (e.g., CRDT-based synchronization).
    *   **Financial Market Data**: High-frequency ticker updates where every millisecond of latency reduction is critical.
    *   **Interactive Gaming**: Managing synchronized game-state updates across high-concurrency client sessions.

### 2026 Modernization Note: WebSocket vs. WebTransport
While WebSockets remain standard for reliable streams, **WebTransport API** is now preferred for low-latency media streaming and advanced gaming, as it allows developers to choose between reliable and unreliable data delivery over HTTP/3, mitigating the "head-of-line blocking" issues inherent in the TCP-based WebSocket protocol.
<br>

## 2. Explain how the WebSocket handshake works.

### WebSocket Handshake Architecture (2026 Update)

The **WebSocket handshake** transitions a standard HTTP/1.1 request into a persistent, **full-duplex** binary framing protocol (RFC 6455). This mechanism facilitates low-latency communication by bypassing the overhead of repetitive HTTP request-response cycles.

#### 1. Client Initiation
The client initiates the upgrade via an HTTP GET request. In 2026, clients must include the `Sec-WebSocket-Version: 13` header, which remains the definitive version for global interoperability.

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: <base64-encoded-nonce>
Sec-WebSocket-Version: 13
Origin: https://example.com
```

#### 2. Server Response and Protocol Switching
The server inspects the request. Upon validation, it returns an **HTTP 101 Switching Protocols** status. The connection remains open, shifting the underlying socket from textual HTTP parsing to the **WebSocket Frame Protocol**.

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: <computed-hash>
```

#### 3. Cryptographic Handshake Integrity
To prevent caching proxies from incorrectly identifying the connection and to confirm that the server supports the protocol, the server processes the `Sec-WebSocket-Key` using the **GUID** `258EAFA5-E914-47DA-95CA-C5AB0DC85B11`.

**Modernized Node.js (v22+) Implementation:**

```javascript
import { createHash } from 'node:crypto';

const MAGIC_STRING = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';

/**
 * Computes the Sec-WebSocket-Accept header value.
 * @param {string} clientKey - Provided via Sec-WebSocket-Key header
 */
function computeAcceptHeader(clientKey) {
    return createHash('sha1')
        .update(clientKey + MAGIC_STRING)
        .digest('base64');
}
```

*Note:* While SHA-1 is cryptographically weak for digital signatures, it is utilized here strictly for **handshake obfuscation** and protocol validation; it remains the standard per RFC 6455.

#### 4. Post-Handshake Full-Duplex Framing
Once the handshake is complete, data is exchanged via **frames**. These frames introduce a minimal header (2–14 bytes), significantly reducing overhead compared to standard HTTP/2 or HTTP/3 headers.

*   **Complexity:** The frame parsing complexity is $O(1)$ relative to payload size, as the header structure is fixed-length once the payload length is determined.
*   **Persistent State:** The TCP connection remains active until a `Close` control frame (opcode `0x8`) is received or a network timeout occurs.
*   **Security (2026 Best Practice):** Always mandate **WSS (WebSocket Secure)** using **TLS 1.3** to ensure data integrity and confidentiality. Do not use unencrypted `ws://` in production environments.
<br>

## 3. What are some common use cases for WebSockets?

### WebSockets (RFC 6455) Modernized Audit

**WebSockets** provide a persistent, full-duplex communication channel over a single TCP connection. In 2026, they remain the standard for low-latency, stateful communication, though often abstracted by higher-level protocols like **WebTransport** or **gRPC-Web** in specific enterprise architectures.

### High-Frequency Use Cases

*   **Collaborative Productivity (CRDT-based)**: Real-time conflict resolution using **Conflict-free Replicated Data Types (CRDTs)** for multi-user editing environments (e.g., decentralized text editors, Figma-like canvas engines).
*   **Edge-Compute IoT Telemetry**: Bi-directional streaming for low-latency command-and-control loops in robotics and industrial IoT, typically leveraging **MessagePack** or **Protobuf** serialization to minimize payload overhead.
*   **High-Frequency Trading (HFT) Interfaces**: Real-time order book synchronization. WebSockets remain preferred over **Server-Sent Events (SSE)** when bi-directional signaling (e.g., order cancellation) is required.
*   **Live Multi-User Gaming**: State synchronization for game servers. While **WebRTC** is preferred for peer-to-peer data channels, WebSockets handle server-authoritative state broadcasting for massive concurrent sessions.
*   **AI-Agent Orchestration**: Streaming token-by-token responses from LLM backends to frontend interfaces, minimizing Time to First Token (TTFT).

### Technical Limitations & 2026 Alternatives
*   **WebTransport (HTTP/3)**: For scenarios requiring **unreliable** delivery (datagrams), WebTransport is now the preferred successor to WebSockets, as it avoids Head-of-Line (HOL) blocking inherent in TCP.
*   **Scalability**: WebSocket connections are stateful and consume memory per socket ($O(n)$ where $n = \text{connected clients}$). Modern implementations utilize **Redis Pub/Sub** or **NATS** as a message broker to decouple WebSocket gateway servers from application logic.

### 2026 Implementation Standard (Modernized)

Modern implementations utilize **TypeScript** for type safety across the wire, often leveraging **Zod** for schema validation of incoming JSON payloads to prevent injection attacks or malformed data state errors.

```typescript
// Modernized WebSocket implementation with Type Safety
interface MarketUpdate {
  symbol: string;
  price: number;
  timestamp: number;
}

const socket = new WebSocket('wss://api.exchange.com/v2/stream');

socket.addEventListener('open', () => {
  const subscribeMsg = JSON.stringify({ action: 'subscribe', pair: 'BTC-USD' });
  socket.send(subscribeMsg);
});

socket.addEventListener('message', (event: MessageEvent<string>) => {
  try {
    const data: MarketUpdate = JSON.parse(event.data);
    // UI thread processing using requestAnimationFrame for 60/120fps smoothness
    requestAnimationFrame(() => updateDashboard(data));
  } catch (err) {
    console.error('Schema validation failed:', err);
  }
});

function updateDashboard(data: MarketUpdate): void {
  const el = document.getElementById(`price-${data.symbol}`);
  if (el) el.textContent = data.price.toFixed(2);
}
```

### Performance Metrics
*   **Latency**: Typical round-trip time (RTT) for WebSockets is significantly lower than polling ($O(RTT)$ vs $O(RTT + \text{polling interval})$).
*   **Memory Footprint**: Each open socket consumes approximately 10KB–50KB of RAM on the server side, necessitating efficient **load balancing** (e.g., NGINX, HAProxy) using sticky sessions if state is maintained at the application layer.
<br>

## 4. What are the limitations of WebSockets?

### Limitations of WebSockets in 2026

**WebSockets (RFC 6455)** remain the industry standard for full-duplex communication. However, the shift toward **HTTP/3 (QUIC)** and serverless paradigms necessitates a re-evaluation of their role in modern architectures.

#### Connection Lifecycle & Resource Management
*   **Persistent Resource Consumption**: Unlike the request-response model of HTTP/1.1 or the multiplexed streams of HTTP/2 and HTTP/3, WebSockets maintain stateful TCP connections. Each connection consumes a socket file descriptor and memory on the server. At scale, this leads to $O(N)$ memory complexity where $N$ is the number of concurrent users, complicating horizontal scaling without an external message broker (e.g., Redis Pub/Sub, NATS).
*   **State Persistence & Serverless**: WebSocket persistence is fundamentally incompatible with the ephemeral nature of **Serverless Functions (FaaS)**. Solutions like *AWS API Gateway WebSocket APIs* or *Azure Web PubSub* effectively abstract this, but they introduce vendor lock-in and additional latency overhead for the handshake/upgrade process.

#### Infrastructure & Network Constraints
*   **Middlebox Interference**: While HTTP/3 over QUIC bypasses TCP head-of-line blocking, WebSockets are bound to TCP. Older firewalls, Deep Packet Inspection (DPI) proxies, and restrictive corporate gateways often terminate or drop long-lived WebSocket connections, necessitating aggressive heartbeat/keep-alive strategies.
*   **Load Balancing Complexity**: Conventional Layer 7 load balancers require "sticky sessions" or WebSocket-aware routing to maintain the stateful pipe. In contrast, modern **Service Meshes (Istio/Linkerd)** handle this, but misconfiguration often leads to silent connection drops during deployment rolling updates.

#### Protocol Efficiency vs. Alternatives
*   **HTTP/3 and WebTransport**: **WebTransport (RFC 9218)** is the 2026 successor to WebSockets for high-throughput, low-latency requirements. It provides unreliable (datagram-based) and reliable stream-based communication over HTTP/3, mitigating head-of-line blocking issues present in WebSocket’s TCP foundation.
*   **Overkill for Simple Updates**: For unidirectional "server-to-client" streams, **Server-Sent Events (SSE)** remain superior. SSE is lighter, operates over standard HTTP/2 streams, and features automatic reconnection natively, reducing the boilerplate code required for WebSockets.

#### Security & Compliance
*   **Authentication Fragility**: WebSockets do not support standard HTTP headers (like `Authorization: Bearer <token>`) during the `101 Switching Protocols` handshake in browser environments. Developers must often pass tokens as query parameters, which are susceptible to leakage in server logs and browser history. **Subprotocol negotiation** or cookie-based auth is preferred, but requires careful CSRF mitigation.
*   **Privacy Regulation (GDPR/CCPA)**: Persistent connections make "Right to be Forgotten" requests more complex. Real-time data streams must be actively tracked in memory to ensure immediate termination upon user request or session expiration to maintain compliance.

#### Mobile & Connectivity
*   **Battery and Radio State**: Maintaining a persistent TCP connection keeps the mobile radio in a high-power state longer. In regions with unstable 5G/6G signals, frequent reconnection cycles (the "thundering herd" problem) can significantly degrade mobile device battery life compared to HTTP-based polling or Push Notifications (APNs/FCM).

#### Summary Table: 2026 Protocol Comparison

| Feature | WebSockets | HTTP/3 (WebTransport) | SSE |
| :--- | :--- | :--- | :--- |
| **Transport** | TCP | QUIC | HTTP/2+ |
| **Full-Duplex** | Yes | Yes | No |
| **Complexity** | Moderate | High | Low |
| **Use Case** | Real-time gaming/Chat | High-perf streaming | Real-time dashboards |

#### Recommendation
For new greenfield projects in 2026:
1. Use **SSE** for uni-directional server-to-client updates.
2. Use **WebTransport** for high-performance, low-latency bidirectional requirements.
3. Use **WebSockets** only when legacy browser compatibility (pre-2023 environments) or broad ecosystem library support is a critical business constraint.
<br>

## 5. Can you describe the WebSocket API provided by HTML5?

### Technical Audit: WebSocket API (2026 Standards)

**WebSocket** is a stateful, **full-duplex** communication protocol standardized in **RFC 6455**. It operates over a single, persistent TCP connection, bypassing the overhead of HTTP/1.1 request-response cycles. While originally introduced as part of the HTML5 suite, it is now an independent IETF standard.

### Key Aspects of WebSockets

- **Protocol Upgrade**: Initiated via an **HTTP/1.1 GET request** featuring specific `Upgrade: websocket` and `Sec-WebSocket-Key` headers. Post-handshake, the connection upgrades to a binary framing layer.
- **Bi-directional Framing**: Data is partitioned into **frames** (text, binary, ping, pong, close). Framing allows for fragmented message delivery, maintaining $O(1)$ overhead per frame header compared to HTTP overhead.
- **Browser Native Stack**: Fully integrated into the **Web API** (WHATWG Living Standard). Modern architectures prefer `wss://` (WebSocket Secure) to ensure TLS encryption, mandatory for production deployment.

### WebSocket Lifecycle

1. **The Handshake**: Client sends an HTTP GET request with `Upgrade` headers. Server validates via `Sec-WebSocket-Accept` (SHA-1 hashing of the client's key).
2. **Persistent State**: Unlike HTTP/3 (QUIC-based), which optimizes streams, WebSocket provides a raw, long-lived pipe. For load balancing, **sticky sessions** or **Redis-based pub/sub** backplanes are required for horizontal scaling.
3. **Data Transmission**: Messages are sent as continuous frames. Binary data (e.g., `ArrayBuffer`, `Blob`) is natively supported, making it superior to HTTP for high-frequency binary data (e.g., WebRTC signaling, binary serialization like Protobuf).
4. **Termination**: Controlled shutdown via `CloseFrame` (opcode `0x8`). Abrupt network failure triggers an `onerror` event followed by `onclose`.

### 2026 Modern Considerations

- **WebSocket vs. WebTransport**: For 2026-era high-performance needs, **WebTransport** (built on HTTP/3) is preferred over WebSockets for scenarios requiring unreliable delivery or multi-streaming without head-of-line blocking.
- **Resource Management**: WebSocket connections consume server memory per-socket. Audit implementations for **backpressure** support to prevent buffer overflows during high-throughput bursts.

### Revised Code Example (ES2026+)

```javascript
// Using modern Class-based architecture and Optional Chaining
class SocketClient {
  constructor(url) {
    this.socket = new WebSocket(url);
    this.init();
  }

  init() {
    this.socket.addEventListener('open', () => console.log('Connected'));
    this.socket.addEventListener('message', ({ data }) => this.handle(data));
    this.socket.addEventListener('error', (err) => console.error(err));
  }

  handle(data) {
    // Handling Blob/ArrayBuffer natively
    if (data instanceof Blob) {
      data.text().then(text => console.log('Received:', text));
    }
  }

  send(payload) {
    if (this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(payload);
    }
  }
}

const client = new SocketClient('wss://api.example.com/v2/stream');
```

### Key Use-Cases

- **Real-Time Data Streaming**: Financial tickers, live analytics.
- **Collaborative CRDTs**: Maintaining shared state (e.g., Yjs/Automerge) in multi-user document editors.
- **Signaling Layer**: Providing the "handshake" mechanism for **WebRTC** Peer-to-Peer connections.
- **IoT Telemetry**: Low-latency sensor data ingestion with minimal framing latency.
<br>

## 6. Explain the WebSocket frame format.

### WebSocket Frame Architecture (RFC 6455 Refined)

The **WebSocket frame** protocol enables full-duplex communication over a single TCP connection. Modern implementations (2026+) prioritize high-throughput data framing and strict security hygiene, particularly regarding masking and frame fragmentation.

#### Initial Header and Control Bits

Every frame begins with a fixed 2-byte sequence. The first byte identifies the fragment state and opcode, while the second byte dictates the **Mask** bit and initial payload length.

```plaintext
 0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-------+---------------+
|F|R|R|R| opcode|M| Payload len |
|I|S|S|S|  (4)  |A|     (7)     |
|N|V|V|V|       |S|             |
+-+-+-+-+-------+---------------+
```

*   **FIN (1 bit):** Indicates the final fragment of a message.
*   **RSV1-3 (3 bits):** Reserved for protocol extensions (e.g., per-message `deflate` compression). Must be $0$ unless an extension is negotiated.
*   **OPCODE (4 bits):**
    *   `0x0`: Continuation
    *   `0x1`: Text (UTF-8)
    *   `0x2`: Binary
    *   `0x8`: Connection Close
    *   `0x9`: Ping
    *   `0xA`: Pong
*   **MASK (1 bit):** If set to $1$, a $32$-bit masking key follows. Per RFC 6455, clients **must** mask all frames sent to servers. Servers **must not** mask frames sent to clients.

#### Extended Header Logic

The payload length field (7 bits) determines the necessity of extended length bytes:
*   **0–125:** The value is the payload length.
*   **126:** The following $16$ bits ($2$ bytes) represent the payload length (unsigned big-endian).
*   **127:** The following $64$ bits ($8$ bytes) represent the payload length.

#### Payload and Masking

The payload data consists of the **Extension Data** (if any) followed by the **Application Data**. 

**Masking Mechanics:**
If the mask bit is set, the payload is XORed with a $32$-bit masking key ($masking\_key$). The transformation for the $i$-th byte of data ($transformed\_octet_i$) is:

$$transformed\_octet_i = original\_octet_i \oplus masking\_key[i \pmod 4]$$

This mechanism provides no cryptographic security; it exists exclusively to prevent cross-protocol attacks on intermediary proxies that might otherwise interpret malicious binary data as legitimate HTTP requests.

#### Example Interpretation (2026 Context)

Consider a binary frame: **FIN=1, Opcode=0x2 (Binary), Mask=0, Payload=75 bytes of 'A' (0x41).**

1.  **Byte 0:** `10000010` ($0x82$). (FIN set, Binary opcode).
2.  **Byte 1:** `01001011` ($0x4B$). (Mask bit 0, Length 75).
3.  **Payload:** $75$ bytes of `0x41`.

**Modern Implementation Note:** In 2026 production environments (e.g., using Node.js `ws` or Python `websockets` libraries), developers rarely interact with raw frames. High-level abstractions handle fragmentation and masking internally. Audits should focus on the **RSV1 bit** usage, as modern performance-critical applications heavily utilize the `permessage-deflate` extension ($RSV1=1$) to reduce bandwidth by $O(n)$ relative to compression ratios.
<br>

## 7. How do WebSockets handle communication through proxies and firewalls?

### WebSockets and Network Intermediaries (2026 Standards)

**WebSockets** (RFC 6455) provide persistent, full-duplex communication channels over a single TCP connection. In 2026, architectural designs must account for the prevalence of **HTTP/3 (QUIC)** and sophisticated **Layer 7 (L7) firewalls**.

### Proxies and Intermediaries

*   **HTTP/1.1 Upgrade Mechanism**: WebSockets initiate via an HTTP `GET` request containing the `Upgrade: websocket` and `Connection: Upgrade` headers. Older, non-WebSocket-aware proxies often fail to propagate these headers, leading to a `400 Bad Request` or `502 Bad Gateway`. 
*   **Protocol Negotiation**: Modern proxies act as **WebSocket-aware gateways**. They participate in the HTTP handshake to maintain statefulness. If an intermediary does not explicitly support the `Upgrade` mechanism, it will drop the connection.
*   **Connection Multiplexing and HTTP/2+**: While HTTP/1.1 proxies struggle with long-lived WebSocket streams (causing timeout-induced closures), **HTTP/2 and HTTP/3 (QUIC)** proxying handles multiplexing more efficiently. However, WebSocket-over-HTTP/2 (RFC 8441) is the required standard for modern infrastructure to prevent head-of-line blocking at the proxy level.

### Firewalls and Content Inspection

*   **Port Utilization**: Industry standards mandate WebSocket traffic over **WSS (WebSocket Secure)** using port **443**. Firewalls configured for HTTPS traffic generally permit WSS traffic as the handshake and subsequent framing are opaque to stateful inspection engines once the TLS tunnel is established.
*   **Deep Packet Inspection (DPI)**: Sophisticated L7 firewalls perform **TLS Termination**. By decrypting traffic, these firewalls can inspect individual WebSocket frames. If the WebSocket payload violates security policies (e.g., unauthorized protocol tunneling), the firewall will send a `TCP RST` to terminate the connection.
*   **Idle Timeout Policies**: Firewalls often terminate TCP connections that appear idle. Modern WebSocket implementations must utilize **Ping/Pong control frames** (every 30–60 seconds) to maintain state and prevent silent session teardown by middleboxes.

### Addressing Challenges: 2026 Best Practices

*   **Encapsulation via TLS**: TLS 1.3 is the mandatory transport layer. It provides **Forward Secrecy**, rendering content inspection significantly more difficult for firewalls unless they perform man-in-the-middle (MITM) proxying with enterprise-trusted root certificates.
*   **Fallback Strategies**: In high-restriction environments, **WebSockets-over-HTTPS** acts as the baseline. If connectivity fails due to protocol-specific blocking, applications should fall back to **WebTransport** (via HTTP/3), which provides low-latency communication that is natively compatible with modern QUIC-based network infrastructure.

### Modern Implementation (Python 3.14+)

In 2026, `websockets` (asyncio-native) is the preferred library. It handles frame-level management and heartbeat (ping/pong) mechanisms automatically, crucial for maintaining proxy transparency.

```python
import asyncio
import websockets
import ssl

# Define SSL context for secure TLS 1.3 tunneling
ssl_context = ssl.create_default_context()

async def connect_proxy():
    uri = "wss://api.example.com/v1/socket"
    
    # Standard 2026 approach: Use context manager for robust lifecycle
    async with websockets.connect(
        uri, 
        ssl=ssl_context,
        ping_interval=30,  # Prevent firewall idle timeouts
        ping_timeout=10
    ) as websocket:
        
        await websocket.send("Client-Hello")
        response = await websocket.recv()
        print(f"Received: {response}")

# Execute within asyncio event loop
if __name__ == "__main__":
    asyncio.run(connect_proxy())
```

### Technical Audit Summary
*   **Deprecation Warning**: Avoid manual `websocket-client` (threading-based) in high-concurrency environments; use `asyncio` for performance efficiency.
*   **Standard Compliance**: Ensure all production endpoints support **RFC 8441** for seamless traversal through HTTP/2-enabled proxies.
*   **Complexity**: WebSocket connection establishment involves an $O(1)$ handshake transition, but maintaining state in NAT environments requires $O(n)$ heartbeats over the connection lifetime.
<br>

## 8. What are the security considerations when using WebSockets?

### Security Considerations for WebSocket Architecture (2026)

Persistent connections shift the security boundary from request-based validation to session-state validation. Modern implementations must account for long-lived transport layers and the mitigation of asymmetric resource consumption.

#### Key Security Concerns

*   **Cross-Site WebSocket Hijacking (CSWSH)**: Unlike standard CORS, the WebSocket handshake does not automatically enforce same-origin policies. The `Origin` header must be strictly validated against a whitelist of trusted schemes and hostnames. Failure to validate the `Origin` allows cross-site scripts to initiate unauthorized persistent connections.
*   **Input Sanitization (Protocol-Level)**: WebSockets transport raw binary or text frames. Since the persistent nature allows for high-frequency streaming, perform **schema validation** (e.g., using Protocol Buffers or JSON Schema) at the application layer to prevent injection attacks (SQLi, Command Injection) that bypass traditional HTTP middleware.
*   **Resource Exhaustion & Rate Limiting**: Persistent connections bypass standard per-request firewall rules. Implement **backpressure management** and concurrency limits per IP address to mitigate $O(n)$ connection-exhaustion DDoS attacks. Enforce idle timeouts to prevent "zombie" connections from consuming file descriptors.
*   **Transport Layer Security (TLS)**: Always utilize `wss://` (WebSocket Secure). Standard `ws://` connections are susceptible to Man-in-the-Middle (MitM) attacks and packet sniffing. TLS 1.3 is the mandated baseline in 2026.
*   **Authentication & Session Management**: Authentication should occur during the initial HTTP handshake (e.g., via Secure/HttpOnly Cookies or short-lived Bearer tokens). Once established, the server must map the persistent connection to an authenticated identity, ensuring that subsequent messages within the stream are strictly verified against the session owner's permissions.
*   **Message Size & Framing Attacks**: Enforce strict frame-size limits. A malicious client could stream massive payloads to induce heap exhaustion. Implement a `maxPayloadLength` threshold at the server’s WebSocket handler level.

#### Modernized Implementation (Node.js/ws)

In 2026, origin verification and frame-size limiting are critical defense-in-depth measures.

```javascript
// Server (Node.js 22+ with 'ws' 8.x+)
const { WebSocketServer } = require('ws');
const wss = new WebSocketServer({ 
  port: 8080,
  maxPayload: 1024 * 16, // 16KB limit to prevent memory exhaustion
  verifyClient: (info, cb) => {
    const origin = info.origin;
    if (isAllowedOrigin(origin)) {
      cb(true); // Accept connection
    } else {
      cb(false, 403, 'Forbidden');
    }
  }
});

function isAllowedOrigin(origin) {
  const allowed = ['https://trusted.app'];
  return allowed.includes(origin);
}

wss.on('connection', (ws, req) => {
  // Session established. 
  // Perform secondary validation if using JWT/Tokens extracted from headers/cookies
  ws.on('message', (data) => {
    // Validate data schema here (e.g., Joi or Zod)
  });
});
```

```javascript
// Client (Browser Standard API)
// Always use 'wss' for encrypted transmission.
const ws = new WebSocket('wss://api.example.com/v1/socket');

ws.onmessage = (event) => {
  // Never interpret message data as executable code (e.g., avoid eval())
  const payload = JSON.parse(event.data);
  processUpdate(payload);
};
```

#### Audit Summary
1.  **Transport**: Moved from `ws://` (unencrypted) to mandatory `wss://`.
2.  **Origin Validation**: Leveraged `verifyClient` hook to terminate unauthenticated handshakes before resource allocation.
3.  **Stability**: Introduced `maxPayload` constraints to prevent buffer overflow/DoS vectors.
4.  **Security Posture**: Emphasized that session context must be validated per-frame, as state persists beyond the initial handshake.
<br>

## 9. How would you detect and handle WebSocket connection loss?

### Connection Monitoring

1. **Native Heartbeats**: Utilize the browser-native `WebSocket.ping()` and `pong` events (if supported by the specific protocol implementation) or implement application-layer heartbeat frames. Standardize on an interval $I$ (typically 30s) and a timeout $T < I$.
2. **Server-Side Monitoring**: Deploy **Prometheus** exporters to track `active_connections` via socket gauges. In 2026, utilize **OpenTelemetry** trace spans to correlate socket lifecycle events with downstream microservice latency.
3. **Client-Side Tracking**: Utilize the `onclose` and `onerror` event listeners. For React 19+ environments, wrap socket logic in `useSyncExternalStore` or custom hooks to ensure state consistency across the component tree.

### Identifying Disconnection Causes

1. **Graceful Closure**: Indicated by Close Code `1000` (Normal Closure).
2. **Abnormal Disconnection**: Indicated by Close Code `1006` (No close frame received). This usually signifies a TCP-level timeout, NAT table expiration, or physical network failure.
3. **Protocol Errors**: Indicated by codes `1002` (Protocol error) or `1009` (Message too large). These require client-side logic updates rather than reconnection.

### Reconnection Strategies

1. **Jittered Exponential Backoff**: Prevent "Thundering Herd" syndrome by adding a random factor to the backoff. 
   The delay $D$ is calculated as: $D = \min(base \times 2^n, max\_delay) + \text{jitter}$, where $n$ is the attempt count and $\text{jitter} \in [0, \text{base}]$.
2. **State Synchronization**: Upon reconnection, the client must perform a state-sync handshake to retrieve missed events during the offline window (e.g., requesting a delta-payload based on the last processed sequence ID).
3. **Visibility API Integration**: Utilize `document.visibilityState`. Pause or reduce heartbeat frequency when the page is hidden to save energy and reduce server overhead.

### Modernized Code Example (TypeScript/React 19)

```typescript
const RECONNECT_BASE = 2000;
const MAX_DELAY = 30000;

function useWebSocket(url: string) {
  const [ws, setWs] = useState<WebSocket | null>(null);
  const attempt = useRef(0);

  const connect = useCallback(() => {
    const socket = new WebSocket(url);

    socket.onopen = () => {
      attempt.current = 0;
      console.log('Connected');
    };

    socket.onclose = (event) => {
      if (event.code === 1000) return;

      const delay = Math.min(RECONNECT_BASE * 2 ** attempt.current, MAX_DELAY) + Math.random() * 1000;
      setTimeout(connect, delay);
      attempt.current++;
    };

    setWs(socket);
  }, [url]);

  useEffect(() => {
    connect();
    return () => ws?.close();
  }, [connect]);
}
```

### 2026 Auditor Notes
*   **Performance**: Avoid frequent polling; rely on TCP Keep-Alive settings configured at the load balancer (e.g., NGINX `proxy_read_timeout` / AWS NLB idle timeouts) to drop dead connections.
*   **Security**: Ensure all WebSocket upgrades originate from `wss://` (TLS 1.3).
*   **Complexity**: Reconnection logic maintains $O(1)$ space complexity, but server-side connection state management during widespread reconnect events is $O(N)$ where $N$ is the number of concurrent clients. Ensure server-side rate limiting on the `/ws` handshake endpoint.
<br>

## 10. Explain the role of ping/pong frames in WebSockets.

### Role of Ping/Pong Frames in WebSockets (RFC 6455)

**Ping** (Opcode `0x9`) and **Pong** (Opcode `0xA`) frames are **Control Frames** within the WebSocket protocol designed for **Liveness Monitoring** and **Path Maintenance**. They operate independently of the data stream, ensuring the underlying TCP connection remains viable in the presence of middleboxes (NATs, load balancers, and firewalls).

#### Purpose and Operational Necessity

1. **Connection Keep-Alive (Liveness):** Stateful firewalls often drop idle TCP connections after a predetermined TTL (Time-To-Live). Periodic Ping frames reset these timers, preventing abrupt connection termination.
2. **Dead Peer Detection:** If a network path fails or a client silently disconnects (e.g., mobile radio sleep), the Pong response absence allows the server to identify "zombie" connections. The server can then initiate `Close` frames to reclaim file descriptors and memory.
3. **Latency Probing:** By including a timestamp in the application data payload, endpoints can measure Round-Trip Time (RTT) to assess network health and adjust congestion control parameters.

#### Frame Architecture (RFC 6455 Specification)

Control frames must have a payload length $\le 125$ bytes and cannot be fragmented. 

| Frame Type | Opcode (Hex) | Description |
| :--- | :--- | :--- |
| **Ping** | `0x9` | Request for peer verification. |
| **Pong** | `0xA` | Unsolicited or solicited response to a Ping. |

**Hexadecimal Structure:**
A Ping frame with no payload is represented as:
`89 00` (FIN bit set, Opcode 9, Mask bit 0, Payload Length 0).

*Note:* Per RFC 6455 Section 5.5.3, a Pong frame **must** be sent in response to a Ping frame as soon as possible, mirroring the "Application Data" payload of the received Ping.

#### Modern Security Considerations

* **Resource Exhaustion:** Rapid, high-frequency Pings are a vector for **DDoS (Distributed Denial of Service)**. Implement rate-limiting at the application layer or via an ingress controller (e.g., NGINX/Envoy) to drop connections exceeding a predefined ping-rate threshold.
* **Payload Mirroring:** Since Pong frames echo the Ping payload, they are not suitable for authentication tokens or sensitive data. Do not treat Pong responses as proof of identity; they only verify path connectivity.

#### Modern Implementation (Python 3.14+ / `websockets` 15.0+)

In current asynchronous paradigms, the `websockets` library handles Ping/Pong heartbeats automatically via the `ping_interval` and `ping_timeout` configuration parameters. Manual invocation is typically unnecessary.

```python
import asyncio
import websockets

async def connection_manager():
    # Automatically manages keep-alive pings every 20 seconds
    # Closes connection if Pong is not received within 10 seconds
    async with websockets.connect(
        "wss://api.example.com",
        ping_interval=20,
        ping_timeout=10
    ) as ws:
        async for message in ws:
            print(f"Data received: {message}")

if __name__ == "__main__":
    asyncio.run(connection_manager())
```

#### Complexity Analysis
* **Memory Overhead:** $O(1)$ per connection, as ping frames are transient and non-buffered.
* **Network Overhead:** $O(k)$ where $k$ is the frequency of pings. The protocol ensures this is minimal, as control frames share the existing TCP socket rather than establishing new handshakes.
<br>

## 11. How does WebSocket ensure ordered delivery of messages?

### WebSocket Ordered Delivery Mechanisms

**WebSocket** ensures ordered delivery by leveraging the inherent stream-based guarantees of **TCP** (Transmission Control Protocol) and the **WebSocket Framing Protocol** (RFC 6455).

#### The Role of TCP Layering
WebSockets operate over a single, long-lived TCP connection. **TCP** provides the foundational guarantee of ordered delivery through the following mechanisms:

*   **Sequence Numbers**: Every byte transmitted is assigned a unique sequence number.
*   **Acknowledgment (ACK)**: The receiver acknowledges receipt of segments; if a gap in sequence numbers is detected, the receiver buffers out-of-order packets and requests retransmission of missing segments.
*   **In-Order Delivery**: The TCP stack ensures that segments are delivered to the application layer (the WebSocket implementation) only after all preceding segments have been successfully received and reassembled.

#### Full-Duplex Communication
Unlike the HTTP/1.1 request-response cycle, the WebSocket protocol maintains a persistent **full-duplex** channel. This prevents "head-of-line blocking" at the HTTP application level by allowing asynchronous traffic flow, while the underlying TCP connection maintains strict serial integrity for that specific bidirectional stream.

#### WebSocket Framing and Message Integrity
When a WebSocket message exceeds the frame buffer size or is fragmented, the **WebSocket Framing Protocol** ensures reconstruction via the **FIN (Final)** bit:

1.  **Fragmentation**: If a message is too large or requires streaming, the sender splits the payload into multiple frames.
2.  **Opcode Tracking**: The first frame uses a specific **opcode** (e.g., `0x1` for text, `0x2` for binary), while subsequent fragments use the **continuation opcode** (`0x0`).
3.  **FIN Bit**: The `FIN` bit is set to `0` for all non-final fragments and `1` for the last fragment. 
4.  **Reconstruction**: The endpoint buffers the payloads of frames with `FIN=0` until the frame with `FIN=1` is received, at which point the message is processed as a discrete unit.

#### Modern Implementation (2026 Standards)
Modern environments leverage **Streams API** and **Web Workers** for efficient frame reconstruction, avoiding the memory-intensive string concatenation seen in older implementations.

```javascript
// 2026 Modern Implementation using ReadableStream
const socket = new WebSocket('wss://api.example.com/stream');

// Using a TransformStream to handle fragmentation natively
const messageStream = new TransformStream({
  transform(chunk, controller) {
    // Logic for handling FIN bits and buffer assembly
    controller.enqueue(chunk);
  }
});

socket.onmessage = async (event) => {
  // Utilizing the Blob/ArrayBuffer interface for memory efficiency
  const data = event.data;
  
  // In 2026, logic handles binary fragments via Uint8Array concatenation
  // to avoid the O(n^2) cost of string concatenation in older JS engines
  const buffer = await new Response(data).arrayBuffer();
  processBuffer(buffer);
};
```

### Complexity Analysis
The reconstruction of WebSocket frames is effectively $O(n)$ relative to the total payload size, where $n$ is the sum of bytes in all fragments. Because TCP guarantees the arrival of segments in order ($i, i+1, \dots, n$), the application-level reconstruction is memory-bound rather than compute-bound. 

$$T(n) = \sum_{i=1}^{k} \text{copy}(frame_i) \approx O(n)$$

*Note: The usage of `ArrayBuffer` and `TypedArrays` in 2026 environments ensures that the space complexity remains $O(n)$, minimizing garbage collection pressure compared to legacy string concatenation.*
<br>

## 12. Can WebSockets be used for broadcasting messages to multiple clients? If so, how?

### WebSocket Broadcasting: 2026 Architectural Audit

**WebSockets** support true **bidirectional full-duplex communication**. While the protocol defines point-to-point connections between a client and a server, broadcasting is achieved by maintaining an application-level registry of active connections and iterating over them. 

### Modern Broadcasting Mechanism
- **Connection Registry**: The server manages a set of active socket descriptors.
- **Complexity**: Broadcasting a message to $N$ connected clients incurs an $O(N)$ overhead per message.
- **Horizontal Scaling**: In 2026 production environments, simple memory-resident sets (as shown in the previous code) fail under load-balanced clusters. **Pub/Sub brokers** (e.g., Redis, NATS, or RabbitMQ) are now the standard for synchronizing state across multiple server nodes.

### Optimized Code Example (Python 3.14+)
Using modern `asyncio` patterns and typed connection management.

```python
import asyncio
from typing import Set
from websockets.server import serve, WebSocketServerProtocol

# Type-hinted connection tracking
active_clients: Set[WebSocketServerProtocol] = set()

async def handler(websocket: WebSocketServerProtocol):
    active_clients.add(websocket)
    try:
        async for message in websocket:
            # Broadcast pattern: Concurrently propagate to all clients
            if active_clients:
                tasks = [client.send(message) for client in active_clients]
                await asyncio.gather(*tasks, return_exceptions=True)
    finally:
        active_clients.remove(websocket)

async def main():
    async with serve(handler, "localhost", 8765):
        await asyncio.get_running_loop().create_future()  # Run forever

if __name__ == "__main__":
    asyncio.run(main())
```

### Architectural Best Practices (2026)

*   **Broadcast Reliability**: The naive loop $O(N)$ is susceptible to slow-client head-of-line blocking. Utilize `asyncio.gather` with `return_exceptions=True` to prevent a single disconnected client from crashing the broadcast loop.
*   **Pub/Sub Decoupling**: For distributed systems, replace the local `set()` with an external event bus. When a server node receives an event, it publishes to a Redis channel; all server instances subscribe to this channel and push to their locally connected clients.
*   **Connection Lifecycle**: Always implement **Heartbeat/Ping-Pong** mechanisms (handled natively by the `websockets` library) to prune stale connections ($O(1)$ cleanup) and prevent memory leaks.
*   **Protocol Considerations**: For scenarios requiring unidirectional broadcasting (server-to-client only), evaluate **Server-Sent Events (SSE)** over HTTP/2, which provides automatic reconnection and lower header overhead compared to the WebSocket handshake.
<br>

## 13. What is the difference between WebSockets and Server-Sent Events (SSE)?

### Comparative Audit: WebSockets vs. Server-Sent Events (SSE)

**WebSockets** and **Server-Sent Events** (SSE) represent distinct architectural patterns for streaming data. As of 2026, SSE is universally supported across all modern browsers (including full implementation in WebKit/Safari), rendering the previous "niche" classification obsolete.

### Key Distinctions

#### WebSockets
- **Characteristics**: **Full-duplex**, bidirectional communication over a single, long-lived TCP connection. Enables low-latency, stateful interactions where both the client and server act as peers.
- **Protocol**: Operates on a custom framing protocol (RFC 6455) initiated via an HTTP 1.1/2/3 upgrade request.
- **Complexity**: Requires **state management** on both ends. Developers must manually implement heartbeat mechanisms (ping/pong) to detect silent connection drops.
- **Use Case**: High-frequency, interactive applications requiring sub-millisecond latency, such as **collaborative editing (CRDTs)**, multiplayer gaming, and high-frequency trading dashboards.

#### Server-Sent Events (SSE)
- **Characteristics**: **Unidirectional** (Server-to-Client). Operates over standard HTTP. Once the connection is established, the server pushes text-based event streams to the client.
- **Protocol**: Native **HTTP**. It leverages standard transport layers, meaning it benefits from HTTP/2 multiplexing and HTTP/3 (QUIC) stream-level features without requiring a custom protocol handshake.
- **Features**: Built-in support for **automatic reconnection**, event ID tracking for message recovery, and lightweight consumption via the `EventSource` API. 
- **Use Case**: Low-overhead streaming of data updates, such as **LLM response generation** (streaming inference tokens), live notification feeds, and telemetry monitoring.

### Technical Comparison Summary

| Feature | WebSockets | Server-Sent Events (SSE) |
| :--- | :--- | :--- |
| **Communication** | Full-Duplex (Bidirectional) | Unidirectional (Server -> Client) |
| **Transport** | Custom TCP/HTTP Upgrade | Native HTTP (1.1, 2, 3) |
| **Connection Overhead** | High (Stateful) | Low (Stateless HTTP) |
| **Data Format** | Binary or Text (Custom) | UTF-8 Text (EventStream) |
| **Browser Support** | Universal | Universal (including Safari/iOS) |
| **Complexity** | $O(n)$ state sync logic | $O(1)$ stream consumption |

#### 2026 Architectural Guidance
*   **Prefer SSE** for read-heavy or update-heavy applications (e.g., real-time monitoring, AI token streaming) due to the reduced resource overhead of standard HTTP and native automatic reconnection. 
*   **Prefer WebSockets** only when the application requires true peer-to-peer messaging (e.g., VoIP, gaming). Avoid WebSockets for simple "server-to-client" updates to prevent unnecessary TCP handshake overhead and state management complexity.
<br>

## 14. Explain how a WebSocket connection is closed.

### WebSocket Connection Termination (2026 Standard)

A **WebSocket connection** (RFC 6455) is a persistent, full-duplex communication channel. Termination follows a defined **Close Handshake** procedure to ensure data integrity.

### Closure Scenarios

#### The Close Control Frame
Termination is governed by the exchange of **Close frames** (Opcode `0x8`). When one endpoint initiates closure, it sends a Close frame containing an optional status code and reason. The peer must acknowledge this with a corresponding Close frame before the underlying **TCP connection** is severed.

#### The CloseEvent Interface
Upon termination, the browser triggers a `CloseEvent` on the `WebSocket` object:

*   **`code`**: An `unsigned short` indicating the closure status (e.g., `1000` for Normal Closure).
*   **`reason`**: A `USVString` providing a human-readable explanation.
*   **`wasClean`**: A `boolean` (True if the connection closed gracefully via the handshake).

### Closing via Client API

1. **Graceful Closure**: The client invokes `socket.close(code, reason)`. The browser initiates the handshake and waits for the server to acknowledge.
2. **Abrupt Termination**: Triggered by network interface loss or `AbortController` signal integration, resulting in `wasClean = false`.

### Closing via Server-Side

1. **Graceful Closure**: The server sends a Close frame. The client, upon receipt, must respond with its own Close frame.
2. **Protocol Error**: If the server detects a violation of the WebSocket protocol (e.g., malformed frames), it terminates the connection immediately with status `1002`.

### 2026 Lifecycle Management

Modern applications leverage the **Streams API** or **WebTransport** for high-performance needs, but for standard WebSockets, connection stability is managed via `keepalive` mechanisms at the transport layer to prevent silent TCP half-open states.

### Implementation: Modern Closure Handling

```javascript
// React 19 / Modern Browser Context
const socket = new WebSocket('wss://api.example.com/v1/stream');

socket.onclose = (event) => {
  const { code, reason, wasClean } = event;
  
  if (wasClean) {
    console.info(`Connection closed cleanly [${code}]: ${reason}`);
  } else {
    // Implement exponential backoff reconnection logic
    console.error(`Connection dropped unexpectedly [${code}]`);
    attemptReconnection();
  }
};

// Standardized closure invocation
const handleShutdown = (reason) => {
  // 1000: Normal Closure
  socket.close(1000, reason || 'Client-side termination');
};

// React 19: Clean up during component unmount
import { useEffect } from 'react';

function useWebSocket(url) {
  useEffect(() => {
    const ws = new WebSocket(url);
    return () => ws.close(1000, 'Component unmounted');
  }, [url]);
}
```

### Technical Note on Status Codes
As of 2026, status codes follow IANA registry standards:
*   **1000**: Normal Closure (Success).
*   **1001**: Going Away (Browser navigation or server shutdown).
*   **1006**: Abnormal Closure (Signal for reconnection logic).
*   **1011**: Internal Error (Server-side exception).
<br>

## 15. What fallback mechanisms can be used if WebSockets are not supported by a browser or server?

### Modernization Audit: Real-Time Communication Fallbacks

In 2026, the architectural standard for real-time web communication relies on the **WebSocket API (RFC 6455)**, **WebTransport**, and **Server-Sent Events (SSE)**. Legacy polling methods are discouraged due to high overhead on $O(n)$ network traffic and server resource exhaustion. 

Modern applications favor **graceful degradation** via libraries like Socket.io or SignalR, which abstract these fallback chains.

---

### Polling & Streaming Evolution

#### Short Polling (Deprecated/Legacy)
*   **Mechanism**: Periodic HTTP requests.
*   **Assessment**: High latency and significant server overhead ($O(n)$ requests over time). Use only if extreme backward compatibility is required.

#### Long Polling (Deprecated)
*   **Mechanism**: Server holds request until data is available. 
*   **Assessment**: Mitigates latency but suffers from **Head-of-Line (HoL) blocking** and connection overhead. Avoid in 2026 deployments.

#### Server-Sent Events (SSE)
*   **Mechanism**: Unidirectional, text-based stream over HTTP.
*   **2026 Status**: Re-emerged as the preferred standard for AI streaming (e.g., LLM tokens) and unidirectional updates. Native browser support is excellent.
*   **Pros**: Efficient, built-in reconnection logic, lower overhead than WebSockets.

#### HTTP/3 and WebTransport
*   **Mechanism**: A modern successor to WebSockets, operating over QUIC.
*   **2026 Status**: **The industry standard for low-latency, multiplexed communication.** It solves HoL blocking and provides both reliable and unreliable data streams.

---

### Implementation Patterns (2026 Standard)

#### Modern Fetch/SSE Implementation
```javascript
// Using ReadableStream for efficient processing (2026 Standard)
async function consumeStream(url) {
  const response = await fetch(url, { headers: { 'Accept': 'text/event-stream' } });
  const reader = response.body.pipeThrough(new TextDecoderStream()).getReader();

  while (true) {
    const { value, done } = await reader.read();
    if (done) break;
    processData(value);
  }
}
```

#### WebTransport (The 2026 WebSocket Alternative)
```javascript
// WebTransport provides high-performance, low-latency streams
const transport = new WebTransport('https://api.example.com:443/ws');
await transport.ready;

const stream = await transport.createBidirectionalStream();
const writer = stream.writable.getWriter();
await writer.write(new TextEncoder().encode('Hello, Server!'));
```

---

### Architectural Recommendations

| Mechanism | 2026 Usage | Rationale |
| :--- | :--- | :--- |
| **WebTransport** | **Primary** | Multiplexed, QUIC-based, solves HoL blocking. |
| **SSE** | **Secondary** | Best for unidirectional data (AI streaming/dashboards). |
| **WebSockets** | **Legacy/Compat** | Maintain for existing infrastructure; migration is recommended. |
| **Polling** | **Prohibited** | Use only for edge cases where persistent streams are blocked. |

### Security & Reliability
*   **TLS/QUIC**: All real-time streams must operate over `WSS` or `HTTP/3` (TLS 1.3). Encryption is mandatory.
*   **Backpressure**: Implement `ReadableStream` backpressure to ensure that slow clients do not cause memory spikes on the server.
*   **Connection Resilience**: Use `Exponential Backoff` for reconnection logic ($T = min(cap, base \times 2^{attempt})$) to prevent "thundering herd" server crashes.
<br>



#### Explore all 100 answers here 👉 [Devinterview.io - WebSockets](https://devinterview.io/questions/web-and-mobile-development/websocket-interview-questions)

<br>

<a href="https://devinterview.io/questions/web-and-mobile-development/">
<img src="https://firebasestorage.googleapis.com/v0/b/dev-stack-app.appspot.com/o/github-blog-img%2Fweb-and-mobile-development-github-img.jpg?alt=media&token=1b5eeecc-c9fb-49f5-9e03-50cf2e309555" alt="web-and-mobile-development" width="100%">
</a>
</p>

