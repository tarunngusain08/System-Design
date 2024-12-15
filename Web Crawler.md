# Web Crawler Design

<img width="1512" alt="Screenshot 2024-12-11 at 3 17 01 PM" src="https://github.com/user-attachments/assets/23a6cf9c-d46f-4ae3-9153-6b09883ad7c8" />

## Overview
This document outlines the design and architecture of a distributed web crawler that efficiently fetches, processes, and stores metadata and HTML content from billions of web pages. The crawler is designed with scalability, fault tolerance, and politeness policies in mind.

---

## Functional Requirements (FRs)
1. **Distributed Crawling**: Handle crawling across multiple servers.
2. **HTML Content Parsing**: Access and parse HTML content.
3. **Follow Sub-links**: Respect `robots.txt` rules and maintain politeness.
4. **Metadata Storage**: Store searchable metadata efficiently.

---

## Non-Functional Requirements (NFRs)
1. **High Availability**: Ensure minimal downtime.
2. **Idempotency**: Prevent duplicate crawls.
3. **Efficient Storage**: Optimize for space and retrieval.
4. **Asynchronous Queuing**: Use non-blocking mechanisms for URL handling.
5. **Dynamic Crawl Rate**: Adapt crawl speed based on system load.
6. **Failure Recovery**: Retry failed tasks and use dead-letter queues (DLQ).
7. **Distributed Indexing**: Support scalable metadata storage and querying.

---

## Scale Considerations
- **Web Pages**: 10 billion
- **Data per Site**: ~1MB/site, ~10PB total
- **Metadata per Site**: ~1KB/site, ~10TB total
- **Crawling Time**: 10 days (~1M seconds)
- **Threads and Servers**:
  - 10 billion URLs / 1M seconds = ~10K threads
  - Requires 5K-8K cores (~150-250 servers with 32 cores each)
- **Hashing for URLs**:
  - 64-bit hash for URL deduplication (requires ~80GB in Redis)

---

## Architecture

### **Components**

#### **Kafka**
- **Topics**: Separate topics for URL queue, retry queue, and dead-letter queue.
- **Partitioning**:
  1. Based on hosts to reduce DNS query time and enhance locality.
  2. Based on hash for simplicity.
- **DLQ**: Tracks tasks exceeding retry limits (3-5 attempts).

#### **Redis**
- **Use Cases**:
  1. Rate limiting.
  2. DNS caching.
  3. Caching crawl delays.
  4. Tracking processed URLs.
- **Policies**:
  - Use `allkeys-lru` or `volatile-ttl` for non-critical data.
  - Monitor memory usage and adjust TTLs dynamically.
- **Replication**:
  - Use Redis Sentinel or Cluster for asynchronous replication with a strong consistency model.
  - Employ quorum-based writes for critical operations.

#### **Crawler Pool**
- Distributed crawlers that:
  1. Read URLs from Kafka.
  2. Retry failed tasks with exponential backoff.
  3. Write raw HTML content to S3.
  4. Query Redis to avoid duplicate processing.
- **Politeness Policies**:
  - Respect `crawlDelay` using cached values.
  - Query DNS resolvers in parallel for reduced latency.

#### **S3 Storage**
- Stores raw HTML content for asynchronous parsing.
- Implements lifecycle policies to manage storage costs.
- Uses consistent hashing for quick retrieval.

#### **Parser Pool**
- Parses raw HTML from S3 and extracts metadata.
- Writes parsed metadata to Elasticsearch.
- Supports specialized parsers for non-HTML content (e.g., PDFs, JSON).

#### **Elasticsearch**
- Stores metadata for efficient querying.
- Metadata schema:
  ```json
  {
    "id": "unique_id",
    "url": "site_url",
    "s3_link": "html_content_location",
    "crawl_delay": "delay_in_seconds",
    "crawled_last_time": "timestamp"
  }
  ```
- Implements sharding for scalability (e.g., shard by URL hash).

#### **Smart Scheduler**
- Prioritizes URLs based on criteria (e.g., PageRank, freshness).
- Fetches metadata from Elasticsearch to populate Kafka topics.
- Implements a heuristic to detect infinite scrolling and session-based URLs (e.g., `?page=`).

---

## Politeness and Fault Tolerance
1. **Robots.txt Handling**:
   - Pre-fetch and TTL cache `robots.txt` for frequently crawled hosts.
   - Priority mechanism for sites with frequent updates.
2. **Error Handling**:
   - Classify HTTP error codes:
     - **Permanent**: 404 (immediate DLQ).
     - **Transient**: 500, 429 (retry with backoff).
3. **DNS Pool Resolver**:
   - Round-robin across multiple DNS servers to avoid overloading a single resolver.

---

## Monitoring and Optimization
1. **Monitoring**:
   - Use Prometheus/Grafana for tracking crawl rate, storage usage, and failures.
   - Distributed tracing (e.g., Jaeger) for debugging.
2. **Optimization**:
   - Compress raw HTML before storing in S3.
   - Use token bucket algorithms for rate limiting.

---

## Future Enhancements
1. **Adaptive Learning**:
   - Use ML models to predict high-value pages.
2. **Geo-distributed Crawling**:
   - Deploy region-specific crawlers for better latency and compliance.
3. **Enhanced Metadata**:
   - Enrich metadata with semantic analysis (e.g., OpenGraph tags).

---

## Summary
This design ensures scalability, resilience, and efficiency while adhering to web crawling best practices. It incorporates robust queuing, storage, and fault-tolerance mechanisms to handle billions of pages effectively.
