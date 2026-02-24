# #112 - multiprecision size incorrect on x86_64 [Closed]

> Username: carlhua  
> Created at: 2019-02-08 16:26:51 UTC  
> Updated at: 2019-03-04 18:48:01 UTC  
> Closed at: 2019-03-04 18:48:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/112  

Per boost multiprecision documentation:  
  
When used at fixed precision, the size of this type is always one machine word larger than you would expect for an N-bit integer: the extra word stores both the sign, and how many machine words in the integer are actually in use. The latter is an optimisation for larger fixed precision integers, so that a 1024-bit integer has almost the same performance characteristics as a 128-bit integer, rather than being 4 times slower for addition and 16 times slower for multiplication (assuming the values involved would always fit in 128 bits). Typically this means you can use an integer type wide enough for the "worst case scenario" with only minor performance degradation even if most of the time the arithmetic could in fact be done with a narrower type.  
  
  
I noticed for the following type:  
// Fixed precision unsigned types:  
typedef number<cpp_int_backend<128, 128, unsigned_magnitude, unchecked, void> >   uint128_t;  
typedef number<cpp_int_backend<256, 256, unsigned_magnitude, unchecked, void> >   uint256_t;  
  
they do not follow the documentation:  
uint128_t is exactly 16 bytes  
uint256_t is exactly 48 bytes.  
  
Per the documentation, on a 64 bit system, uint128_t should be 24 bytes (16 + 1 word (8)) and uint256_t should be 40 (32 + 1 word (8)). Please advise.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-08 18:21:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/112#issuecomment-461897291  

I need to update the docs:  
  
* When there is a native integer type large enough to hold all the bits of the cpp_int, then cpp_int becomes a thin wrapper around it.  So `uint128_t` is a thin wrapper around `__int128` on GCC, and the same size as that type.  
* Otherwise the comments in the docs apply, except that: _the compiler may add padding bytes_.  Looking at the results for gcc/mingw I see the types are all 12 bytes larger than expected (note `sizeof(int) == 4`), and the types are all 16-byte aligned.  So for 8 byte ints and 16 byte alignment, the type will indeed be 8 bytes larger than the sum of their parts.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-03-04 18:48:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/112#issuecomment-469373084  

This will be fixed for the next release.
