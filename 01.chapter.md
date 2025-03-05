In system design interviews, "back-of-the-envelope calculations" help you estimate system capacity and performance using rough calculations based on common metrics. These estimates guide you in choosing designs that can handle expected loads and meet performance goals. Here are key concepts to know for making these estimates:

1. **Power of Two**:
   - Understanding powers of two is useful because computer systems use binary (base 2). 
   - Common capacities (like memory or storage) often scale by powers of two: 2, 4, 8, 16, etc.
   - For example, if you need to double system capacity, you may plan to go from 16 to 32 servers, following the pattern of powers of two.
![image](https://github.com/user-attachments/assets/70d2d9eb-5b65-4b94-8b26-92e8b8c6c819)

2. **Latency Numbers**:
   - Latency refers to the delay between a request and its response. Different system components have typical latency values.
   - Key latencies to remember:
     - **CPU cache**: Very fast (nanoseconds).
     - **RAM access**: Slightly slower, but still fast (tens of nanoseconds).
     - **Disk access**: Much slower (milliseconds).
     - **Network requests**: Can vary but are usually measured in milliseconds.
   - Knowing these typical latencies helps estimate response times and identify bottlenecks in a design.
![image](https://github.com/user-attachments/assets/7a444d6e-acf3-4d20-8433-07e2ac07d509)
![image](https://github.com/user-attachments/assets/9e48c1d6-72ac-44bc-80c4-fe7c57ddb3b1)

3. **Availability Numbers**:
   - Availability represents how often a system is up and running without downtime, usually given in "nines" (e.g., 99.9% or "three nines").
   - High availability is often crucial in design. Knowing these numbers helps set realistic goals for uptime.
![image](https://github.com/user-attachments/assets/5364f141-1dad-4188-a6c4-6098f083a2a9)

These concepts help you estimate system needs and ensure your design is capable of handling required loads, response times, and availability.

