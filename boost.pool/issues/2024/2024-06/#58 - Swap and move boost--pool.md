# #58 - Swap and move boost::pool [Open]

> Username: mglisse  
> Created at: 2024-06-26 16:50:05 UTC  
> Updated at: 2024-06-26 16:50:05 UTC  
> Url: https://github.com/boostorg/pool/issues/58  

Hello,  
from a quick look (so I can easily be mistaken) at the implementation of `boost::pool`, it looks like allowing swap and move operations should not cause any major issue. Is that something that you would consider?  
  
Use case: we do not use a global pool (bad for threading), we have a few big objects (usually no more than a handful, or maybe 1-2 per thread) that each own a pool for their internal needs, and we would like to provide swap/move operations on those objects. Wrapping the pool in a unique_ptr is a possibility, but it seems like an unnecessary (small) waste.
