# #408 - Problem with re-definition of `_DEBUG` in `wrap_python.hpp` [Open]

> Username: mont29  
> Created at: 2023-01-06 09:36:19 UTC  
> Updated at: 2023-01-06 09:36:19 UTC  
> Url: https://github.com/boostorg/python/issues/408  

[python/include/boost/python/detail/wrap_python.hpp](https://github.com/boostorg/python/blob/develop/include/boost/python/detail/wrap_python.hpp#L45) undefines `_DEBUG`, and then redefines it as an empty macro at the end of the file.  
  
However, this macro is not necessarily empty originally.  
  
This discrepancy of values breaks building debug Blender 3.5 (aka current master) with any official boost library, as TBB (2020 U3) has some complex logic to handle various cases of values for `_DEBUG` (see [include/tbb/tbb_config.h](https://github.com/oneapi-src/oneTBB/blob/tbb_2020/include/tbb/tbb_config.h#L415)).  
  
One could argue that the TBB 2020 code is also weak (it seems that they changed it in the more recent oneTBB), but it does rely on the value of `_DEBUG` to stay the same, which imho is a reasonable expectation?.  
  
Not sure how keeping the same value for this debug could be ensured though... Maybe through `BOOST_PP_ASSIGN_SLOT`?  
  
-----  
  
PS: For now, [Blender is patching](https://developer.blender.org/rB019b930) its own boost to redefine `_DEBUG` as 1, but this is obviously not a proper solution in general.
