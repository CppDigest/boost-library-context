# #62 - std::upper_bound with iterator hint? [Open]

> Username: NAThompson  
> Created at: 2019-09-24 18:56:35 UTC  
> Updated at: 2019-12-02 22:19:08 UTC  
> Url: https://github.com/boostorg/algorithm/issues/62  

In [this PR](https://github.com/boostorg/math/pull/255), I use `std::upper_bound` to calculate the empirical cumulative distribution function. However, the principle use of this function is in a quadrature, where each call to the function occurs with increasing argument. Hence, if I could cache an iterator hint, then the call complexity would be an amortized log(log(N)) (or is it amortized constant time? I forget. In either case, it's better than log(N).)  
  
Does boost.algorithm have iterator hints for binary searches?

---

## Comment 1

> Username: mclow  
> Created at: 2019-09-25 20:46:09 UTC  
> Url: https://github.com/boostorg/algorithm/issues/62#issuecomment-535213606  

> Does boost.algorithm have iterator hints for binary searches?  
It does not; in fact, it doesn't have `upper_bound` at all.  
  
However, that's an interesting idea. I'll think about it.  
At the very least, you should be able to bisect the search range based on the hint.

---

## Comment 2

> Username: Ma-XX-oN  
> Created at: 2019-12-02 21:21:20 UTC  
> Url: https://github.com/boostorg/algorithm/issues/62#issuecomment-560586155  

I've stumbled on a similar issue.  I would like to do a binary search, but would like to add a hint as to where to start.  However, if you think about it, you could to do the following:  
  
    auto new_begin = std::upper_bound(current_begin, current_end, test());  
    current_begin = new_begin;  
  
So that when you do the next call to `std::upper_bound` you'd ignore everything before the last search results.  This is like stating the initial partition, which I think is the same thing, right?

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-12-02 22:11:32 UTC  
> Updated at: 2019-12-02 22:19:08 UTC  
> Url: https://github.com/boostorg/algorithm/issues/62#issuecomment-560719133  

In my case, there is no hard guarantee that the desired element is strictly after the hint element. There is only a good reason to believe that the desired element is close to the hint element.  
  
My mental model of this is closer to branch prediction. Fast if it’s right, not a disaster if it’s wrong.
