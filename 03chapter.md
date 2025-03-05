**key points to keep in mind for a system design interview:**

1. **Focus on the Process**: The interviewer cares more about how you approach the design, not creating a flawless product in an hour.
2. **Collaborate and Communicate**: Show teamwork skills, ask clarifying questions, and handle feedback constructively.
3. **Avoid Over-Engineering**: Design with practicality; don’t add unnecessary complexity.
4. **Highlight Trade-offs**: Show awareness of trade-offs and prioritize essential features.
5. **Stay Open-Minded**: Be flexible and avoid stubbornness, as interviews test adaptability and problem-solving skills under ambiguity.

These points help demonstrate your design thinking, technical ability, and interpersonal skills.

Here’s a simple 4-step process for tackling a system design interview:

### Step 1 - **Understand the Problem and Establish Scope**
   - **Don’t Rush**: Avoid jumping to conclusions too quickly. Take your time to understand the problem.
   - **Ask Clarifying Questions**: Inquire about user requirements, usage scale, main features, and platform (e.g., mobile or web).
   - **Document Assumptions**: Write down any assumptions you make for future reference.

   **Example Questions**:
   - What are the core features?
   - What is the expected scale?
   - Are we designing for a mobile app, web app, or both?
   - How should the system handle large traffic volumes?
     
**Reverse chronology** is a method of arranging events or items in reverse order,
with the most recent or latest event displayed first. This approach is often used in news feeds, timelines, or blogs,
where the newest content appears at the top, and older content follows in descending order.
For example, in a social media feed, you might see the latest post first, followed by earlier posts as you scroll down.

### Step 2 - **Propose High-Level Design and Get Buy-In**
   - **Create a High-Level Outline**: Draw a rough diagram with core components (e.g., servers, databases, caches).
   - **Collaborate**: Treat the interviewer as a teammate, asking for feedback and discussing your approach.
   - **Scale Evaluation**: Do quick estimations to check if your design can handle the anticipated load.

   **Example**:
   - For a news feed, split the design into *feed publishing* (storing posts) and *feed building* (ordering posts in reverse chronological order).

![image](https://github.com/user-attachments/assets/ea2fa9de-ec8d-415b-a3ef-8bf5eef7ed06)
![image](https://github.com/user-attachments/assets/d8e4dd95-6061-4870-ae18-0da0d22d7cfd)

**Explanation of "Fanout"**

The fanout mechanism is a common technique in social media systems to ensure that posts from a user are distributed to the feeds of all their followers. In this architecture:

When a user creates a post, the Fanout Service adds this post to each follower's news feed.
This pre-population of followers' feeds (fanout) improves retrieval speed when followers access their feeds since the posts are already cached.


Here are the key points:

### Step 3 - Design Deep Dive
- Goals:
  - Ensure you and the interviewer have aligned on:
    - Overall goals and feature scope
    - A high-level blueprint for the design
    - Feedback on high-level design
    - Initial focus areas based on feedback
  - Identify and prioritize components in the architecture.
- Interviewer preferences:
  - Focus may vary; for senior candidates, discussion could emphasize performance characteristics like bottlenecks and resource estimations.
  - Some interviewers may want more detailed exploration of certain components.
- Example topics for different designs:
  - URL shortener: dive into the hash function design.
  - Chat system: reduce latency, support online/offline status.
- Tips:
  - Time management is crucial—avoid spending too long on minute details.
  - Demonstrate key abilities, focusing on design scalability rather than implementation minutiae.
- Example:
  - For a news feed system, focus on two main use cases:
    1. Feed publishing
    2. News feed retrieval
    
![image](https://github.com/user-attachments/assets/732d1544-15b6-4248-86d4-6b666a17287c)

1.User submits a post request.
2.Load Balancer distributes the request to a web server.
3.Web Server authenticates, rate-limits, and forwards to the Post Service and Fanout Service.
4.Post Service caches the post and stores it in the Post DB.
5.Fanout Service retrieves friend IDs and data, then sends fanout tasks to the Message Queue.
6.Fanout Workers process the queue, updating friends’ News Feed Cache.
7.Friends can now see the post instantly via the News Feed Cache.

![image](https://github.com/user-attachments/assets/b30dce2b-6149-4cf8-9dcb-c82e3514fd36)

- User sends a request through a web browser or mobile app.
- Request goes to **DNS** for domain resolution.
- **Load balancer** distributes the request to **web servers**.
- **Web servers** handle authentication and rate limiting.
- Request is passed to the **News Feed Service**.
- **Caches** (News Feed, User, Post) are checked for data to speed up retrieval.
- If data is not in the caches, **User DB** and **Post DB** are queried.
- Caches are updated with data fetched from the databases.
- Response is sent back to the user through the **web servers** and **load balancer**.
- **CDN** handles static content delivery for improved performance.
- 
### Step 4 - Wrap Up
- Possible follow-ups:
  - Identify bottlenecks and suggest improvements.
  - Recap your design, especially if multiple solutions were discussed.
  - Discuss error handling (e.g., server failure, network loss).
  - Address operational issues like monitoring metrics, error logs, and system rollouts.
  - Consider scaling the design for increased user capacity (e.g., from 1 million to 10 million users).
  - Suggest additional refinements if time allows.

### Dos and Don’ts
#### Dos:
- Ask for clarification and confirm assumptions.
- Understand problem requirements.
- Communicate your thought process with the interviewer.
- Suggest multiple approaches if feasible.
- After agreeing on the high-level design, dive into component details, starting with the most critical.
- Work collaboratively with the interviewer.

#### Don’ts:
- Don’t jump into a solution without clarifying requirements.
- Avoid excessive detail on a single component at the start.
- Don’t assume the interview is complete until the interviewer indicates so.
- Avoid thinking in silence; keep communicating.

### Time Allocation (for a 45-minute session)
- Step 1: Understand the problem and establish design scope - 3 to 10 minutes
- Step 2: Propose high-level design and get buy-in - 10 to 15 minutes
- Step 3: Design deep dive - 10 to 25 minutes
- Step 4: Wrap up - 3 to 5 minutes
