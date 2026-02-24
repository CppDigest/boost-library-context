# #154 - Variance of performance measurements is unclear [Closed]

> Username: jbytheway  
> Created at: 2015-06-23 02:52:55 UTC  
> Updated at: 2015-06-26 19:59:01 UTC  
> Closed at: 2015-06-26 19:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/154  

The auto-generated graphs of performance in the documentation are very nice, but it's not clear how precise the measurements are.  It would be good if you could clarify your methods here.  For example, are you taking the average of many measurements?  If so, how many?  Mean or median?  
  
How much variation is there?  Clearest of all to indicate this would be error bars on the plots.  Probably putting error bars on all the plots would be excessive and noisy, but maybe you could do so on one or two, just to reassure your readers that there's not too much noise.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-25 13:03:59 UTC  
> Url: https://github.com/boostorg/hana/issues/154#issuecomment-115247741  

The timings are quite precise, for every time I regenerate the plots they are almost the same. However, I do not take the mean of many measurements, for that would (at the minimum) double the time to run all the benchmarks, which is unacceptable.

---

## Comment 2

> Username: jbytheway  
> Created at: 2015-06-26 03:55:56 UTC  
> Url: https://github.com/boostorg/hana/issues/154#issuecomment-115492965  

Just saying that much in the docs would be worthwhile.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-26 19:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/154#issuecomment-115857668  

Good suggestion, thanks.
