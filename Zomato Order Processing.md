# Zomato Order Processing System

<img width="1361" alt="Screenshot 2025-01-20 at 7 15 52 PM" src="https://github.com/user-attachments/assets/21d7ffea-be4d-4a62-bee5-ef85bf4fed4c" />

## **Requirements**
### **Functional Requirements**
1. Customers can place an order within a 5 km radius.
2. Customers can update their order (e.g., delivery address, time) or cancel within specified timeframes:
   - **< 10 min**: 70% refund.
   - **< 30 min**: 40% refund.
   - **> 30 min**: No cancellation/refund allowed.
3. Orders maintain states: **Order Placed**, **Order in Processing**, **Order Completed**.
4. Notifications are sent to customers for all order state changes.
5. Order history is retained for customers for 6 months.
6. Payment handling includes refunds and vendor payment management.

### **Non-Functional Requirements**
1. High availability and fault tolerance.
2. End-to-end latency < 300 ms for critical flows.
3. Resilient and scalable architecture.
4. Multi-region support.
5. Data retention for 1 year (~100 GB). 

---

## **Scale**
1. **Daily Active Users (DAU)**: 1 million.
2. **Daily Orders**: ~200,000 (20% recurring orders).
3. **Order Size**: 500 bytes/order (~200 MB/day).
4. **Retention**:
   - 1 year: ~100 GB of orders data.
   - 5 years: ~400 GB.
5. **Caching**:
   - 10% caching per request (~100 bytes/order).
   - Cache invalidation with a 1-week TTL.

---

## **Major Components**

### **1. Order Service**
- **Purpose**: Handles order placement, updates, cancellations, and state transitions.
- **Key Technologies**:
  - Redis: For caching order details and reducing DB load.
  - PostgreSQL: For persistent storage of order data.
  - Kafka: For publishing order events to downstream services (e.g., analytics, notifications).

### **2. Payment Service**
- **Purpose**: Manages payments, refunds, and external gateway integrations.
- **Key Technologies**:
  - Redis: For caching payment statuses and reducing latency.
  - Payments DB: Stores all payment-related transactions.
  - Vendor Pool: Handles interactions with payment gateways, ensures retries and vendor fallback.
  - Kafka: Publishes payment events for reconciliation and analytics.

### **3. Notification Service**
- **Purpose**: Sends notifications to customers via SMS, push, or email.
- **Key Technologies**:
  - Kafka: Subscribes to order and payment events.
  - Vendor Pools: Abstracts third-party services for SMS, push, and email.
  - Monitoring: Tracks notification delivery and retry attempts.

### **4. Rate Limiter**
- **Purpose**: Prevents abuse by limiting API calls per user or key.
- **Key Technology**: Redis-based token bucket algorithm.

---

## **Design Highlights**
1. **Resilience**: Use of DLQs, retries, and circuit breakers for fault tolerance.
2. **Scalability**: Kubernetes for auto-scaling, Kafka partitions for high-throughput messaging.
3. **Observability**: Prometheus and Grafana for monitoring, ELK Stack for logs.
4. **Consistency**: Event-driven architecture ensures consistent state propagation across services.
