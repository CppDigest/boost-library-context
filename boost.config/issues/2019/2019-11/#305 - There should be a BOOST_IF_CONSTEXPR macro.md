# #305 - There should be a BOOST_IF_CONSTEXPR macro [Closed]

> Username: rubenvb  
> Created at: 2019-11-23 14:58:47 UTC  
> Updated at: 2020-02-24 12:16:42 UTC  
> Closed at: 2020-02-24 12:16:42 UTC  
> Url: https://github.com/boostorg/config/issues/305  

This would expand to `if constexpr` where available, and plain `if` where it's not.  
  
This can help write "correct" code and suppress warnings when doing plain `if` on compile-time constants such as trait expressions.  
  
An example of this is found here:  
https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/char/detail/cast_char.hpp#L33  
  
Having this macro prevents further (verbose) proliferation of locally disabling the warning.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-02-24 12:16:42 UTC  
> Url: https://github.com/boostorg/config/issues/305#issuecomment-590293023  

Done
