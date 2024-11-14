# Typeahead

## Overview

This repository outlines the design and implementation details of a scalable, high-performance Typeahead System. The system provides real-time suggestions to users based on their input, ensuring low latency and high availability.

---

## Functional Requirements (FRs)

1. **Keyword Suggestions**:
   - Provide up to 10 suggestions based on user input.

2. **Ranking**:
   - Rank suggestions based on trend and popularity.

3. **Personalized Suggestions**:
   - Tailor results using user preferences or historical data.

4. **Categorization**:
   - Group suggestions by predefined categories for better context.

---

## Non-Functional Requirements (NFRs)

1. **High Availability & Eventual Consistency**:
   - Ensure service continuity even under partial failures.
   
2. **Low Latency**:
   - Target response times under 100ms.

3. **Graceful Degradation**:
   - Maintain core functionality during partial outages.

4. **Resilience & Failover**:
   - Implement redundancy for fault tolerance.

5. **Reliability**:
   - Ensure consistent and accurate results.

6. **Caching**:
   - Employ distributed caching to reduce latency.

---

## Scalability Considerations

### Query Load
- **100K concurrent queries/sec**:
  - Requires 100MB/s bandwidth for suggestions.
  - Generates 50MB/s logs (~5TB/day).

### Keyword Storage
- **10 Billion Keywords (~100GB)**:
  - Sharded based on the first letter (26 shards).
  - Uniform shard size: ~4GB/shard.

### Replication Strategy
- Hotspot shards (~80% traffic) replicated 4 times.
- Remaining shards (~20% traffic) replicated 3 times.
- Total storage: ~400GB after replication.

### Caching
- Cache 5% of the data (~5.5GB).
- With 3 replicas, total cache size: ~16.5GB (~20GB).
- Target hit ratio: 70%.

### Data Structure Optimization
- **Trie**:
  - Reduces storage by 25%, saving ~100GB.
  - Optimized total storage: ~300GB.

---

## System Design Components

1. **Sharded Database**:
   - Partition keywords to ensure even load distribution.
   
2. **Distributed Caching**:
   - Leverage Redis for caching frequently accessed keywords.

3. **Replication**:
   - Ensure data redundancy and availability.

4. **Efficient Data Structures**:
   - Use tries to optimize keyword storage and retrieval.

5. **Resiliency**:
   - Autoscaling, load balancing, and failover mechanisms.

---

## Performance Metrics

- **Latency**: <100ms for global queries.
- **Hit Ratio**: >70% for cached queries.
- **Throughput**: 100K queries/sec.
