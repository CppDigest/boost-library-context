# #402 - is_contiguous_iterator should be a public customization point [Open]

> Username: jbytheway  
> Created at: 2014-12-31 04:32:25 UTC  
> Updated at: 2017-04-25 15:07:16 UTC  
> Url: https://github.com/boostorg/compute/issues/402  

Currently the library specializes is_contiguous_iterator for containers it knows to have contiguous iterators.  However, it seems likely that users will have their own vector-like containers (indeed, there are several in Boost already).  It would be helpful if is_contiguous_iterator was a documented customization point by which users could mark their own iterators as contiguous.  
  
(Indeed, the library already misses one standard one: std::string::iterator)  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-31 05:12:33 UTC  
> Url: https://github.com/boostorg/compute/issues/402#issuecomment-68424167  

I agree, this would probably be better to have in the public API. There have been some discussions in the past (http://lists.boost.org/boost-users/2013/03/78146.php) to add this sort of trait somewhere more general in Boost (perhaps Boost.TypeTraits). If possible I'd like this trait to actually live elsewhere as I feel it is generally useful, but for now I can add it to the Boost.Compute public API.

---

## Comment 2

> Username: jbytheway  
> Created at: 2014-12-31 05:17:52 UTC  
> Url: https://github.com/boostorg/compute/issues/402#issuecomment-68424300  

Indeed, it would be even better if it were central.  Indeed, there's a proposal to have it in std (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4183.pdf) but that will be a while...

---

## Comment 3

> Username: jbytheway  
> Created at: 2014-12-31 13:45:26 UTC  
> Url: https://github.com/boostorg/compute/issues/402#issuecomment-68442420  

I imagine if you made a pull request for Boost.TypeTraits it would be looked on favourably.
