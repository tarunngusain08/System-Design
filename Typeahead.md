# TypeAhead

## Overview

This TypeAhead system provides real-time keyword suggestions based on user input. The design supports high scalability, low latency, and fault tolerance to handle high query volumes efficiently. 

---

## Functional Requirements (FRs)

1. **Suggest 10 keywords** based on user input.
2. **Rank suggestions** by trends and popularity.
3. **Categorize suggestions** for better user experience.
4. **Personalized recommendations** tailored to individual users.

---

## Non-Functional Requirements (NFRs)

1. **High Availability**: Ensures continuous service with eventual consistency.
2. **Low Latency**: Provides responses in under 100ms.
3. **Graceful Degradation**: Maintains functionality under partial failures.
4. **Resilient Architecture**: Implements robust failover mechanisms.
5. **Caching**: Reduces read latency for frequently accessed data.

---

## System Scalability

### Data Growth Projections
- 1.825 trillion queries over 5 years (~2TB).
- Keyword deduplication reduces storage to ~400GB.
- **Sharding Strategy**:
  - **26 shards** for uniform data distribution.
  - Hotspot shards handle 80% of traffic.  
  - Replication factor: **4 for hotspots**, **3 for others**.
  - Total storage: **~1.45TB**.

---

## Storage Optimization

1. **Trie Data Structure**:
   - Space-efficient with 30% savings.
   - Reduces total storage to ~1TB.
   - Real-world savings range from 20-50% depending on prefix overlaps.

2. **Caching**:
   - Caches 5% of keywords (~80GB).
   - With a replication factor of 3, total cache size: **~250GB**.
   - Ensures a **>70% cache hit ratio**.

3. **Indexing Overhead**:
   - Adds ~300GB (20%) for fast keyword retrieval.

---

## Performance Metrics

1. **Network Bandwidth**:
   - ~100MB/s for 100K queries per second (10 suggestions/query).

2. **Logging**:
   - ~100MB/s, resulting in ~10TB of logs daily.
   - **Archival Strategy**: Implement periodic log compression to optimize storage.

---

## Trie Implementation Analysis

- **Node Adjacency**:
  - 250 bytes per node for adjacency lists.
  - Hotspots (10 frequently used characters): **~3PB**.
  - Non-hotspots (16 less-used characters): **~800TB**.
  - Total storage: **~3.8PB**.

---

## Key Components

1. **Sharded Database**:
   - Uniform and skewed traffic distribution handling.
   - High read throughput supported by replication.

2. **Trie-Based Storage**:
   - Efficient prefix-based keyword storage.
   - Reduces space requirements significantly.

3. **Distributed Cache**:
   - Optimized for frequently accessed data.
   - High cache hit ratios to lower response times.

4. **Failover Mechanisms**:
   - Automatic recovery during failures.
   - Ensures minimal downtime.

---

## Future Considerations

1. **Trie Optimizations**:
   - Explore compact data structures like Patricia tries.
2. **Dynamic Caching**:
   - Implement adaptive caching policies to optimize memory use.
3. **Traffic Assumptions**:
   - Continuously validate and fine-tune hotspot assumptions based on real usage data.
