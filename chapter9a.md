# **System Design – Notification System (Chapter 10)**  

## **Introduction**  
- A **notification system** delivers important messages to users, like breaking news, product updates, or reminders.  
- Notifications are sent through **three main channels**:  
  1. **Mobile Push Notification**  
  2. **SMS Message**  
  3. **Email**  

![image](https://github.com/user-attachments/assets/05a9061e-7b05-48a1-b08d-aad550cade5d)

---

## **Step 1: Understanding the Problem & Scope**  
### **Key Questions to Clarify Requirements**  
| **Question** | **Answer** |
|-------------|-----------|
| **What types of notifications?** | Push, SMS, Email |
| **Is it real-time?** | Soft real-time (delays allowed under heavy load) |
| **Supported devices?** | iOS, Android, Laptop/Desktop |
| **How are notifications triggered?** | By client apps & scheduled server-side events |
| **Can users opt out?** | Yes |
| **How many notifications per day?** | 10M Push, 1M SMS, 5M Emails |

---

## **Step 2: High-Level Design**  
### **Types of Notifications**  
#### **1. iOS Push Notification**  
- Uses **Apple Push Notification Service (APNS)**.  
- Needs:  
  - **Device Token** (unique ID for each device).  
  - **Payload** (JSON message format).  

#### **2. Android Push Notification**  
- Uses **Firebase Cloud Messaging (FCM)** (similar to APNS).  

#### **3. SMS Messages**  
- Uses **third-party providers** like **Twilio** or **Nexmo**.  

#### **4. Email Notifications**  
- Uses services like **SendGrid** or **Mailchimp** for better **delivery rates & analytics**.  


![image](https://github.com/user-attachments/assets/b82c1c2d-d260-41ec-aa0b-5462cc7090bf)

---

### **Contact Info Gathering Flow**  
- When a **user installs the app** or **signs up**, we collect:  
  - **Device Token** (for push notifications).  
  - **Phone Number** (for SMS).  
  - **Email Address** (for emails).  
- Data is **stored in a database** (user table & device table).  

📌 **Example**: A user may have multiple devices, so we need to send push notifications to **all registered devices**.

---

### **Notification Sending & Receiving Flow**  
#### **Initial Design (Figure 10-9)**  
1. **Services (1-N)**: Triggers notifications (e.g., billing reminder, delivery alert).  
2. **Notification System**: Central system that processes notifications.  
3. **Third-Party Services**: Sends messages to users (APNS, FCM, Twilio, SendGrid, etc.).  
4. **User Devices**: Receives notifications.  

![image](https://github.com/user-attachments/assets/b85633eb-ebff-4da2-81a8-b5eb84589488)

🔴 **Problems in Initial Design**:  
- **Single Point of Failure (SPOF)** – One server can fail.  
- **Hard to Scale** – Handles too many tasks in one place.  
- **Performance Bottleneck** – Waiting for third-party responses slows the system.  

---

### **Improved High-Level Design (Figure 10-10)**  
✔ **Fixing the Problems**:  
- **Separate Database & Cache** – Removes load from the notification server.  
- **Multiple Notification Servers** – Adds automatic horizontal scaling.  
- **Message Queues** – Decouples system components, preventing failures from affecting everything.  

![image](https://github.com/user-attachments/assets/a31a2d8b-ab5f-4c27-9ba4-76cb2a1ea16b)

#### **Updated Flow**  
1. **Service Calls Notification API**.  
2. **Notification Server Fetches Data** (User info, settings).  
3. **Sends Notification Event to a Queue**.  
4. **Workers Process Events from Queues**.  
5. **Workers Send Notifications via Third-Party Services**.  
6. **Third-Party Services Deliver Notifications to Users**.  

---

## **Step 3: Design Deep Dive**  
### **Reliability Considerations**  
#### **1. Preventing Data Loss**  
- Notifications can be delayed or re-ordered, **but never lost**.  
- Solution: **Store all notifications in a database** & use **retry mechanisms**.  

![image](https://github.com/user-attachments/assets/9c13bb1a-292b-4c03-9ad9-41c2dc97d0d3)

#### **2. Handling Duplicate Notifications**  
- Distributed systems can sometimes **send duplicate notifications**.  
- Solution: **Dedupe Mechanism** (checks Event ID before sending).  

---

### **Additional Components & Considerations**  
#### **1. Notification Templates**  
- **Predefined messages** to save time & maintain consistency.  
- Example Push Notification Template:  
  ```
  BODY: [ITEM NAME] is back! Order before [DATE].
  CTA: Order Now
  ```
  
---

#### **2. Notification Settings**  
- Users can **opt-out** of specific notifications.  
- Stored in the **Notification Settings Table**:  

| **Field** | **Description** |
|----------|--------------|
| `user_id` | Unique user ID |
| `channel` | Push, Email, or SMS |
| `opt_in` | True/False |

**Before sending a notification, the system checks if the user has opted in**.

---

#### **3. Rate Limiting**  
- Prevents sending **too many notifications** to the same user.  
- Prevents **users from disabling notifications completely** due to spam.  

---

#### **4. Retry Mechanism**  
- If **third-party services fail**, messages are **re-added to the queue**.  
- If failures persist, **alerts are sent to developers**.  

---

#### **5. Security in Push Notifications**  
- **APIs require authentication** using `appKey` & `appSecret`.  
- Only **trusted clients** can send push notifications.  

---

#### **6. Monitoring Queued Notifications (Figure 10-12)**  
- **Tracks number of pending messages in queues**.  
- If the queue gets too large → **Scale up worker nodes**.  

![image](https://github.com/user-attachments/assets/0268a14b-20c5-4729-85e9-a1f5070a2760)

---

#### **7. Event Tracking (Figure 10-13)**  
- Logs **open rate, click rate, engagement**.  
- Helps in **analyzing user behavior**.  

![image](https://github.com/user-attachments/assets/0a741d30-d484-4465-88da-2f417ef3d1fe)

---

### **Updated Design (Figure 10-14)**
🔹 **Improvements Over Previous Design**:  
✔ **Rate Limiting & Authentication Added**.  
✔ **Retry Mechanism for Failed Notifications**.  
✔ **Notification Templates for Efficiency**.  
✔ **Tracking & Monitoring for Future Enhancements**.  

![image](https://github.com/user-attachments/assets/29a5012f-8824-4793-8195-94c9e1243706)

---

## **Step 4: Wrap-Up**  
- Notifications **keep users informed** (e.g., Netflix updates, payment confirmations).  
- **Key Design Features**:  
  - **Supports Push, SMS, Email**.  
  - **Uses Message Queues for Reliability**.  
  - **Retry Mechanism to Prevent Failures**.  
  - **Security via API Authentication**.  
  - **Event Tracking for Analytics**.  
  - **User Preferences & Rate Limits for a Better UX**.  

✅ **A well-designed notification system is scalable, reliable, and user-friendly.**  

---
