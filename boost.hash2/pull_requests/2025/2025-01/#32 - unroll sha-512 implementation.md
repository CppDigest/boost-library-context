# #32 unroll sha-512 implementation [Merged]

> Username: cmazakas  
> Created at: 2025-01-08 22:46:27 UTC  
> Updated at: 2025-01-09 15:31:27 UTC  
> Merged at: 2025-01-09 01:01:44 UTC  
> Closed at: 2025-01-09 01:01:44 UTC  
> Url: https://github.com/boostorg/hash2/pull/32  

On my machine using `clang++-19 -O3 -DNDEBUG -std=c++20`:  
```  
exbigboss@pleiades ~/cpp/boost-root/libs/hash2 (develop)  
❯ sudo nice -n -20 ./_build/benchmark/buffer  
boost::hash2::sha2_512 (N=65536): 2444405123: 9996 ms, 409.76 MB/s  
boost::hash2::sha2_384 (N=65536): 2750146615: 10317 ms, 397.01 MB/s  
boost::hash2::sha2_512_224 (N=65536): 855476869: 10197 ms, 401.68 MB/s  
boost::hash2::sha2_512_256 (N=65536): 2791159815: 9968 ms, 410.91 MB/s  
--  
boost::hash2::sha2_512 (N=15): 2444405123: 11209 ms, 365.42 MB/s  
boost::hash2::sha2_384 (N=15): 2750146615: 11128 ms, 368.07 MB/s  
boost::hash2::sha2_512_224 (N=15): 855476869: 11126 ms, 368.14 MB/s  
boost::hash2::sha2_512_256 (N=15): 2791159815: 10966 ms, 373.51 MB/s  
--  
boost::hash2::sha2_512 (N=4): 2444405123: 12702 ms, 322.46 MB/s  
boost::hash2::sha2_384 (N=4): 2750146615: 12894 ms, 317.66 MB/s  
boost::hash2::sha2_512_224 (N=4): 855476869: 12599 ms, 325.10 MB/s  
boost::hash2::sha2_512_256 (N=4): 2791159815: 12899 ms, 317.54 MB/s  
--  
  
exbigboss@pleiades ~/cpp/boost-root/libs/hash2 (feature/sha-512-perf)  
❯ sudo nice -n -20 ./_build/benchmark/buffer  
boost::hash2::sha2_512 (N=65536): 2444405123: 8552 ms, 478.94 MB/s  
boost::hash2::sha2_384 (N=65536): 2750146615: 8547 ms, 479.23 MB/s  
boost::hash2::sha2_512_224 (N=65536): 855476869: 8564 ms, 478.27 MB/s  
boost::hash2::sha2_512_256 (N=65536): 2791159815: 8550 ms, 479.06 MB/s  
--  
boost::hash2::sha2_512 (N=15): 2444405123: 9241 ms, 443.24 MB/s  
boost::hash2::sha2_384 (N=15): 2750146615: 9223 ms, 444.10 MB/s  
boost::hash2::sha2_512_224 (N=15): 855476869: 9220 ms, 444.24 MB/s  
boost::hash2::sha2_512_256 (N=15): 2791159815: 9224 ms, 444.05 MB/s  
--  
boost::hash2::sha2_512 (N=4): 2444405123: 11025 ms, 371.51 MB/s  
boost::hash2::sha2_384 (N=4): 2750146615: 10995 ms, 372.53 MB/s  
boost::hash2::sha2_512_224 (N=4): 855476869: 11050 ms, 370.67 MB/s  
boost::hash2::sha2_512_256 (N=4): 2791159815: 10972 ms, 373.31 MB/s  
--  
```

---
