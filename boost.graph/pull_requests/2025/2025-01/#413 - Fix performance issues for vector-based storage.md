# #413 Fix performance issues for vector-based storage [Merged]

> Username: AnthonyVH  
> Created at: 2025-01-15 18:14:51 UTC  
> Updated at: 2025-03-25 22:33:28 UTC  
> Merged at: 2025-01-22 09:11:39 UTC  
> Closed at: 2025-01-22 09:11:39 UTC  
> Url: https://github.com/boostorg/graph/pull/413  

- None of the functions called inside `vertex_by_property()` required a mutable graph object, but `vertex_by_property()` itself did. This is now fixed.  
- At several locations in the code base there was the equivalent of `container.reserve(container.size() + 1)`, e.g. `add_vertex()`. Calling such an `add_vertex()` in a loop with an `std::vector` backing the vertex storage results in O(N²) time complexity instead of the expected (amortized) O(N). This is fixed by allowing the container to grow as expected.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-01-16 09:31:36 UTC  
> Updated_at: 2025-01-16 09:32:49 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2595006058  

Thanks for your pull request.   
  
> - None of the functions called inside `vertex_by_property()` required a mutable graph object, but `vertex_by_property()` itself did. This is now fixed.  
  
Yup, that's good.  
  
> - At several locations in the code base there was the equivalent of `container.reserve(container.size() + 1)`, e.g. `add_vertex()`. Calling such an `add_vertex()` in a loop with an `std::vector` backing the vertex storage results in O(N²) time complexity instead of the expected (amortized) O(N). This is fixed by allowing the container to grow as expected.  
  
I don't actually see why this is so, unless `resize` is doing something really pessimistic and reallocating the entire vector every time. Have you benchmarked this to confirm the change in performance?   
Regardless, I prefer your implementation to the existing one, although please use `auto` where you can.  
Can you use `emplace_back()` to create an empty vector instead of `push_back(std::vector< cg_vertex >())`?

---

## Comment 2

> Username: AnthonyVH  
> Created_at: 2025-01-16 10:38:52 UTC  
> Updated_at: 2025-01-16 12:22:36 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2595167249  

> I don't actually see why this is so, unless `resize` is doing something really pessimistic and reallocating the entire vector every time.  
  
Everything I'm writing here assumes that `add_vertex()` is going to be called in a loop. Which I guess is a pretty reasonable assumption, how else is anyone going to construct a graph.  
  
TLDR: Calling `resize(prev_size + 1)` forced reallocation on every call to `add_vertex()`, making it O(N). Letting the `std::vector` decide when to resize itself by calling `push_back()`/`emplace_back()` makes that O(1).  
  
Now, for the long and windy explanation. Inside `add_vertex()` the old code did `vertices.resize(prev_size + 1)`. From [`std::vector<>::resize()`](https://en.cppreference.com/w/cpp/container/vector/resize):  
  
> Resizes the container to contain count elements, does nothing if count == size(). If the current size is greater than count, the container is reduced to its first count elements.   
  
and  
  
> Complexity: Linear in the difference between the current size and count. Additional complexity possible due to reallocation if capacity is less than count.   
  
I.e. in case the new size is larger than the old, `resize()` is going to increase the capacity of the underlying storage to exactly the requested new size. Crucially, it is _not_ going to apply the growth factor it would when an "automatic" capacity increment happens (e.g. if you call `push_back()` on an `std::vector` for which `size() == capacity()`).  
  
Note that it clearly states that the complexity is linear, not O(1). Growing an `std::vector` by pushing elements into it is _amortized_ O(1). It's exactly because of reallocation that this is amortized. Assume an empty `std::vector` with capacity X, then the first X pushes are "free". On the X + 1th `push_back()` the underlying storage is full, so `std::vector` will call `resize()` internally, move/copy all existing X elements to new storage, and then push the X + 1th element on. So on average you pay O(X) / X = O(1) per `push_back()`. When this automatic resize happens the capacity will grow by a certain factor (1.5 for MSVC/libc++, and 2 for libstdc++ AFAIK), which ensures the amortized O(1) complexity for element insertion.  
  
Manually calling `resize()`/`reserve()` in a loop is always a bad idea, because it ensures the automatic resizing is side-stepped. Now every one of those calls is going to reallocate all of the existing elements in the `std::vector`, because the capacity was only  large enough to hold `prev_size` elements. Meaning every `add_vector()` calls takes O(N) time, whereas normally `resize()` would only be called every N `add_vector()` calls, meaning it _could_ be O(1).  
  
> Have you benchmarked this to confirm the change in performance?  
  
  
I did not see existing performance benchmarks, so I didn't try to implement one. However, the behavior of calling `resize()` is well defined, so for sure the complexity of `add_vertex()` goes from O(N) to O(1) with my change.  
  
Here is a quick benchmark I threw together illustrating the difference: [quick-bench](https://quick-bench.com/q/CizM4JSwOAU1oko52IsZKFqPkHE). Note that the difference is only 10% here, most likely because there's not that much data to copy around.  
  
![image](https://github.com/user-attachments/assets/ab836326-775a-4e9a-901b-64e7271894d4)  
  
I tried creating another benchmark where each entry in the vector is larger (which would be the case when e.g. a vertex has bundled properties that take up a reasonable amount of space). Unfortunately the quick-bench.com website kept timing out on that. I expect the difference to be much more pronounced in this case. If you'd like to try yourself, here's the code in [Compiler Explorer](https://godbolt.org/z/M18s44xnE) (you can hit "Quick bench" in the top left there).  
  
*Edit:* I realize the benchmarks show only a small speed difference. I guess the compiler somehow sees through the whole thing? I know of instances in industrial code where this exact same change caused a massive speed-up.

---

## Comment 3

> Username: AnthonyVH  
> Created_at: 2025-01-16 10:45:51 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2595183969  

Here's another snippet from `cppreference.com` that describes the issue with the `resize()` call (which will call `reserve()` under the hood). From [`std::vector<>::reserve()`](https://en.cppreference.com/w/cpp/container/vector/reserve):  
  
> Correctly using reserve() can prevent unnecessary reallocations, but inappropriate uses of reserve() (for instance, calling it before every [push_back()](https://en.cppreference.com/w/cpp/container/vector/push_back) call) may actually increase the number of reallocations (by causing the capacity to grow linearly rather than exponentially) and result in increased computational complexity and decreased performance. For example, a function that receives an arbitrary vector by reference and appends elements to it should usually not call reserve() on the vector, since it does not know of the vector's usage characteristics.  
>  
> When inserting a range, the range version of [insert()](https://en.cppreference.com/w/cpp/container/vector/insert) is generally preferable as it preserves the correct capacity growth behavior, unlike reserve() followed by a series of [push_back()](https://en.cppreference.com/w/cpp/container/vector/push_back)s.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-01-22 09:06:08 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2606661360  

Ohh, I get it, sorry, I just completely forgot that `push_back` will amortize the reallocations.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-01-22 09:14:19 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2606679285  

Thanks for a great improvement to the efficiency!  
  
I know the code change was very small, but in future please don't combine unrelated changes into one PR.

---

## Comment 6

> Username: AnthonyVH  
> Created_at: 2025-01-22 09:20:22 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2606692986  

Thanks a lot for the quick merge! I'll try to better pay attention to the PR hygiene next time 😁.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2025-03-24 23:30:18 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2749626495  

Btw, whatever differences you can benchmark, I can put in the release notes. At the moment, I think the only thing I can say, and correct me if I get it wrong, is "A 10% improvement to graph construction when using `adjacency_list` with `vector`-based storage."  
Do I need to be more specific about exactly where/what the `vector` is used for? I mean, edge list vs vertex list?

---

## Comment 8

> Username: AnthonyVH  
> Created_at: 2025-03-25 16:19:23 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2751817994  

I've tried creating a benchmark to show the result before and after my changes, but am finding it exceedingly difficult to trick the compiler. So unfortunately for now, I can't present a meaningful result (i.e. a plot clearly showing O(N) vs O(N^2) `add_vertex` behavior). I'll have another go later this week.  
  
The fix was only for the vertex list. (I did just see that the same `resize()` call exists for `add_edge()` in `detail/adjacency_list.hpp`).

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2025-03-25 22:33:26 UTC  
> Url: https://github.com/boostorg/graph/pull/413#issuecomment-2752695567  

No worries, thanks for trying. It's hard to outsmart the compiler. 😂

---
