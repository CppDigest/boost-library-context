# #150 - Remove max_load_factor [Closed]

> Username: sdkrystian  
> Created at: 2020-08-12 01:22:48 UTC  
> Updated at: 2020-08-22 21:53:58 UTC  
> Closed at: 2020-08-22 21:53:58 UTC  
> Url: https://github.com/boostorg/json/issues/150  

Currently, `max_load_factor` is set to `1.0` and cannot be changed.   
  
The `std::ceil` calls in `object::rehash` are not being constant folded for one reason or another, so those are turned into SSE2 instructions that don't do anything meaningful. Removing `max_load_factor` also means that the number of buckets is always equal to the capacity, so we don't have to calculate it each time we destroy or clear an object.  
  
Benchmarks show a ~3.4% improvement on `citm_catalog.json` and a ~3.8% improvement on `twitter.json`.  
```  
Parse array.json,clang x64/sse2,loadfactor (pool),388  
Parse array.json,clang x64/sse2,develop (pool),392  
Parse canada.json,clang x64/sse2,loadfactor (pool),563  
Parse canada.json,clang x64/sse2,develop (pool),568  
Parse citm_catalog.json,clang x64/sse2,loadfactor (pool),826  
Parse citm_catalog.json,clang x64/sse2,develop (pool),795  
Parse citm_catalog_nws.json,clang x64/sse2,loadfactor (pool),293  
Parse citm_catalog_nws.json,clang x64/sse2,develop (pool),286  
Parse integers-32.json,clang x64/sse2,loadfactor (pool),593  
Parse integers-32.json,clang x64/sse2,develop (pool),591  
Parse integers-64.json,clang x64/sse2,loadfactor (pool),817  
Parse integers-64.json,clang x64/sse2,develop (pool),816  
Parse random.json,clang x64/sse2,loadfactor (pool),470  
Parse random.json,clang x64/sse2,develop (pool),467  
Parse small.json,clang x64/sse2,loadfactor (pool),473  
Parse small.json,clang x64/sse2,develop (pool),465  
Parse strings.json,clang x64/sse2,loadfactor (pool),7674  
Parse strings.json,clang x64/sse2,develop (pool),7429  
Parse twitter.json,clang x64/sse2,loadfactor (pool),551  
Parse twitter.json,clang x64/sse2,develop (pool),531  
```
