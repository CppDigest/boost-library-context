# #349 - Add collector accumulator [Closed]

> Username: HDembinski  
> Created at: 2022-01-19 17:02:20 UTC  
> Updated at: 2025-11-04 08:12:39 UTC  
> Closed at: 2025-11-04 08:12:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/349  

A simple accumulator that just fills a `std::vector` with the samples passed to it. The user can then run any kind of statistical estimation on the sample. It is a very inefficient accumulator, but sometimes that's acceptable and the flexibility it offers is more important than the performance.  
  
Pseudo-code  
```c++  
// C++17  
namespace bh = boost::histogram;  
auto h = bh::make_histogram(bh::axis::integer(0, 2),  
              bh::dense_storage<bh::accumulators::Collector<>>());  
  
// fill  
h(0, 1);  
h(0, 2);  
h(0, 3);  
h(1, 4);  
h(1, 5);  
  
for (auto x: h.at(0))  
   std::cout << x << std::endl;  
```  
Prints: 1 2 3

---

## Comment 1

> Username: wiso  
> Created at: 2023-11-06 11:52:57 UTC  
> Updated at: 2023-11-06 11:54:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/349#issuecomment-1794657141  

I think this would be a very nice feature since this is basically a groupby. I have submitted a simple PR. I am using `collector` instead of `Collector` for consistency with the other accumulators.

---

## Comment 2

> Username: wiso  
> Created at: 2025-11-03 22:12:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/349#issuecomment-3482802830  

I guess this can be closed due to #390

---

## Comment 3

> Username: HDembinski  
> Created at: 2025-11-04 08:12:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/349#issuecomment-3484459099  

True.
