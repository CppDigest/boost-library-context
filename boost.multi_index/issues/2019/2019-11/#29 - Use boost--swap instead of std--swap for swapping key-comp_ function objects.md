# #29 - Use boost::swap instead of std::swap for swapping key/comp_ function objects [Closed]

> Username: valziegler  
> Created at: 2019-11-07 11:20:04 UTC  
> Updated at: 2019-11-11 17:00:31 UTC  
> Closed at: 2019-11-11 17:00:31 UTC  
> Url: https://github.com/boostorg/multi_index/issues/29  

void ordered_index_impl<...>::swap_(ordered_index_impl<...>& x) uses std::swap for swapping the key and comp_ functors. Is there any particular reason for not using boost::swap here?

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-11-08 12:30:49 UTC  
> Url: https://github.com/boostorg/multi_index/issues/29#issuecomment-551711595  

Hi,  
  
No, there's no particular reason why ADL-enabled `swap` is not used, and in fact the current behavior is in disagreement with [[swappable.requirements]](https://eel.is/c++draft/swappable.requirements). Will fix this in a few days.

---

## Comment 2

> Username: valziegler  
> Created at: 2019-11-08 12:33:34 UTC  
> Url: https://github.com/boostorg/multi_index/issues/29#issuecomment-551715470  

Thank you!

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-11-09 12:03:12 UTC  
> Url: https://github.com/boostorg/multi_index/issues/29#issuecomment-552093526  

Fixed in 3caea31bfc4659a755650e2b0d846569b2227939, care to check?

---

## Comment 4

> Username: valziegler  
> Created at: 2019-11-11 13:32:03 UTC  
> Url: https://github.com/boostorg/multi_index/issues/29#issuecomment-552446884  

adl_swap works for me, thanks again.
