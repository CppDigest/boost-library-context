# #5 Bugfix/trac10510 [Merged]

> Username: VemundH  
> Created at: 2014-09-30 20:24:50 UTC  
> Updated at: 2015-04-02 02:29:46 UTC  
> Merged at: 2015-04-02 02:29:46 UTC  
> Closed at: 2015-04-02 02:29:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/5  

See https://svn.boost.org/trac/boost/ticket/10510  
  
This fixes a bug in the SIMD optimization of operator<.  
According to the documentation on https://software.intel.com/sites/landingpage/IntrinsicsGuide/ it is safe to assume that  `_mm_movemask_epi8` will set the 16 most significant bits of the return value to 0: `dst[MAX:16] := 0`  
  
uint32_t is required in the final compare to handle `0x0000FFFF < 0xFFFFFFFF` (the results of the `(x - 1) ^ x` trick)

---
