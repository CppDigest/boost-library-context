# #175 - Fill with iterator/array [Closed]

> Username: henryiii  
> Created at: 2019-03-19 21:01:49 UTC  
> Updated at: 2019-08-19 22:28:34 UTC  
> Closed at: 2019-08-19 22:15:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/175  

Would a dynamic fill be faster if there was a fill with spans? Basically something like this:  
  
```cpp  
// Could be a variant from Python  
auto hist = make_histogram(regular(10,0,1));  
  
values = std::vector<double>(.2,.3,.1,.9);  
  
// Fill with a span (for example, https://github.com/martinmoene/span-lite)  
// Or with a set of iterators and a length. Not really a great interface, but if it's mostly for Python's use, it could be protected or similar.  
hist.fill_n(values); // Any span would work  
```  
  
This could maybe be implemented in such a way that any dynamic penalties would only be occurred once, rather than per fill? Then this, which would be very expensive if done each time, could be done more reasonably:  
  
```cpp  
auto hist = make_weighed_histogram(regular(10,0,1), regular(10,0,1));  
  
values1 = std::vector<double>(.2,.3,.1,.9);  
values2 = std::vector<double>(.3,.6,.7,.1);  
weights = std::vector<weight>(1.,1.,1.,.5);  
  
std::vector<variant<span<double>,span<weight>> values = {values1, values2, weights};  
  
hist.fill_n(values); // All spans must have the same length  
// First non-weight or sample span must match first axis, etc.  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-06-11 12:09:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/175#issuecomment-500812834  

I am planning to implement a `fill` method along these lines.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-08-19 22:15:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/175#issuecomment-522774837  

Implemented in develop. It is much faster.

---

## Comment 3

> Username: henryiii  
> Created at: 2019-08-19 22:18:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/175#issuecomment-522775429  

Fantastic! I was wondering if that would go in after 1.71 was released. I'm at a training workshop at LBNL this week but am beginning to work heavily on the bindings now.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-08-19 22:23:52 UTC  
> Updated at: 2019-08-19 22:24:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/175#issuecomment-522776950  

We should discuss about this, because I wanted to give the bindings a major rewrite myself. I have a very clear picture in mind what needs to be done. The fill method changes everything for the bindings. We don't need specializations any more for histograms with static axes. Just making bindings for the histogram with a general `std::vector<axis::variant<...>>` is sufficient, because this is as fast as a histogram with static axes now.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-08-19 22:28:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/175#issuecomment-522778168  

Parallelization has to be removed in boost.histogram, because it doesn't really work on that outer level. There is potential to parallelize inside the fill method, which will be realized in the future.
