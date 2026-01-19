# Design a Web Crawler (Google / Bing)

A web crawler is a software program which browses the World Wide Web in a methodical and automated manner.

## 1. Requirements
- **Scalability**: Handle billions of pages.
- **Politeness**: Don't overwhelm a single website with requests (respect `robots.txt`).
- **Extensibility**: Support new content types (Images, PDF, Video).
- **Robustness**: Handle bad HTML, infinite loops (spider traps), and malicious links.

## 2. Key Components
1. **Seed URLs**: The starting point of crawling.
2. **URL Frontier**: A queue of URLs to be crawled. It manages prioritization and politeness.
3. **HTML Downloader**: Fetches pages from the web.
4. **Content Parser**: Extracts links and text from the HTML.
5. **Content Seen?**: A deduplication service (using Hash/Checksum) to avoid processing the same content twice.
6. **URL Filter**: Blacklists certain sites or file types.
7. **URL Storage**: Stores the metadata of crawled URLs.

## 3. The Workflow
1. URLs are picked from the **URL Frontier**.
2. **Downloader** fetches the page.
3. **Parser** extracts URLs from the page.
4. New URLs pass through the **Filter**.
5. URLs not yet crawled are added back to the **Frontier**.

## 4. Advanced: URL Frontier & Politeness
To be polite, the Frontier should ensure that we don't hit the same server (e.g., `wikipedia.org`) too frequently.
- **Mapping**: Map hostname to a specific queue.
- **Queue Selector**: Only pick from a queue if it hasn't been hit in the last $X$ seconds.

## 5. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Data Richness**: Allows building search engines or data analysis tools. | **Huge Bandwidth**: Crawling the web consumes massive network resources. |
| **Automation**: Keeps data up-to-date without manual input. | **Legal/Ethical issues**: Must respect TOS and copyright laws. |
| | **Infinite Loops**: Hard to detect pages that dynamically generate "Next Page" links forever. |

## 6. Real-Time Use Case
**Google Search** uses Googlebot. **SEO tools** like Ahrefs or Semrush use crawlers to analyze site health and backlinks.

## 7. Implementation Concept (Bloom Filter for URL Deduplication)
Checking if a URL exists in a DB of billions of rows is slow. A **Bloom Filter** is a space-efficient probabilistic data structure that can tell you if a URL is "Definitely not in the set" or "Maybe in the set".
