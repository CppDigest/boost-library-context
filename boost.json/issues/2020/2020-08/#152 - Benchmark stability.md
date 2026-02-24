# #152 - Benchmark stability [Closed]

> Username: sdkrystian  
> Created at: 2020-08-12 15:53:22 UTC  
> Updated at: 2020-08-27 09:33:42 UTC  
> Closed at: 2020-08-27 09:33:42 UTC  
> Url: https://github.com/boostorg/json/issues/152  

The current method we use for benchmarking isn't entirely stable with large numbers of trials. File data could be flushed from the cache using `_mm_clflush` in between runs, and there might be a way to flush the instructions cache too.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-12 16:38:29 UTC  
> Url: https://github.com/boostorg/json/issues/152#issuecomment-672984366  

Define "stable." When I have 10 trials, most of them have exactly the same throughput. The benchmark program discards the outliers (top and bottom 2 or so). If you are getting different results, I would suspect your hardware is the culprit.

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-12 17:32:53 UTC  
> Url: https://github.com/boostorg/json/issues/152#issuecomment-673011450  

If you run a benchmark 20 times on a file (once for develop and once for your branch) the benchmark that runs second will have a significant advantage because it's likely that the file is entirely within the cache (and at a lower level),

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 17:47:18 UTC  
> Url: https://github.com/boostorg/json/issues/152#issuecomment-673018327  

I don't see why. If Trials==20, then the file will be cached on the first trial of the first benchmark. Why would the file only become cached on all the trials of the second branch?

---

## Comment 4

> Username: sdkrystian  
> Created at: 2020-08-12 18:37:21 UTC  
> Url: https://github.com/boostorg/json/issues/152#issuecomment-673042266  

The more a resource is used, the lower it gets in the cache hierarchy.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-27 09:33:42 UTC  
> Url: https://github.com/boostorg/json/issues/152#issuecomment-681837884  

I don't think this matters
