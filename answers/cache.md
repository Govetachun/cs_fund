# Caching Simplified

Caching means **copying data** into a faster storage layer so that future requests for that data can be served more quickly.

## Why Cache?
- **Speed:** Accessing data from a CPU cache or browser cache is much faster than accessing it from main memory or over the network.
- **Reduced Load:** Fewer trips to the database, disk, or network.
- **Improved User Experience:** Faster page loads, more responsive applications.

## Cache Miss vs. Cache Hit
- **Cache Hit:** Data is found in the cache.
- **Cache Miss:** Data isn't in the cache (or is stale), so the system must fetch it from the original source.

## Common Cache Strategies

1. **Write-Around Cache**
   - Data is written directly to storage (disk) without going to cache.
   - Cache is filled only when data is read (often used for infrequent writes).

2. **Write-Through Cache**
   - Data is written to both cache and storage simultaneously.
   - Ensures consistency but may fill cache with rarely accessed data.

3. **Write-Back Cache**
   - Data is first written to cache, then periodically written back to storage.
   - Improves write performance but can risk data loss if cache fails before storage is updated.

## Cache Eviction Policies

- **FIFO (First In, First Out):** Evict the oldest data added to the cache.
- **LRU (Least Recently Used):** Evict the data that hasn’t been accessed for the longest time.
- **LFU (Least Frequently Used):** Evict the data with the fewest accesses.
