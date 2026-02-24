# #31 - Bad filebuf (fstream) performance compared to standard library [Closed]

> Username: EvanBalster  
> Created at: 2017-11-03 20:47:28 UTC  
> Updated at: 2022-06-13 09:18:11 UTC  
> Closed at: 2022-06-13 09:18:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/31  

Hello —  
  
I noticed a performance issue writing WAV files today, and was dismayed to discover that all I/O in `nowide` is implemented through the `overflow` and `underflow` functions.  Every byte is written and flushed individually, resulting in performance costs hundreds of times higher than the standard library for large block writes.

---

## Comment 1

> Username: EvanBalster  
> Created at: 2017-11-03 21:56:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/31#issuecomment-341837062  

Pull request:  https://github.com/artyom-beilis/nowide/pull/32

---

## Comment 2

> Username: Flamefire  
> Created at: 2019-12-04 08:09:37 UTC  
> Url: https://github.com/boostorg/nowide/issues/31#issuecomment-561525932  

> that all I/O in nowide is implemented through the overflow and underflow functions.  
  
This is how it is supposed to do and the stdlib does the same. Those functions are defined this way.  
  
>  performance costs hundreds of times higher than the standard library   
  
There were to many flushes (and some other non-standard-compliant things) that I fixed. I expect those changes to become part of this repo soon. In the meantime you can check out my fork. I used the benchmark created by @artyom-beilis to measure the new speed and it looks good. BTW: The current filebuf was already okish in performance as blocks of 512B (usually) were written.  
  
```  
Testing I/O performance stdio  
  write block size       32 275.862 MB/s  
  write block size       64 447.552 MB/s  
  write block size      128 474.074 MB/s  
  write block size      256 496.124 MB/s  
  write block size      512 421.053 MB/s  
  write block size     1024 500.000 MB/s  
  write block size     2048 477.612 MB/s  
  write block size     4096 507.937 MB/s  
  write block size     8192 512.000 MB/s  
   read block size       32 1015.873 MB/s  
   read block size       64 1254.902 MB/s  
   read block size      128 1828.571 MB/s  
   read block size      256 2000.000 MB/s  
   read block size      512 2285.714 MB/s  
   read block size     1024 2370.370 MB/s  
   read block size     2048 2370.370 MB/s  
   read block size     4096 2370.370 MB/s  
   read block size     8192 3200.000 MB/s  
Testing I/O performance std::fstream  
  write block size       32 426.667 MB/s  
  write block size       64 453.901 MB/s  
  write block size      128 512.000 MB/s  
  write block size      256 496.124 MB/s  
  write block size      512 503.937 MB/s  
  write block size     1024 147.806 MB/s  
  write block size     2048 280.702 MB/s  
  write block size     4096 496.124 MB/s  
  write block size     8192 492.308 MB/s  
   read block size       32 1049.180 MB/s  
   read block size       64 1333.333 MB/s  
   read block size      128 1828.571 MB/s  
   read block size      256 2370.370 MB/s  
   read block size      512 2560.000 MB/s  
   read block size     1024 2782.609 MB/s  
   read block size     2048 3200.000 MB/s  
   read block size     4096 2666.667 MB/s  
   read block size     8192 3047.619 MB/s  
Testing I/O performance nowide::fstream  
  write block size       32 460.432 MB/s  
  write block size       64 447.552 MB/s  
  write block size      128 477.612 MB/s  
  write block size      256 457.143 MB/s  
  write block size      512 488.550 MB/s  
  write block size     1024 444.444 MB/s  
  write block size     2048 474.074 MB/s  
  write block size     4096 484.848 MB/s  
  write block size     8192 457.143 MB/s  
   read block size       32 1185.185 MB/s  
   read block size       64 1777.778 MB/s  
   read block size      128 1882.353 MB/s  
   read block size      256 3368.421 MB/s  
   read block size      512 2560.000 MB/s  
   read block size     1024 2782.609 MB/s  
   read block size     2048 3764.706 MB/s  
   read block size     4096 2909.091 MB/s  
   read block size     8192 2782.609 MB/s  
  
```

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-03-27 17:33:01 UTC  
> Updated at: 2022-03-27 17:35:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/31#issuecomment-1079980683  

Reopening as I'm working on some changes to further improve the performance of writing larger chunks of data, e.g. when using `fstream::write` or `fstream::read` with a large size

---

## Comment 4

> Username: Flamefire  
> Created at: 2022-06-13 09:18:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/31#issuecomment-1153678784  

Closed by #160 which implements the bulk-IO API/optimization of `filebuf`. This brings performance similar to `std::filebuf`  
  
I did consider conditionally using the `std::filebuf` for standard libraries supporting a `wchar_t-open` as done by [Boost.Filesystem](https://github.com/boostorg/filesystem/blob/develop/include/boost/filesystem/fstream.hpp) but that brings in another option making Boost.Nowide use one of 3 implementations: real-std::filebuf, std::filebuf-with-convertion-open, custom-filebuf.  
This makes it harder to test (here and for users) as it is now not only Windows vs non-Windows but also stdlibs with wide-open vs others.     
Hence there is a "good enough" filebuf over C-I/O for Windows (only) and an option to always use that to help testing or enforce consistent behavior.
