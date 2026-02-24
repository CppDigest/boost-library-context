# #32 - Benchmarks [Closed]

> Username: mborland  
> Created at: 2023-05-12 09:25:34 UTC  
> Updated at: 2023-08-15 17:22:37 UTC  
> Closed at: 2023-08-15 17:22:37 UTC  
> Url: https://github.com/boostorg/charconv/issues/32  

For a comparison table:  
- [x] Boost.Lexical Cast  
- [x] Boost.Spirit.Qi  
- [x] Boost.Spirit.Karma  
- [x] STL  
- [x] Additionally MSVC STL `from_chars` to show if it's worth the MSVC team pursuing Lemire's implementation.  (https://github.com/microsoft/STL/issues/1610)  
- [x] printf / strtod  
- [x] Google double-conversion `https://github.com/google/double-conversion`

---

## Comment 1

> Username: mborland  
> Created at: 2023-05-24 13:45:40 UTC  
> Url: https://github.com/boostorg/charconv/issues/32#issuecomment-1561183775  

With the fixes from #47 and #49 we can now successfully run all the benchmarks.  
  
```  
 274.4 ns | Boost.lexical_cast  float  
 439.7 ns | Boost.lexical_cast double  
  
 300.1 ns | Boost.spirit.karma  float  
 307.4 ns | Boost.spirit.karma double  
  
 175.1 ns | std::snprintf  float general  9  
 342.3 ns | std::snprintf double general 17  
  
  33.6 ns | std::to_chars  float general shortest  
  38.3 ns | std::to_chars double general shortest  
  
  33.6 ns | Boost.Charconv::to_chars  float general shortest  
  38.3 ns | Boost.Charconv::to_chars double general shortest  
----------  
  79.3 ns | std::strtof  float scientific  
 134.7 ns | std::strtod double scientific  
  
  24.0 ns | std::from_chars  float scientific  
  23.5 ns | std::from_chars double scientific  
  
 105.3 ns | Boost.Charconv from_chars  float scientific  
2064.1 ns | Boost.Charconv from_chars double scientific  
  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2023-05-24 14:10:49 UTC  
> Url: https://github.com/boostorg/charconv/issues/32#issuecomment-1561236776  

Ouch. Why is our from_chars so much slower?

---

## Comment 3

> Username: mborland  
> Created at: 2023-05-24 14:13:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/32#issuecomment-1561241945  

> Ouch. Why is our from_chars so much slower?  
  
That is what I am trying to figure out. Even if we fell back to `std::strtod` every single time the double performance shouldn't be nearly that bad.

---

## Comment 4

> Username: mborland  
> Created at: 2023-08-04 18:31:00 UTC  
> Url: https://github.com/boostorg/charconv/issues/32#issuecomment-1666023323  

Here is the most recent which hits all of the requests from the ML:  
  
```  
GCC 13 x64 + libstdc++  
 202.8 ns | Boost.lexical_cast float  
 365.1 ns | Boost.lexical_cast double  
  61.8 ns | Boost.spirit.karma float  
  64.4 ns | Boost.spirit.karma double  
 159.6 ns | std::sprintf float scientific 8  
 319.8 ns | std::sprintf double scientific 16  
  32.6 ns | std::to_chars float plain shortest  
  34.7 ns | std::to_chars double plain shortest  
  30.5 ns | Boost.Charconv::to_chars float plain shortest  
  34.4 ns | Boost.Charconv::to_chars double plain shortest  
----------  
  66.9 ns | std::strtof float scientific  
 124.0 ns | std::strtod double scientific  
  23.7 ns | std::from_chars float scientific  
  22.0 ns | std::from_chars double scientific  
  20.9 ns | Boost.Charconv::from_chars float scientific  
  21.1 ns | Boost.Charconv::from_chars double scientific  
  26.5 ns | Boost.Spirit.Qi float scientific  
  37.2 ns | Boost.Spirit.Qi double scientific  
  12.9 ns | std::from_chars uint64_t  
  14.0 ns | Boost.Charconv::from_chars uint64_t  
```
