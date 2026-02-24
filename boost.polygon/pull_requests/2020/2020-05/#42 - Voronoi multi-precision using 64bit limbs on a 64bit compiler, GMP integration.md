# #42 Voronoi multi-precision using 64bit limbs on a 64bit compiler, GMP integration [Open]

> Username: bubnikv  
> Created at: 2020-05-25 16:12:11 UTC  
> Updated at: 2020-05-30 05:59:25 UTC  
> Url: https://github.com/boostorg/polygon/pull/42  

1) On a 64bit compiler, 64bit int is used as a base for the multi-precision int. This makes the Voronoi about 25% faster than the original code.  
2) If available, GMP hand crafted vectorized fixed point operators are used. This makes the Voronoi around 50% faster than the original code.  
  
A sqr() operator was introduced instead of x * x, as the sqr operator may be optimized by GMP better than mul.  
  
The unit tests seem to pass all right. I was not quite sure about voronoi_ctype_traits  
```  
#ifdef BOOST_VORONOI_64_T  
  // using uint64   
  typedef extended_int<33> big_int_type;  
#else  
  // using uint32  
  typedef extended_int<64> big_int_type;  
#endif   
```  
  
I am using 33 limbs on a 64bit compiler instead of 32. The extended_int multiplication, add and subtract operators drop the top most zero, which leaves space for one limb. With 32bit limbs, the top most 32bit limb is released, while with the same operands the top most 64bit limb would possibly not be zero, therefore it would not be released. Therefore I increased the number of 64bit limbs to 33. Maybe @asydorchuk will consider this one extra limb unnecessary?

---

## Comment 1

> Username: bubnikv  
> Created_at: 2020-05-30 05:59:25 UTC  
> Url: https://github.com/boostorg/polygon/pull/42#issuecomment-636282633  

It looks like my overly optimistic comments on performance increase were due to some bug that I have fixed now. The performance gains are there, but less pronounced, likely around 30% for GMP. That was measured on a single thread on a modern AMD chip with a lot of cache.  
  
Switching to uint64 for the limbs does not make sense on Visual Studio with the current implementation, where there the compiler does not support uint64 natively and boost multiprecision is used instead.  
  
On my old Intel Haswell laptop with 6GB cache and compiled with Visual Studio, the GMP gains are in the range of 23% when running Voronoi on a single thread, but only around 7% when running on all hardware threads as the Voronoi generator is is not quite cache friendly.

---
