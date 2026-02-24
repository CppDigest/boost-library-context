# #31 sha-256 perf [Merged]

> Username: cmazakas  
> Created at: 2024-12-30 18:28:34 UTC  
> Updated at: 2024-12-31 15:32:20 UTC  
> Merged at: 2024-12-31 01:10:13 UTC  
> Closed at: 2024-12-31 01:10:14 UTC  
> Url: https://github.com/boostorg/hash2/pull/31  

First pass attempt at speeding up the sha-256 implementation.  
  
On my machine, benchmark/buffer.cpp is:  
```  
boost::hash2::sha2_256 (N=65536): 174029441: 14975 ms, 273.52 MB/s  
--  
boost::hash2::sha2_256 (N=15): 174029441: 15397 ms, 266.02 MB/s  
--  
boost::hash2::sha2_256 (N=4): 174029441: 18116 ms, 226.10 MB/s  
```  
and with these changes:  
```  
boost::hash2::sha2_256 (N=65536): 174029441: 12777 ms, 320.57 MB/s  
--  
boost::hash2::sha2_256 (N=15): 174029441: 13570 ms, 301.84 MB/s  
--  
boost::hash2::sha2_256 (N=4): 174029441: 15982 ms, 256.28 MB/s  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-12-30 18:34:21 UTC  
> Url: https://github.com/boostorg/hash2/pull/31#issuecomment-2565799057  

+17% isn't bad at all.

---
