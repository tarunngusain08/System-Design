# Uber Surge Pricing System

## Problem Overview

In a large-scale ride-hailing platform like Uber, millions of riders and drivers interact simultaneously across multiple regions. To ensure an optimal and balanced service, the platform must dynamically adjust pricing based on real-time supply and demand. This adjustment is known as **surge pricing**.

**Surge pricing** aims to:
- Motivate more drivers to offer rides when demand is high.
- Regulate demand by setting higher prices during peak times or in high-demand areas, making the system resilient to overloading and ensuring availability for those who need it most.

The system needs to process high volumes of data in real time, consider a range of factors (such as rider demand, driver availability, and environmental conditions), and quickly compute and display surge-adjusted pricing for each rider.

## Key Functional Requirements (FRs)

1. **Dynamic Pricing Calculation**: Riders should receive real-time, dynamically calculated prices that reflect current conditions.
2. **Demand and Supply Tracking**: The system should monitor ride requests per minute per area, weather conditions, and optimal routes to adjust prices accordingly.
3. **New User Pricing**: To encourage new users, they should receive slightly lower fares during their initial rides.
4. **Driver Incentives**: Surge pricing should reward drivers with a higher fare and fair compensation for offering rides in high-demand zones.

## Key Non-Functional Requirements (NFRs)

1. **High Availability and Resilience**: The system should handle regional fluctuations in demand without downtime.
2. **Low Latency**: Pricing calculations and updates should be nearly instantaneous to prevent ride cancellations due to delayed fare display.
3. **Graceful Degradation**: In case of high load, the system should maintain basic functionality and prioritize requests.
4. **High Throughput**: The system should process millions of requests per day efficiently.

## Limited Use Cases

1. **Real-Time Surge Adjustment**:
   - During peak hours or high-demand events (concerts, sports events), prices automatically increase in specific areas to incentivize more drivers to be available.
   - Example: A user requests a ride at 6 PM in a busy city center. Due to high demand, the fare is dynamically adjusted to account for surge pricing.

2. **Weather-Based Surge Pricing**:
   - If severe weather conditions (heavy rain, snow) increase demand for rides, surge pricing adjusts accordingly.
   - Example: Rain begins unexpectedly in a region, increasing rider requests. The system updates fares in affected areas to encourage more drivers to accept rides.

3. **Supply-Driven Adjustments**:
   - In areas where driver availability is low, the system raises prices to attract drivers from nearby regions.
   - Example: Late-night hours in suburban areas may have fewer drivers. Surge pricing incentivizes nearby drivers to move to that area.

4. **Optimized Fare for New Users**:
   - The system can adjust surge pricing slightly lower for new users to improve their experience and encourage app adoption.
   - Example: A new user opens the app for their first ride during a minor surge period. The fare displayed is slightly reduced to enhance their initial experience.

## Data Flow and Considerations

1. **Ride Requests and Driver Availability**: Tracks requests and drivers across different regions, and adjusts prices accordingly.
2. **Weather and Traffic Data**: Uses third-party APIs and local caching for weather updates to determine surge multipliers in adverse conditions.
3. **Notifications and Alerts**: Uses Kafka or similar message brokers for pushing surge alerts to users and drivers in real-time.
4. **Data Persistence**: Records historical data for analytics and future trend predictions.


<img width="972" alt="Screenshot 2024-11-12 at 3 28 38 PM" src="https://github.com/user-attachments/assets/15e75799-50a2-4a86-be13-9f0d93e3a4ba">

