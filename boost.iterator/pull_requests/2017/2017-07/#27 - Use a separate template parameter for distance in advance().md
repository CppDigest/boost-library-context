# #27 Use a separate template parameter for distance in advance(). [Merged]

> Username: Lastique  
> Created at: 2017-07-10 11:54:56 UTC  
> Updated at: 2017-07-12 16:47:56 UTC  
> Merged at: 2017-07-12 16:20:35 UTC  
> Closed at: 2017-07-12 16:20:35 UTC  
> Url: https://github.com/boostorg/iterator/pull/27  

This follows `std::advance` interface and also allows to use distance types other than iterator's `difference_type` (if the iterator supports that).  
  
This feature would be useful in the implementation of `boost::next/prior`. See the discussion in https://github.com/boostorg/utility/pull/30.

---

## Comment 1

> Username: morinmorin  
> Created_at: 2017-07-11 15:27:29 UTC  
> Url: https://github.com/boostorg/iterator/pull/27#issuecomment-314481037  

Although `std::advance` has the following signature,  
```  
template <typename Iterator, typename Distance>  
void advance(Iterator& it, Distance n)  
```  
I implemented `boost::advance` as  
```  
template <typename Iterator>  
void advance(Iterator& it, typename iterator_difference<Iterator>::type n)  
```  
to follow the signature of `std::next/prev` and `std::experimental::ranges::advance` (in Range TS). This was because I couldn't find enough reasoning for using a legacy-looking signature. But at the same time, I thought that this choice might be arguable.  
  
My motivation of implementing `boost::advance` was to add Boost's IteratorTraversalCategory-aware iterator operations; I don't have strong opinion on the signature of `boost::advance`. If the two template-parameter version is needed, then I'm fine with changing the signature.

---
