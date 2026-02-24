# #140 - [Travis] Fail the build when a performance regression happens [Closed]

> Username: ldionne  
> Created at: 2015-06-18 20:05:42 UTC  
> Updated at: 2016-06-11 19:49:10 UTC  
> Closed at: 2016-04-20 01:20:38 UTC  
> Url: https://github.com/boostorg/hana/issues/140  

We have compile-time and runtime benchmarks, but it should be possible to fail a Travis build when there is a performance regression.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-04-20 01:20:38 UTC  
> Url: https://github.com/boostorg/hana/issues/140#issuecomment-212195306  

This is complicated to implement (what exactly do we consider a regression, while still accounting for possible variability in the Travis benchmark results?). Also, we'll be moving the benchmark suite to [Metabench](http://github.com/ldionne/metabench), so this should probably be addressed there if anything.
