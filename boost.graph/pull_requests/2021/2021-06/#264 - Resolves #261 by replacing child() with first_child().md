# #264 Resolves #261 by replacing child() with first_child() [Merged]

> Username: qbit86  
> Created at: 2021-06-02 23:18:19 UTC  
> Updated at: 2022-03-04 02:10:24 UTC  
> Merged at: 2022-03-04 02:10:18 UTC  
> Closed at: 2022-03-04 02:10:18 UTC  
> Url: https://github.com/boostorg/graph/pull/264  



---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2021-12-10 04:17:10 UTC  
> Url: https://github.com/boostorg/graph/pull/264#issuecomment-990602860  

I'd personally prefer to depend on Boost.Heap and use `d_ary_heap` from there. Not sure why it's currently copied into this detail directory.

---

## Comment 2

> Username: qbit86  
> Created_at: 2021-12-10 10:23:52 UTC  
> Url: https://github.com/boostorg/graph/pull/264#issuecomment-990817413  

> I'd personally prefer to depend on Boost.Heap and use `d_ary_heap` from there. Not sure why it's currently copied into this detail directory.  
  
I don't believe that specific `d_ary_heap_indirect<>` could be easily replaced with general purpose `d_ary_heap<>`.  
The former manipulates values indirectly via explicit indices and has some hacks [1] to cooperate with external logic of updating the underlying map.  
  
[1] [boost/graph/detail/d_ary_heap.hpp#L161..L164](https://github.com/boostorg/graph/blob/b76ffc7f2a56955e2cdeb93f6902c40d3bbcffdd/include/boost/graph/detail/d_ary_heap.hpp#L161..L164)

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2021-12-13 01:21:32 UTC  
> Url: https://github.com/boostorg/graph/pull/264#issuecomment-992026373  

Oh, dear, that's a bit disappointing.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2022-03-04 02:10:24 UTC  
> Url: https://github.com/boostorg/graph/pull/264#issuecomment-1058756020  

Thanks for this!

---
