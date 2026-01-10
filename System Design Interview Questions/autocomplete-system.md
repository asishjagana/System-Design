# Design an Autocomplete System (Search Suggestions)

How does **Google** suggest search queries as you type?

## 1. Requirements
- **Fast**: Suggestions must appear in < 20ms of typing.
- **Relevant**: Show most popular and trending terms.
- **Scalable**: Support millions of searches per second.

## 2. Key Data Structure: Trie (Prefix Tree)
A **Trie** is the most efficient data structure for prefix matching. Each node represents a character. To handle "popularity", each leaf node can store a `weight` or `frequency`.

## 3. High-Level Architecture
1. **Collector/Aggregator**: A service that monitors what people are searching for and updates the "frequency" count in a database.
2. **Trie Builder**: A background job that takes the data from the DB and rebuilds an optimized Trie every few hours.
3. **Query Service**:
    - **Trie Cache**: The entire trie (or top prefixes) is kept in memory (using Redis or local memory).
    - When user types "app", the system jumps to the "p" node under "a" and finds the top 5 children with the highest frequency (e.g., "apple", "app store", "apply").

## 4. Advanced Optimization: Pre-computation
Finding the top 10 suggestions for a prefix can be slow if we have to scan the whole subtree.
- **Solution**: At each node of the Trie, store the top 10 results for that prefix inside the node itself. This makes the search $O(L)$ where $L$ is length of the prefix, instead of $O(Subtree)$.

## 5. Real-Time Use Case: Google Search / Amazon
They don't just use a simple Trie. They use **Distributed Tries** and heavily cache results for popular prefixes at the **Edge/CDN**.

## 6. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Incredible Speed**: $O(L)$ prefix lookup is as fast as it gets. | **Memory Intensive**: Tries can consume massive RAM for large dictionaries. |
| **Personalization**: Can be modified to show user-specific history first. | **Update Latency**: If a topic suddenly trends (Breaking News), the Trie needs rebuilding. |
| | **Stemming issues**: Handling misspelled prefixes requires complex fuzzy matching. |

## 7. Concept Code (Simple Trie Node)
```csharp
class TrieNode {
    public Dictionary<char, TrieNode> Children = new();
    public bool IsEndOfWord;
    public int Frequency;
    // Optimization: Store top 5 words starting with this prefix
    public List<string> TopSuggestions = new(); 
}
```
