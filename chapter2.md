**Example: Estimate Twitter QPS and storage requirements**

 Assumptions:
 
 • 300 million monthly active users.
 
 • 50% of users use Twitter daily.
 
 • Users post 2 tweets per day on average.
 
 • 10% of tweets contain media.
 
 • Data is stored for 5 years.

 ** Estimations:**
  
 Query per second (QPS) estimate:
 
 • Daily active users (DAU) = 300 million * 50% = 150 million
 
 • Tweets QPS = 150 million * 2 tweets / 24 hour / 3600 seconds = ~3500
 
 • Peek QPS = 2 * QPS = ~7000
 
 We will only estimate media storage here.
 
 • Average tweet size:
 
 • tweet_id   64 bytes
 
 • text 
 
• media 

140 bytes

 1 MB
 
 • Media storage: 150 million * 2 * 10% * 1 MB = 30 TB per day
 
 • 5-year media storage: 30 TB * 365 * 5 = ~55 PB


**Tips**

Back-of-the-envelope estimation is about showing your process rather than exact answers. Here are some quick tips:

Use Approximation: Simplify numbers to make calculations easier (e.g., approximate “99987 / 9.1” as “100,000 / 10”).

Write Assumptions: Note your assumptions for clarity and reference.

Label Units: Always specify units (e.g., “5 MB” instead of “5”) to avoid confusion.

Practice Common Scenarios: Practice quick estimations for QPS, storage, cache size, server count, etc., as these are commonly asked.
