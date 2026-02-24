# #22 - [peer-review] Cache-Line–Blocked Bloom Filter as Primary Use Case [Closed]

> Username: Becheler  
> Created at: 2025-05-31 18:39:26 UTC  
> Updated at: 2025-06-13 11:29:07 UTC  
> Closed at: 2025-06-13 11:29:07 UTC  
> Url: https://github.com/boostorg/bloom/issues/22  

## Description  
  
Tomer Vromen noted that using a block size equal to a CPU cache line (e.g., 512 bits on x86-64) can significantly improve performance, but this option isn’t clearly documented. Below is a concise explanation followed by Tomer’s key comments and suggested improvements.  
  
Tomer Vromen (May 22):  
> “I wanted to use a Blocked BF with blocks of 1 cache line. It was not obvious from the docs that this is possible, because the docs say that the underlying type for the Block must be an unsigned integral type. I tried the following which seemed to work:    
>    
> ```cpp  
> using CacheLine = std::bitset<512>;  
> using filter = boost::bloom::filter<T, 1, boost::bloom::block<CacheLine, K>>;  
> ```    
>    
> I would like to see this officially supported and documented as a main use-case.”  
  
> “To use multiblock I needed to include a dedicated header, which is not needed for block. This is a bit confusing.”  
  
---  
  
### Why Cache-Line Blocking Matters (Cache-Beginner-Friendly)  
  
- A Bloom filter uses a bit array to track set membership. Traditional implementations may scatter bit checks across memory, causing multiple cache misses for each insertion or lookup.  
- A **Blocked Bloom Filter** divides the bit array into fixed-size “blocks.” Each block is a small sub-array in memory.  
- **Cache-Line Block:** If each block is exactly one CPU cache line (e.g., 512 bits or 64 bytes on many x86-64 CPUs), then every insertion or lookup touches only a single cache line. This minimizes cache misses and yields much faster throughput when filtering millions of items.  
  
## Action Items  
  
1. **Add a “Cache-Line Blocked” Example**    
   - In the **Filter Definition** or **Usage** section, insert a full, working code snippet showing how to declare a Bloom filter with a 512-bit (64-byte) cache-line block:    
   - Explain in simple terms that “each block is one cache line, so insertions/lookups only touch a single 64-byte chunk of memory, minimizing cache misses.”  
  
2. **Clarify Block vs. Multiblock Headers**    
   - Note that to use a single “cache-line–sized” block, only the `<boost/bloom/block.hpp>` header is needed.    
   - If using “multiblock” (multiple cache-line blocks per filter), explain why the separate header   
 (`<boost/bloom/multiblock.hpp>`) is required and when to choose it.  
  
3. **Update Documentation Text**    
   - In the “Filter Definition” section, replace the vague statement “Block must be an unsigned integral type” with a clear description:    
     > “**Block** can be any unsigned-integral–like type (e.g., `uint32_t`, `uint64_t`, or `std::bitset<512>`). To achieve optimal performance, you may choose a block that exactly matches your CPU’s cache line size—e.g., `std::bitset<512>` on x86-64.”  
  
4. **Add Performance Note**    
   - Briefly mention that “Using a cache-line–sized block typically yields the best performance on modern CPUs. If unsure, start with `std::bitset<512>` for x86-64 or the equivalent size on other architectures.”  
  
---  
  
By documenting and highlighting “cache-line–blocked” Bloom filters as a primary use case, new users will immediately see how to achieve optimal speed without having to deduce it from sparse examples.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:29:32 UTC  
> Url: https://github.com/boostorg/bloom/issues/22#issuecomment-2969530099  

[Added support for arrays as blocks](https://github.com/joaquintides/bloom/commit/f09eaeb0ab9d1d372389604e9ad8a4ee0e955ec3)

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 11:29:07 UTC  
> Url: https://github.com/boostorg/bloom/issues/22#issuecomment-2970095098  

Amazing :)  
Thank you @joaquintides !  
I will now close this issue :)
