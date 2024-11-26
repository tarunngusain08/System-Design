# Typing Indicator for Whatsapp

<img width="1331" alt="Screenshot 2024-11-26 at 9 50 29 PM" src="https://github.com/user-attachments/assets/4812239b-5136-4905-97c2-ed57bde74041">



This project implements a scalable and resilient **Typing Indicator** system for WhatsApp, ensuring a seamless and low-latency experience for users while adhering to system performance and availability requirements.

---

### **Architecture Overview**

#### **Components**:
1. **API Gateway**  
   - Handles initialization, authorization, and upgrades.
   - Routes requests to appropriate backend services.

2. **Service Mesh**  
   - Manages inter-service communication with load balancing and observability.

3. **WebSocket Manager**  
   - Handles real-time streaming for typing indicators.
   - Interacts with core services for functionality:
     - **Chat Service**: For user messaging.
     - **Notification Service**: For updates.
     - **Emoji/Sticker Service**: For enhancements.

4. **Data Storage**:
   - **Redis**: Stores ephemeral typing states for quick, low-latency access.
   - **DynamoDB**: Maintains session data for high throughput and persistence.
   - **etcd**: Configuration storage for system-level settings.

---

### **Key Features**

1. **High Scalability**:  
   - Supports 1 billion DAUs with peaks of 1M messages/second.
   - Designed for future growth (2–3 billion users).

2. **Low Latency**:  
   - Typing indicators update in under 1 second.

3. **Resilience and Fault Tolerance**:  
   - Graceful degradation during outages.
   - Redis Cluster ensures redundancy for ephemeral data.

4. **Security**:  
   - WebSocket connections secured with TLS and JWT authentication.

5. **Monitoring**:  
   - Metrics collected via Prometheus, visualized in Grafana.
   - Distributed tracing implemented with Jaeger.

---

### **Design Considerations**

- **Ephemeral Data**: Typing indicators are transient and not stored unnecessarily.  
- **Graceful Degradation**: Typing indicators may be hidden or updated less frequently during degraded conditions.  
- **Regional Scaling**: WebSocket nodes deployed regionally to minimize user-to-user latency.  
- **Event-Driven Communication**: Backend services communicate via Kafka to decouple synchronous calls.  

---

### **Improvements**
- **Session Partitioning**: Optimized DynamoDB by partitioning session data geographically.  
- **Rate Limiting**: Protects from DDoS attacks by enforcing limits on WebSocket and API Gateway requests.  
- **Configuration Caching**: Reduces etcd hits by caching frequently accessed configurations.  

---

### **How It Works**

1. User connects to **WebSocket Manager** via **API Gateway**.
2. Typing states are stored in **Redis** for quick retrieval.
3. WebSocket Manager interacts with backend services for chat, notifications, and stickers.
4. Ephemeral typing states are cleared once a user stops typing or disconnects.
