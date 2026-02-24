# #318 - Implemented a memory-compressed storage [Open]

> Username: HDembinski  
> Created at: 2021-03-24 14:03:57 UTC  
> Updated at: 2022-11-15 11:24:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/318  

Sophisticated analyses use high-dimensional histograms, both disc and memory usage can become an issue. Disc usage is easy to fix by writing a compressed stream with Boost.Serialization and Boost.IOStreams. But this does not fix the memory issue.  
  
It would be interesting to add an experimental memory-compressed storage and see how that performs. In-memory compression can be achieved in several ways.  
  
* Zero-suppression: Some high-dimensional histograms contain a lot of zeros. A simple compression would just replace all the zeros with a code that indicates how many zeros are in the omitted gap. This is comparably easy to implement. Frequent re-allocations of memory in the beginning will be an issue, but that could be optimized over time.  
* We could use an in-memory compressor like Blosc: https://www.blosc.org/pages/blosc-in-depth

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-07-21 10:36:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/318#issuecomment-1191324923  

BLOSC is BSD licensed, so compatible with BSL.  
  
https://www.blosc.org/pages/blosc-in-depth/  
https://github.com/Blosc/c-blosc2
