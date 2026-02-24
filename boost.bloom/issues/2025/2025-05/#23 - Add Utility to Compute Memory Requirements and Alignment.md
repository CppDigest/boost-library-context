# #23 - [peer-review] Add Utility to Compute Memory Requirements and Alignment [Closed]

> Username: Becheler  
> Created at: 2025-05-31 19:02:54 UTC  
> Updated at: 2025-06-13 11:27:24 UTC  
> Closed at: 2025-06-13 11:27:24 UTC  
> Url: https://github.com/boostorg/bloom/issues/23  

## Description  
  
Users need a way to know exactly how many bytes (including padding) and what alignment is required to embed a Bloom filter into a custom buffer. Without this, buffers may be misaligned or over-allocated.  
  
> “There would need to be a free function which takes the configuration as input and returns the smallest number of bytes needed to fit the filter under all alignment cases.”    
> — Vinnie Falco (Slack, May 25)  
  
> “If you can 'carefully offset what the allocator returns', you can also 'carefully offset the buffer user passed in'.”    
> — Janko (Slack, May 25)  
  
---  
  
## Background (Beginner-Friendly)  
  
A Bloom filter stores data in a compact bit array, using multiple hash functions to set and check bits. In many high-performance scenarios, users want to place this bit array inside a pre-allocated buffer of their own (for example, inside a larger struct, a shared memory region, or a specific memory pool). This is called **in-place construction**.    
  
However, simply allocating a “big enough” buffer isn’t enough. Modern CPUs read memory in **cache-line chunks** (often 64 bytes on x86-64). If your Bloom filter’s bit array isn’t aligned to a cache line, each lookup or insertion can cross cache lines, causing slowdowns. In addition, the filter’s internal metadata (such as counters or seeds) may need alignment to specific boundaries as well.  
  
Without knowing:  
  
1. **Total Size** (bit array + any padding needed for alignment)  
2. **Alignment Requirement** (e.g., 64-byte boundary)  
  
…users may:  
- **Under-allocate** (buffer too small, leading to out-of-bounds writes).  
- **Misalign** the buffer (leading to inefficient memory accesses).  
- **Over-allocate** too much (wasting space).  
  
By providing simple helper functions that answer:  
- “How many bytes do I need to allocate for a filter with N bits and K hash functions?”    
- “What alignment do those bytes need?”  
  
…we make it easy for newcomers to allocate the **exact** amount of memory and place their Bloom filter **correctly aligned**, avoiding performance pitfalls and memory errors.    
  
---  
  
## Discussion Points  
  
- **Considering a `bloom::required_bytes(bit_capacity, num_hashes)` Function**    
  Should we provide a helper that returns the total bytes needed (bit array plus any alignment padding) for a given configuration?  
  
- **Considering a `bloom::alignment(bit_capacity, num_hashes)` Function**    
  Would it be helpful to expose the required alignment (e.g., 64 bytes) so users know how to align their buffers?  
  
- **In-Place Construction Approach**    
  If an overload like `bloom_filter(void* buffer, std::size_t buffer_size)` is provided, how should we validate that `buffer_size` and `buffer` alignment meet the requirements, and how should any necessary offsetting be handled?  
  
---   
  
## Documentation Example  
  
How can we show a concise example demonstrating the following, so that readers understand how to allocate and align their buffers correctly?  
  
```cpp  
auto bytes = bloom_required_bytes(1'000'000, 7);  
alignas(bloom_alignment(1'000'000, 7))   
char buffer[bytes];  
  
bloom_filter f(buffer, bytes);  
```  
  
Please review and discuss whether these utilities and examples would improve usability, and if so, how best to integrate them into the API and documentation.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 11:25:09 UTC  
> Url: https://github.com/boostorg/bloom/issues/23#issuecomment-2970073746  

Currently `boost::bloom::filter` allocates its array internally and won't accept an external buffer. We could add a `boost::bloom::filter_view` that does so, but I have resisted to include this in the roadmap in wait for a more clear feedback from the community.

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 11:27:24 UTC  
> Url: https://github.com/boostorg/bloom/issues/23#issuecomment-2970086871  

Thank you @joaquintides ! Let's wait for more feedback then :)  
In the meantime I will close this issue.
