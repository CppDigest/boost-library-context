# #140 - document how histograms can be bootstrapped with a prior distribution [Closed]

> Username: HDembinski  
> Created at: 2018-12-17 15:02:33 UTC  
> Updated at: 2019-01-22 22:50:04 UTC  
> Closed at: 2019-01-22 22:50:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/140  

From #103:  
Iterators are const. This prevents us from bootstrapping the histogram  
with a prior distribution using STL algorithms like std::fill(),  
std::generate(), or std::sample().

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-22 22:50:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/140#issuecomment-456593935  

fixed in develop
