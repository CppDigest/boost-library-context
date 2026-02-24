# #33 Fix deprecated copy warnings and misc. C++20 `operator==` warnings [Closed]

> Username: cmazakas  
> Created at: 2021-11-17 19:33:51 UTC  
> Updated at: 2021-11-18 15:44:00 UTC  
> Closed at: 2021-11-18 15:43:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/33  



---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-18 15:43:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/33#issuecomment-972986449  

@pdimov I'm gonna close this PR and refactor it into 3 separate ones.  
  
Namely, this PR introduced a broken copy assignment operator for the `test::cxx11_allocator` but this wasn't caught because of a defect in the memory tracker's reference counting.  
  
I'm going to make a separate issue for each warning which will have its own PR and this way, everything should hopefully be a bit more trackable.

---
