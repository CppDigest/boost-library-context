# #21 - movelib appears to ignore std::iterator_traits [Closed]

> Username: mikezackles  
> Created at: 2018-10-10 00:03:16 UTC  
> Updated at: 2020-10-19 12:39:01 UTC  
> Closed at: 2020-10-19 12:39:01 UTC  
> Url: https://github.com/boostorg/move/issues/21  

I was attempting to use range-v3 with `boost::container::small_vector`, but by default there is an issue with converting range-v3 iterators for use with `small_vector::insert`. range-v3 internally specializes `std::iterator_traits`, but movelib seems to ignore this specialization.  
  
I can fix the issue with something like  
```  
namespace boost::movelib {  
  template<typename I, typename S>  
  struct iterator_traits< ::ranges::common_iterator<I, S>>  
    : std::iterator_traits< ::ranges::common_iterator<I, S>>  
  {};  
}  
```  
but I wanted to make sure this is intended behavior.  
  
Thanks!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-12-31 16:15:57 UTC  
> Url: https://github.com/boostorg/move/issues/21#issuecomment-450663739  

Yes, it is intended behavior not to rely on std::iterator_traits, specially for compilation times. I don't know which protocol ranges-v3 follows for its iterators, but it would be a nice addition.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-10-19 12:39:01 UTC  
> Url: https://github.com/boostorg/move/issues/21#issuecomment-712128315  

It seems ranges-v3 compatibility it's not a very requested feature since 2018 so I'm closing this issue. Thanks for the issue.
