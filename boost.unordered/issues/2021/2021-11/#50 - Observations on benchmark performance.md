# #50 - Observations on benchmark performance [Closed]

> Username: pdimov  
> Created at: 2021-11-29 04:27:39 UTC  
> Updated at: 2022-01-19 17:37:16 UTC  
> Closed at: 2022-01-19 17:37:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/50  

The containers can use one of two policies: a "prime_policy" (https://github.com/boostorg/unordered/blob/5e8b6a9e5590fa8a0795c36ee5cd0a90dd7df5cb/include/boost/unordered/detail/implementation.hpp#L2605), which uses prime numbers for the bucket count (https://github.com/boostorg/unordered/blob/5e8b6a9e5590fa8a0795c36ee5cd0a90dd7df5cb/include/boost/unordered/detail/implementation.hpp#L287-L294), or a "mix64_policy", which uses power of two sizes, but scrambles the hash value to avoid pathological cases when the hash values don't vary in the lower bits.  
  
Generally, the mix64_policy is selected when size_t is 64 bit, and prime_policy is used otherwise (https://github.com/boostorg/unordered/blob/5e8b6a9e5590fa8a0795c36ee5cd0a90dd7df5cb/include/boost/unordered/detail/implementation.hpp#L2681-L2696).  
  
However, when the key is integral (`int` and above), prime_policy is selected as a special case (https://github.com/boostorg/unordered/blob/5e8b6a9e5590fa8a0795c36ee5cd0a90dd7df5cb/include/boost/unordered/detail/implementation.hpp#L2698-L2702).  
  
The comment claims that the prime policy is a lot faster for consecutive integral keys, but since it performs a modulo operation, which is an expensive div instruction, on `benchmark/uint64.cpp` mix64_policy is slightly faster for me (~23 seconds against ~24 seconds under msvc-14.2 64 bit release).  
  
Therefore, we should consider removing this special case, and use mix64_policy for integral keys, too.  
  
mix64_policy contains code that scrambles the hash value (https://github.com/boostorg/unordered/blob/5e8b6a9e5590fa8a0795c36ee5cd0a90dd7df5cb/include/boost/unordered/detail/implementation.hpp#L2640-L2646). This can be replaced with the simpler  
```  
// Multiplier taken from https://arxiv.org/pdf/2001.05304.pdf  
// Computationally Easy, Spectrally Good Multipliers for Congruential Pseudorandom Number Generators  
// Guy Steele, Sebastiano Vigna, September 28, 2021  
  
key *= 0xf1357aea2e62a9c5;  
key ^= key >> 32;  
```  
which for me is about 20% faster and should have good enough scrambling properties. So we should consider this alternative.  
  
We probably want a mix32_policy, too, for the 32 bit case.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-29 04:37:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/50#issuecomment-981286099  

An alternative way to scramble the hash value and obtain a bucket count is Knuth's multiplicative hashing, which consists of multiplying by phi*2^n, where n is the word size (32 or 64), and then taking the bucket index from the high bits, instead of the low bits. (The constant for the 32 bit case is 2654435769.) This is also called "Fibonacci hashing", e.g. [here](https://en.wikipedia.org/wiki/Hash_function#Fibonacci_hashing) and [here](https://probablydance.com/2018/06/16/fibonacci-hashing-the-optimization-that-the-world-forgot-or-a-better-alternative-to-integer-modulo/).)  
  
However, the policies at the moment only take `bucket_count` as a parameter in `to_bucket`, and for the above to be efficient we need its log2 so that we know how much to shift right. So using it required more changes than I was willing to make, and I didn't time it. It should be roughly as performant as the 0xf1357aea2e62a9c5 scrambler.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-01-19 17:37:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/50#issuecomment-1016705153  

Fibonacci hashing implemented on develop.
