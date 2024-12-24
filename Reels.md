# Reels - High-Level Design (HLD)

## Requirements - 

<img width="337" alt="Screenshot 2024-12-23 at 9 57 45 PM" src="https://github.com/user-attachments/assets/4b7e920c-03b6-4bd3-bc4c-88dfcdfaabe1" />

## Functional Requirements (FRs)

1. **Stream Short Videos:**
    - Stream short videos to users based on their preferences using HLS/DASH.
2. **User Interactions:**
    - Enable features like Like, Comment, and Share for each video.
3. **Ranking and Prioritization:**
    - Display videos based on ranking algorithms that consider user preferences, recency, and engagement.
4. **Follow/Subscribe:**
    - Allow users to follow content creators or subscribe to channels.
5. **Record and Upload Reels:**
    - Provide users with functionality to record and upload their own reels.
6. **Report Actions:**
    - Allow users to report inappropriate content for review and moderation.
7. **Save, Hide, Add Favorites:**
    - Provide options to save, hide, or mark videos as favorites for future reference.
8. **Personalized Recommendation Engine:**
    - Generate a personalized video feed using machine learning algorithms.

## Non-Functional Requirements (NFRs)

1. **High Availability:**
    - Ensure the platform remains operational at all times, with minimal downtime.
2. **Low Latency:**
    - Video load time should be less than 300ms.
    - Upload processing should take less than 1 second.
3. **Dynamic Streaming Quality:**
    - Use adaptive streaming technologies like HLS/DASH to deliver videos at optimal quality based on network conditions.
4. **Graceful Degradation:**
    - Ensure partial functionality in case of system failure.
5. **Async Uploads:**
    - Handle video uploads asynchronously to improve user experience.
6. **Global CDN:**
    - Use a Content Delivery Network (CDN) for fast, efficient content delivery worldwide.
7. **Sharding:**
    - Implement sharding at multiple levels to support scalability and load distribution.

## Scale

1. **Daily Active Users (DAU):**
    - Support 100 million DAU.
2. **Watch-to-Upload Ratio:**
    - Maintain a 1:1000 ratio, i.e., 1 upload for every 1000 watches.
3. **Daily Video Uploads:**
    - Handle 100,000 video uploads daily.
        - Average video size: 100MB.
        - Total data generated: 10TB/day.
4. **Watch Time:**
    - Average watch time: 12 minutes per user per day.
        - Total watch time: 1.2 billion minutes (~200 million hours).
5. **Bandwidth:**
    - Data served: ~1PB/day.
    - Network bandwidth: ~10Pbps.
        - Requires ~1,000 servers with 10Tbps bandwidth.
6. **Peak Traffic Estimation:**
    - Peak upload traffic: 15-20 videos per user per day.
    - Total peak traffic: 2 billion videos/day.
    - 50% of traffic occurs in 4 hours (peak hours).
    - Peak requests: ~100K requests/second.



### **Upload Flow**  

1. **Ensuring Data Consistency During Asynchronous Uploads:**  
   - By relying on Kafka for synchronization, you achieve strong durability guarantees. The dead-letter queue (DLQ) ensures that no data is lost if chunks fail repeatedly.  
   - Adding a checksum (e.g., MD5 or CRC32) per chunk can further ensure integrity. Chunks can be verified during validation and re-verified upon retrieval from the blob.  

2. **Synchronization Between m3u8 Files and Blobs:**  
   - **Approach:** Generate the m3u8 manifest dynamically after all chunks are successfully uploaded and validated.  
   - **Fallback:** If chunks fail to upload or validate, mark the m3u8 file as incomplete, triggering a retry workflow. Notify users proactively if their video is pending further processing.  

---

### **Download Flow**  

1. **Cache Eviction Strategy in Memcached During Peak Traffic:**  
   - Use **cache expiration policies** with a Least Recently Used (LRU) strategy to remove stale data while prioritizing active or trending videos.  
   - Pre-warm the cache with high-priority data (e.g., trending videos, recently uploaded content) to avoid excessive misses.  

2. **Handling Poor Network Conditions or Cancellations in Async Downloader:**  
   - Use adaptive retry logic in the downloader.  
     - Reduce download quality (from HD to SD) based on the network.  
     - Resume downloads from the last completed chunk.  
   - For cancellations, delete partially downloaded chunks locally and mark the session incomplete for potential resumption.  

3. **Fallback Mechanism if CDN Fails to Serve Chunks:**  
   - Fallback to origin storage (blob storage) for direct pulls in case of CDN cache misses or failure.  
   - Implement health checks for CDNs to dynamically reroute requests to a backup CDN provider if the primary fails.  

---

### **Follow/Subscribe Flow**  

1. **Handling Race Conditions in UserDB:**  
   - Use **optimistic locking** to prevent overwriting changes. Versions or timestamps can ensure only the latest data is updated.  
   - Leverage distributed transactions if multiple user actions need to be processed atomically.  

2. **Scaling UserDB:**  
   - Partition or shard the database by user ID or region to distribute load.  
   - Consider using a distributed database like **Cassandra** or **CockroachDB** for horizontal scaling and fault tolerance.  

---

### **Performance Metrics and Observability**  

1. **Monitoring and Observability:**  
   - **For Kafka:** Track lag per partition, broker health, and consumer throughput. Use monitoring tools like Prometheus with Grafana or Kafka’s native JMX.  
   - **Uploads and Downloads:**  
     - Percentage of failed or retried uploads/downloads.  
     - End-to-end latency for video delivery and upload processing.  
   - **Cache Metrics:** Measure hit/miss rates, eviction rates, and TTL performance in Memcached.  

2. **Ranker SLA and Execution:**  
   - Define ranker SLAs to prioritize active users during computation. e.g., process rankings every 5 minutes for trending or newly uploaded videos.  
   - **Improvement:** For peak efficiency, break ranker jobs into micro-batches and execute them in parallel to reduce contention and delays.  

---

### **Suggestions for Edge Cases**  

1. **Slow Networks (Download Flow):**  
   - Implement progressive playback by serving only the lowest-quality chunks initially and switching to higher quality as more bandwidth becomes available.  

2. **Validation Failures:**  
   - Notify users immediately about failed uploads, including actionable messages like "Retry upload" or "Check network connection."  

3. **Thundering Herd:**  
   - Use request coalescing (combine similar queries) at the API gateway or service level.  
   - Precompute rankings for users in small geographical regions to optimize data reuse.  

4. **Cross-Region Traffic:**  
   - For global users, replicate hot video chunks across regions using secondary CDNs to reduce latency and ensure high availability.  

---
