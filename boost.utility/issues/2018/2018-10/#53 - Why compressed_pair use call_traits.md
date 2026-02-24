# #53 - Why compressed_pair use call_traits? [Closed]

> Username: FirstLoveLife  
> Created at: 2018-10-29 06:16:24 UTC  
> Updated at: 2018-10-29 09:57:03 UTC  
> Closed at: 2018-10-29 09:57:03 UTC  
> Url: https://github.com/boostorg/utility/issues/53  

I cannot see any pros of using call_traits [here](https://github.com/boostorg/utility/blob/master/include/boost/detail/compressed_pair.hpp#L110). They just provide alias here, trival typedef/using is enough, imo.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-10-29 09:57:03 UTC  
> Url: https://github.com/boostorg/utility/issues/53#issuecomment-433850761  

If you look at `boost::call_traits` implementation, `param_type` definition is not quite trivial. It provides a more efficient way to pass small arguments to constructors of `compressed_pair`. I don't see a compelling reason to replicate that logic in Boost.CompressedPair.
