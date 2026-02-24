# #26 - Speedup with SSE/AVX extension? [Open]

> Username: user202729  
> Created at: 2024-06-12 02:43:21 UTC  
> Updated at: 2024-06-12 02:43:32 UTC  
> Url: https://github.com/boostorg/crc/issues/26  

[Chromium's zlib fork has a very fast CRC32 implementation using SSE or AVX extension](https://chromium.googlesource.com/chromium/src/+/HEAD/third_party/zlib/crc32_simd.c), whichever available on the machine.  
  
**Would it be preferable to implement this feature here?**  
  
Note that the code there only work for CRC32 (with the fixed polynomial), so we need to either  
  
* generalize it, or  
* use template specialization to use it in the specific case of CRC32.
