# Zepto

<img width="1373" alt="Screenshot 2024-11-23 at 12 14 10 PM" src="https://github.com/user-attachments/assets/aa306441-451f-4ca1-9dc3-c0531da97f2b">

This document outlines the high-level design (HLD) for **Zepto**, a highly scalable and efficient e-commerce platform, focusing on fast grocery deliveries. It provides a breakdown of the functional and non-functional requirements, architecture, scaling considerations, and services used in the system.

---

## **Functional Requirements (FRs)**

1. **Login/Registration**  
   - User authentication and session management.  
   - RBAC (Role-Based Access Control).

2. **Track Location**  
   - Fetch and store user locations to provide location-specific inventory and delivery estimates.

3. **Inventory Management**  
   - Real-time inventory tracking and updates with cache warm-up.

4. **Categorization**  
   - Organize and manage product categories for a better browsing experience.

5. **Cart Management**  
   - Add, remove, and update items in the cart with persistence and TTL.

6. **Search Products**  
   - Full-text search and filtering with Elasticsearch and Redis cache for fast reads.

7. **Notifications**  
   - Send push notifications, in-app updates, emails, and WhatsApp messages.

8. **Payments**  
   - Handle secure payment processing, retries, and status tracking.

9. **Raise/Cancel Orders**  
   - Manage order lifecycle: creation, updates, cancellation, and processing.

10. **Exchange**  
    - Allow customers to request product exchanges.

---

## **Non-Functional Requirements (NFRs)**

1. **Performance**  
   - Low latency for cart and product views (~2 seconds).  
   - Optimal latency for payments (~5 seconds).

2. **Availability**  
   - Highly available system with eventual/session consistency.

3. **Resilience**  
   - Graceful degradation and failover mechanisms to ensure uninterrupted service.

4. **Scalability**  
   - Scale dynamically to handle spikes during peak traffic.

5. **Caching**  
   - Cache warm-up for high-demand periods with LRU and LFU policies for real traffic.

6. **Idempotency**  
   - Ensure idempotent operations for critical flows like payments and order updates.

---

## **Scale Estimation**

1. **Users**:  
   - 10 Million users; ~10 GB data.

2. **Products**:  
   - 50K products; ~100 MB metadata and 5 GB blob storage.

3. **Orders**:  
   - 500K orders/day; ~1 TB over 5 years.

4. **Payments**:  
   - 500K payments/day; ~100 GB over 5 years.

5. **Notifications**:  
   - 5 Million notifications/day across push, in-app, email, and WhatsApp.

6. **Traffic**:  
   - Average: 25 writes/sec.  
   - Peak: ~80 writes/sec (during sales and holidays).

---

## **Architecture**

The system follows a **microservices-based architecture** with the following key services:

### **API Gateway (AG)**

- Handles requests from users and routes them to the appropriate service.  
- Integrated with Istio for traffic management, rate limiting (RL), and load balancing (LB).  
- Manages user sessions and propagates user context across services.

---

### **Services**

1. **User Service**  
   - Database: PostgreSQL (future migration to Spanner for global scalability).  
   - Handles user data, authentication, and RBAC.

2. **Search Service**  
   - Indexing: Elasticsearch for full-text search and filtering.  
   - Cache: Redis for fast reads.  
   - Data Sync: Batched updates from Product DB to Elasticsearch.

3. **Cart Service**  
   - Cache: Redis with persistence and TTL.  
   - Database: Sharded Cart DB with write-heavy, batched updates.  
   - Sharding: Based on user ID with consistent hashing and virtual nodes for failover.

4. **Notification Service**  
   - Queue: Kafka for asynchronous processing and retries with DLQs.  
   - Channels:  
     - **In-app**: Low latency.  
     - **Push**: High reliability.  
     - **Email**: Promotional, eventual consistency.

5. **Payment Service**  
   - Gateway: Integrates with 3rd-party payment providers via a circuit breaker.  
   - Queue: Kafka for retries and DLQs.  
   - Database: Payment DB for transaction history.  
   - ETL: Periodic ingestion into a blob store.

6. **Order Service**  
   - Cache: Redis for local reads and write-back.  
   - Queue: Kafka for high-throughput asynchronous writes.  
   - Database: Orders DB for persistent storage.  
   - ETL: Dumps older data into a blob store for archival.

7. **Inventory Service**  
   - Cache: Redis for real-time inventory reads and updates.  
   - Parser: Cloud function to validate incoming CSV data.  
   - State Machine: Manages inventory updates and cache warming.  
   - Database: Sharded Inventory DB with replicas for quick reads.

---

### **Design Patterns and Strategies**

1. **Sharding**  
   - **User DB**: Currently not sharded, but future-ready for Spanner migration.  
   - **Cart DB and Inventory DB**: Sharded by user ID or product ID using consistent hashing.

2. **Cache Warm-Up**  
   - Redis pre-warming for peak traffic periods like sales and holidays.  
   - TTL setup to optimize memory usage and performance.

3. **Circuit Breaker**  
   - For external dependencies like payment gateways to prevent cascading failures.

4. **Retry Mechanisms**  
   - Kafka handles async retries with exponential backoff and DLQs.

5. **Horizontal Pod Autoscaling (HPA)**  
   - Autoscale based on traffic patterns, with pre-scaling for predictable peak times using historical data.

---

## **Data Flow**

1. **User Registration/Login**  
   - `User → AG → User Service → User DB`.

2. **Search Products**  
   - `User → AG → Search Service → Elasticsearch → Redis Cache`.  
   - Batched updates from Product DB to Elasticsearch.

3. **Cart Management**  
   - `User → AG → Cart Service → Redis (write-through) → Sharded Cart DB`.

4. **Place Orders**  
   - `User → AG → Order Service → Redis → Kafka → Orders DB`.

5. **Inventory Updates**  
   - CSV uploads validated by **Cloud Function**.  
   - Updates managed by **State Machine** and written to **Sharded Inventory DB**.

6. **Notifications**  
   - Messages enqueued in **Kafka**, then routed to respective channels (in-app, push, email).

7. **Payments**  
   - `User → AG → Payment Service → Kafka → Payment Gateway → Payment DB`.

---

## **Scalability and Resilience**

1. **Caching**:  
   - LRU and LFU for cache eviction.  
   - Persistent Redis setup for durability.

2. **Scaling**:  
   - Services scaled dynamically using HPA based on traffic patterns.  
   - Pre-scaling during predictable peak loads.

3. **Resilience**:  
   - Circuit breakers and retries for external dependencies.  
   - Graceful degradation in case of failures (e.g., fallback to cached results).

---

## **Monitoring and Observability**

- Use Istio for tracing, monitoring, and debugging traffic flows between services.  
- Centralized logging and metrics collection (e.g., Prometheus + Grafana).  
- Alerts for DLQ, high latency, or resource bottlenecks.

---

This design ensures **high availability**, **low latency**, and **scalable architecture**, meeting both functional and non-functional requirements.
