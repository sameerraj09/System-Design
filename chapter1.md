In system design interviews, "back-of-the-envelope calculations" help you estimate system capacity and performance using rough calculations based on common metrics. These estimates guide you in choosing designs that can handle expected loads and meet performance goals. Here are key concepts to know for making these estimates:

1. **Power of Two**:
   - Understanding powers of two is useful because computer systems use binary (base 2). 
   - Common capacities (like memory or storage) often scale by powers of two: 2, 4, 8, 16, etc.
   - For example, if you need to double system capacity, you may plan to go from 16 to 32 servers, following the pattern of powers of two.

2. **Latency Numbers**:
   - Latency refers to the delay between a request and its response. Different system components have typical latency values.
   - Key latencies to remember:
     - **CPU cache**: Very fast (nanoseconds).
     - **RAM access**: Slightly slower, but still fast (tens of nanoseconds).
     - **Disk access**: Much slower (milliseconds).
     - **Network requests**: Can vary but are usually measured in milliseconds.
   - Knowing these typical latencies helps estimate response times and identify bottlenecks in a design.

3. **Availability Numbers**:
   - Availability represents how often a system is up and running without downtime, usually given in "nines" (e.g., 99.9% or "three nines").
   - Common availability targets:
     - **99.9% (three nines)**: About 8.76 hours of downtime per year.
     - **99.99% (four nines)**: About 52.56 minutes of downtime per year.
     - **99.999% (five nines)**: Only about 5.26 minutes of downtime per year.
   - High availability is often crucial in design. Knowing these numbers helps set realistic goals for uptime.

These concepts help you estimate system needs and ensure your design is capable of handling required loads, response times, and availability.
