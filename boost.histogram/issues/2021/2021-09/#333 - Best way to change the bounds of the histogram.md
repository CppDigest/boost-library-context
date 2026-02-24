# #333 - Best way to change the bounds of the histogram [Closed]

> Username: vakokako  
> Created at: 2021-09-24 13:36:00 UTC  
> Updated at: 2021-09-27 11:14:47 UTC  
> Closed at: 2021-09-26 10:41:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/333  

I want to update the histogram to new bounds, same as `algorithm::shrink`, but for both shrinking **and** growing.  
  
Something like `shrinkOrExpand`:  
```cpp  
auto hist = make_histogram_with(std::vector<size_t>(), axis::regular(5, 0, 10));  
  
// new histogram with bounds [4, 14):  
auto hist2 = algorithm::reduce(hist, algorithm::shrinkOrExpand(4, 13));  
```  
  
What is the best way to do this?

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-09-26 10:41:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/333#issuecomment-927281611  

Expanding is intentionally not supported. There is a unique and unambiguous way to shrink a histogram that was already filled, but you cannot expand it unambiguously after it was already filled.

---

## Comment 2

> Username: vakokako  
> Created at: 2021-09-27 11:14:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/333#issuecomment-927767634  

The issue comes with under/overflow bins, correct?  
In our current use case, we wrap our own 1d histogram around boost::histogram and we would still like to extend our class with this functionality for 1d case, throwing if the under/overflow bins are present. Could you point to some methods (maybe in namespace `detail`) that would help us do it?
