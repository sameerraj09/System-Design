# **System Design Interview – Web Crawler Notes**  

## **Introduction**  
- A **web crawler** (robot/spider) is used to collect web content like web pages, images, videos, etc.  
- **Uses of web crawlers**:  
  - **Search engine indexing** (Googlebot, Bingbot)  
  - **Web archiving** (US Library of Congress, EU Web Archive)  
  - **Web mining** (Financial firms extracting data from reports)  
  - **Web monitoring** (Checking copyright violations, e.g., Digimarc)  

![image](https://github.com/user-attachments/assets/635b8b37-1cc4-4346-9941-d56138657b15)

---

## **Step 1: Understand the Problem and Design Scope**  
- **Basic Algorithm**:  
  1. Download web pages from a given URL list.  
  2. Extract new URLs from these pages.  
  3. Add new URLs to the download queue and repeat.  
- **Clarifying Questions to Ask**:  
  - **Purpose?** (Search engine indexing, data mining, etc.)  
  - **Pages per month?** (Example: 1 billion pages)  
  - **Content types?** (Example: Only HTML)  
  - **Track updates?** (Yes, newly added/edited pages)  
  - **Store pages?** (Yes, up to 5 years)  
  - **Handle duplicates?** (Ignore duplicate pages)  

- **Key Characteristics of a Good Web Crawler**:  
  - **Scalability** – Must handle billions of pages efficiently.  
  - **Robustness** – Must handle bad HTML, slow servers, errors.  
  - **Politeness** – Should not overload a website with too many requests.  
  - **Extensibility** – Should allow adding new content types (images, PDFs).  

- **Back-of-the-Envelope Estimation**:  
  - **1 billion pages per month**  
  - **400 pages per second**, with peak at **800 pages per second**  
  - **Average page size: 500 KB**  
  - **Storage required**:  
    - **500 TB per month**  
    - **30 PB for 5 years of storage**  

---

## **Step 2: High-Level Design**  
- **Key Components of a Web Crawler (Figure 9-2)**:  
  - **Seed URLs** – Starting points for crawling (e.g., university homepage).  
  - **URL Frontier** – Stores URLs to be processed (FIFO queue).  
  - **HTML Downloader** – Fetches web pages.  
  - **DNS Resolver** – Converts domain names to IP addresses.  
  - **Content Parser** – Checks and validates HTML pages.  
  - **Content Seen?** – Prevents duplicate content storage (uses hashing).  
  - **Content Storage** – Stores HTML content.  
  - **URL Extractor** – Extracts links from pages.  
  - **URL Filter** – Filters out unwanted URLs (e.g., errors, blacklisted sites).  
  - **URL Seen?** – Avoids processing the same URL multiple times.  
  - **URL Storage** – Keeps track of visited URLs.  

![image](https://github.com/user-attachments/assets/7a49b7e4-93b3-4f29-936b-8d10c7347684)

- **Web Crawler Workflow (Figure 9-4)**:  
  1. **Add Seed URLs** to URL Frontier.  
  2. **HTML Downloader** fetches URLs.  
  3. **DNS Resolver** converts URLs to IP addresses.  
  4. **Content Parser** validates pages.  
  5. **Content Seen?** checks for duplicate pages.  
  6. **URL Extractor** extracts links.  
  7. **URL Filter** removes unnecessary links.  
  8. **URL Seen?** checks if a URL was visited before.  
  9. **New URLs** are added to URL Frontier.  


![image](https://github.com/user-attachments/assets/22d96d15-5b91-490d-9642-336345b1beed)

---

## **Step 3: Design Deep Dive**  
### **Graph Traversal: DFS vs BFS**  
- The web is a **graph** with pages as **nodes** and links as **edges**.  
- **BFS (Breadth-First Search)** is preferred over DFS because:  
  - DFS goes too deep, wasting resources.  
  - BFS allows downloading in a structured way (FIFO queue).  


![image](https://github.com/user-attachments/assets/cc3d4f26-d154-412f-8011-23124eea3136)


### **URL Frontier (Figure 9-5, Figure 9-6, Figure 9-7, Figure 9-8)**  
- Manages **politeness, priority, and freshness**.  
- **Politeness**:  
  - Avoid too many requests to the same site (prevents overload or bans).  
  - Uses **per-host FIFO queues**.  


![image](https://github.com/user-attachments/assets/f5b1055d-cd5c-4f9a-9c8c-76d4a0fbd9bc)
![image](https://github.com/user-attachments/assets/bc6391d6-5276-4ab1-a545-e39fcfbf52d4)

- **Priority**:  
  - Some pages are more important (e.g., Apple homepage vs. random forum post).  
  - Prioritizes URLs based on **PageRank, traffic, update frequency**.
 
![image](https://github.com/user-attachments/assets/b8227b3f-19c1-451a-9293-e69f5e7a2342)
![image](https://github.com/user-attachments/assets/c1f9f11b-4500-4e97-a773-16d39a1ee4fc)


- **Freshness**:  
  - Pages change over time.  
  - Recrawling strategy based on update history.  
  - Important pages updated more frequently.  

- **Storage Approach**:  
  - Hybrid **memory + disk storage**.  
  - Memory used for fast access.  
  - Disk stores large amounts of data.  

---

## **HTML Downloader**  
### **Robots.txt Handling**  
- Websites use **robots.txt** to control crawler access.  
- Example (Amazon robots.txt):  
  ```
  User-agent: Googlebot  
  Disallow: /creatorhub/*  
  ```  
- Robots.txt results should be cached to avoid repeated downloads.  

### **Performance Optimization (Figure 9-9)**  
1. **Distributed Crawling** – Multiple servers handle different URL sets.  
2. **DNS Resolver Cache** – Reduces slow DNS lookup times.  
3. **Locality Optimization** – Place crawlers closer to target websites.  
4. **Timeouts** – Avoid long waits for unresponsive pages.  

---

## **Robustness & Extensibility**  
- **Robustness Techniques**:  
  - **Consistent hashing** – Distributes load among servers.  
  - **Save crawl states** – Allows recovery from failures.  
  - **Exception handling** – Prevent crashes due to errors.  
  - **Data validation** – Prevents storage of bad data.  

- **Extensibility (Figure 9-10)**:  
  - Easily add new modules (e.g., **PNG Downloader**, **Web Monitor**).
  - 
![image](https://github.com/user-attachments/assets/d7542e38-bd3f-4cca-9549-47320e0eeb92)

---

## **Detect and Avoid Problematic Content**  
### **1. Redundant Content**  
- **30% of pages are duplicates**.  
- Use **hashing** to detect and remove duplicates.  

### **2. Spider Traps**  
- Some websites create infinite links to trap crawlers.  
- Solution: **Set a max URL length or manually block such sites**.  

### **3. Data Noise**  
- Avoid storing **ads, spam URLs, irrelevant content**.  

---

## **Step 4: Wrap Up**  
### **Key Takeaways**  
- **Good web crawlers** must be **scalable, polite, extensible, and robust**.  
- **Main components**: URL Frontier, HTML Downloader, Content Parser, Storage.  
- **Key techniques**:  
  - BFS-based crawling for efficiency.  
  - Politeness enforcement to avoid overloading websites.  
  - URL prioritization for important pages.  
  - Freshness strategies for keeping data updated.  

### **Additional Considerations**  
- **Handling JavaScript/AJAX-generated content**.  
- **Filtering spam pages**.  
- **Database replication and sharding** for scaling.  
- **Keeping crawlers stateless** for large-scale deployments.  
- **Analyzing crawl data** for improvements.  

---

### **Conclusion**  
Building a **scalable web crawler** is challenging because of the **vast web size and various traps**. This design ensures efficiency, politeness, and scalability while handling complex scenarios like priority, storage, and freshness.  
