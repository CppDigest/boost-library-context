# #20 - [peer-review]  Improve RNG Choice and Document Risks of Naive Generators [Closed]

> Username: Becheler  
> Created at: 2025-05-31 18:12:50 UTC  
> Updated at: 2025-06-24 13:28:13 UTC  
> Closed at: 2025-06-24 13:28:13 UTC  
> Url: https://github.com/boostorg/bloom/issues/20  

## Description  
  
Kostas Savvidis raised concerns that the library’s hand-rolled RNG (`mcg_and_fastrange`) may exhibit poor cross-correlation properties—potentially undermining filter performance. Below are key excerpts and proposed actions.  
  
> *Disclaimer:* My personal understanding of RNG theory is still developing, so this issue may contain mistakes. Please feel free to correct any errors—this process also helps me get up to speed with the theory and, hopefully, simplifies your work. A.B.  
  
---  
  
## Relevant Quotes  
  
> **Kostas (May 2025):**    
> “The main technical innovation of the design of this library was the idea to do it essentially without hashing. … The library is choosing this RNG parametrically which is unworkable and specific details are such that the choice may turn out to be really bad according to well established theory.”    
  
> **Kostas (continued):**    
> “When I request fpr=0.01 or 1%, the number of false collisions is only 86k … this necessitates my recommendation: provide a second more accurate way to compute FPR … or use a better RNG (e.g., Knuth’s multiplier).”    
  
> **Joaquín (response):**    
> “The only reason for not adopting Boost.Random is speed: Boost.Bloom has been implemented with the aim of being as fast as possible, and any non-trivial mixer will have a measurable impact on performance. I’d like to understand (and hopefully reproduce) conditions under which a poor MCG may ruin the filter’s efficiency.”    
  
> **Peter (reply to Kostas):**    
> “I am actually not sure which is more important for a Bloom filter, autocorrelation or cross-correlation. … Using an LCG `(a*x + b)` instead of an MCG `(a*x)` could fix that.”    
  
---  
  
## Context  
  
### Current Code  
  
In Boost.Bloom, the hash strategy is hard-coded as `mcg_and_fastrange` in `boost/bloom/detail/core.hpp` :  
  
The `mcg_and_fastrange` struct implements a very lightweight “multiplicative congruential generator + fast-range” engine, which is used by Boost.Bloom to   
1. advance a simple 64-bit RNG  
2. map its output uniformly into a desired range without a slow division.   
  
The code lacks documentation but this is my understanding:  
```cpp  
struct mcg_and_fastrange  
{  
  // Constructor: choose an odd multiplier 'rng' based on the desired range 'm'  
  constexpr mcg_and_fastrange(std::size_t m) noexcept:  
    rng{  
      m + (  
        (m % 8 <= 3)? 3 - (m % 8) :  
        (m % 8 <= 5)? 5 - (m % 8) :  
                      8 - (m % 8) + 3  
      )  
    }  
  {}  
  
  // expose the current 'rng' value as the modulus (ie size of the Bloom-filter bit array or bucket count).  
  inline constexpr std::size_t range() const noexcept  
  {  
    return static_cast<std::size_t>(rng);  
  }  
  
  // Force 'hash' to be odd so the MCG can cycle through all 2^64 states (avoid even-only subcycle).  
  inline void prepare_hash(boost::uint64_t& hash) const noexcept  
  {  
    hash |= 1u;  // Force the least significant bit to 1  
  }  
  
  // advance the MCG (hash *= rng) via umul128, then return the high 64 bits (hi) as the index in [0, rng), achieving fast range mapping without division.  
  inline std::size_t next_position(boost::uint64_t& hash) const noexcept  
  {  
    boost::uint64_t hi;  
    // Advance hash: hash ← (hash * rng) mod 2^64, and capture the high 64 bits in 'hi'  
    hash = umul128(hash, rng, hi);  
    // 'hi' is now roughly (original_hash * rng) >> 64. That’s our index ∈ [0, rng).  
    return static_cast<std::size_t>(hi);  
  }  
  
private:  
  // Internal 64-bit multiplier. Must be odd and chosen so that rng % 8 ∈ {3, 5}, ensuring a full-period multiplicative cycle mod 2^64.  
  boost::uint64_t rng;  
};  
```  
  
This is in turn injected in the `filter_core` class:  
```cpp  
template<std::size_t K,typename Subfilter,std::size_t BucketSize,typename Allocator>  
class filter_core:empty_value<Allocator,0>  
{  
    using hash_strategy=detail::mcg_and_fastrange;  
   // ...  
}  
```  
  
### Why this `mcg_and_fastrange`  
  
#### Speed  
  
- **MCG Step:** A multiplicative congruential generator (MCG) requires only one 64-bit multiply per iteration—extremely fast on modern CPUs.  
- **FastRange Trick:** Instead of using a costly division (`raw % m`), it computes `index = (raw * m) >> 64` via a 128×64→64 multiply + shift. On x86_64, this is typically a single-cycle (or very few cycles) operation with `mul`/`imul`.  
  
#### Determinism  
- An MCG sequence is entirely determined by the initial seed and the multiplier `a`. Reusing the same seed yields a reproducible bit-position sequence, ensuring the Bloom filter behaves deterministically when seeded identically.  
  
#### Simplicity  
- No external dependencies are required—both the MCG and FastRange functions are implemented inline in `core.hpp`.  
  
---  
  
### Drawbacks / Caveats  
  
#### Statistical Quality  
- **Cross-Correlation Risks:** Simple MCGs can exhibit poor cross-correlation between multiple hash streams (e.g., if `h_i = 2 × g_i` for all `i`, you get 100% correlation in certain buckets).    
- **Limited State Size:** A single MCG has only 64 bits of state. Hashing very large datasets or repeatedly reseeding poorly can lead to clustering or collision patterns that degrade filter performance.  
  
## Action Items Proposal (to be discussed)  
  
0. **Rewrite/Correct this issue to adequately document what on earth is going on  
  
1. **Document Current RNG Behavior & Risks**    
   - For now add a note in the “Implementation” or “Design” section explaining that the library uses an MCG (`mcg_and_fastrange` from `boost/bloom/detail/core.hp`) and the potential cross-correlation pitfalls (e.g., “if in the first bucket `h1 = 2*g1`, then `h_i = 2*g_i` for all `i`, causing 100% correlation”).    
   - Continue discussion with Kostas and Peter  
   - Reference/reproduce Kostas’s observations on pathological cases where MCG-based position selection can degrade false-positive performance.  
  
2. **Evaluate Alternative RNGs**    
   - Benchmark a simple LCG `(a*x + b)` variant (e.g., Knuth’s multiplier + constant) against the existing MCG for throughput and distribution quality.    
   - Optionally create a microbenchmark to measure insertion/lookup performance with MCG vs. LCG vs. a minimal Boost.Random generator.  
  
3. **Offer User-Configurable RNG Options**    
   - Expose a `hashing_strategy` policy or template parameter for the bit-position RNG (currently hard-coded to `mcg_and_fastrange`).  
   - Give it a sensical default  
   - Document how to plug in a custom generator (e.g., `boost::random::mixmax` or a lightweight LCG) and the trade-offs in speed versus statistical quality.  
  
4. **Provide Recommendations in Documentation**    
   - In the “Design” or “Appendix” sections, summarize:    
     - Why naive MCG can produce poor cross-correlation for bucket assignment.    
     - How an LCG `(a*x + b)` with a well-chosen `b` might mitigate those issues.    
     - Where to find robust, high-performance RNGs (e.g., Boost.Random’s MIXMAX or other Boost-provided generators).    
   - Include short code snippets showing how to instantiate a filter with a custom RNG functor.  
  
5. **Monitor for Pathological Cases**    
   - Encourage community contributors (e.g., RNG experts) to identify input sequences or parameter sets that expose MCG weaknesses.    
   - If such cases arise, update documentation and consider replacing the default RNG.  
  
---  
  
**By addressing these points, we ensure Boost.Bloom remains both fast and statistically sound—providing transparency around RNG choices and giving users clear guidance on safer alternatives.**

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-15 15:12:21 UTC  
> Url: https://github.com/boostorg/bloom/issues/20#issuecomment-2974043783  

[Adopted hash strategy with fastrange plus a separate MCG](https://github.com/joaquintides/bloom/commit/b28e526b9e2994d5470adb6ae5024811ff9f7e1d)
