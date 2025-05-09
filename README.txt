# LookAsideCache

A simple C++ implementation of an in-memory key-value cache with configurable eviction policies (FIFO and LRU). This mimics behavior of systems like memcached or Redis, with a basic testing suite.

## Features

- Set key-value pairs
- Get keys (with optional update to eviction priority)
- Automatic eviction when capacity is exceeded
- Supports two eviction policies:
  - FIFO (First-In-First-Out)
  - LRU (Least Recently Used)

## File Structure

- `cache_lib.cc`: Core cache logic
- `fifo_evictor.cc/hh`: FIFO eviction strategy
- `lru_evictor.cc/hh`: LRU eviction strategy
- `test_cache_lib.cc`: Basic test driver

### Prerequisites
- A C++ compiler that supports C++17 (e.g., `g++`)
- Terminal or command-line access

### Build

```bash
g++ -std=c++17 cache_lib.cc test_cache_lib.cc fifo_evictor.cc lru_evictor.cc -o test_cache.exe

### Run 
.\test_cache.exe 

(these instructions are for windows so may need to be modified for another environment) 

### Sample Output 

testKey1 9
testKey1 testKey1 9
testKey2 testKey1 4
testKey3 testKey4 testKey5 6

each line corresponds to the internal behavior of the cashe (inserts, evictions, replacements), as tested by test_cashe_lib.cc

### Extra Detail 

In this project I implemented a basic cache. In cache_lib.cc I implemented functions to delete, set, get, return space used, return hit rate, and reset the cache. 

I used a standard library unordered map as a hash table, which made hashing much easier. I just passed hasher to the unordered map when I initialized it. The unordered map I used implements collision chaining so each hashvalue has a bucket, and when a collision happens it just puts everything with the same hashvalue in the same bucket. This unordered map also has a built in method for resizing called unordered_map::max_load_factor. So I just passed max_load_factor to that and it would dynamically resize the cache. 

I also implemented an LRU (least recently used) eviction policy and a FIFO (first in first out) eviction policy. Both work similarly by using vectors to store the keys that have been added. The LRU version will search and move a key to the back of the queue if it has already been inserted, otherwise it will insert it at the end of the queue. There is more information about the implementation of the evictors in the code comments. 

I ran Valgrind and it gave no memory leaks once I deleted all the values stored on the heap at the appropriate places. 

Most of the time the runtime on the unordered_map will be O(1), constant time, if there are lots of collisions it could approach O(n). But overall it offers very good performance. 
