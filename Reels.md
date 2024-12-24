# Reels - High-Level Design (HLD)

## Requirements - 

<img width="337" alt="Screenshot 2024-12-23 at 9 57 45 PM" src="https://github.com/user-attachments/assets/4b7e920c-03b6-4bd3-bc4c-88dfcdfaabe1" /> 
<img width="1207" alt="Screenshot 2024-12-24 at 8 51 10 PM" src="https://github.com/user-attachments/assets/eec73743-e51b-40da-bdc7-b46304ac5500" />
<img width="1253" alt="Screenshot 2024-12-24 at 8 51 32 PM" src="https://github.com/user-attachments/assets/5c685480-d12c-4c13-a52c-90b3d5dca0dd" />


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

