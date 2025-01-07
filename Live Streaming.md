# Live Streaming System Design

## Problem Statement
Design a system to deliver live video content with minimal latency to millions of concurrent viewers. The platform must provide a seamless and interactive experience for viewers while ensuring scalability and reliability.

---

## Functional Requirements (FRs)

### 1. Display All Live Streams
- Provide a list of all ongoing live streams.
- Include **filters** and **search functionality** to help users discover streams.
- Display **metadata** such as:
  - Titles
  - Viewer count
  - Tags (e.g., categories, topics)

### 2. Stream a Live Video
- Support various devices (e.g., mobile, desktop, smart TVs).
- Ensure compatibility with popular streaming protocols like:
  - **HLS** (HTTP Live Streaming)
  - **Dash** (Dynamic Adaptive Streaming over HTTP)
  - **WebRTC** for ultra-low latency.

### 3. Reactions
- Allow viewers to send real-time reactions (e.g., likes, emojis).
- Aggregate and deliver reactions **in near real-time** to avoid overwhelming clients.

### 4. Chat
- Provide a scalable chat system:
  - Use **partitioning** (sharding by channel/stream) or **pub-sub models**.
  - Include **moderation features** (e.g., profanity filtering, banning users).
- Ensure the chat infrastructure scales with stream traffic.

### 5. Send Tip
- Enable viewers to tip streamers securely and quickly.
- Tips should reflect **in real-time** with animations or acknowledgments for streamers.

---

## Non-Functional Requirements (NFRs)
1. **High Availability**: Ensure system uptime with fault tolerance.
2. **Eventual Consistency**: Overall consistency, with high consistency for payments and metadata.
3. **Low Latency**: Maintain latency ~5 seconds for video streaming.
4. **Graceful Degradation**: Handle failures without a complete system crash.
5. **High Throughput**:
   - Handle large volumes of messages, reactions, and payments.
6. **Adaptive Bitrate Streaming**: Adjust video quality dynamically based on the viewer's network conditions.
7. **Edge Computing**: Use CDN/edge servers to reduce latency and load on origin servers.
8. **Authentication and Security**: Secure the system with robust AuthN/AuthZ mechanisms.

---

## Scale Considerations
- **Concurrent Users**: 10 million concurrent viewers.
- **Live Streams**: 
  - 10,000 live streams on average.
  - Each stream with ~1,000 viewers.
- **Latency**: ~5 seconds.
- **Traffic Distribution**: 
  - Top 1-2% of streams drive 80-90% of the traffic.
  - Popular streams: ~50,000 to 100,000 peak viewers/stream.
- **Messaging**:
  - Average: 100,000 messages per second.
  - Peak: 20,000 messages per stream per second.
- **Reactions**:
  - Average: 1 million reactions per second.
  - Peak: 100,000 reactions per stream per second.
- **Payments**:
  - 10,000 payments per second.

---

## Goals
- Deliver a smooth live-streaming experience.
- Achieve scalability and reliability for high traffic.
- Provide real-time interactivity through chat, reactions, and tipping.
