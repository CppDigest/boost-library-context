# #359 - Use of deprecated Bind placeholders [Open]

> Username: lukem  
> Created at: 2021-04-19 06:38:00 UTC  
> Updated at: 2022-07-31 21:21:04 UTC  
> Url: https://github.com/boostorg/python/issues/359  

Some of the Boost.Python headers still use the deprecated Bind placeholders in the global namespace:  
  
> /usr/include/boost/bind.hpp:41:1: note: #pragma message: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
  
Fix the files:  
- <boost/python/exception_translator.hpp>  
- <boost/python/iterator.hpp>  
  
with fixes:  
1. Replace <boost/bind.hpp> and <boost/bind/placeholders.hpp> with <boost/bind/bind.hpp>  
2. Replace (global) `_1` with `boost::placeholders::_1`  
3. Replace (global) `_2` with `boost::placeholders::_2`

---

## Comment 1

> Username: finjulhich  
> Created at: 2021-12-01 17:34:43 UTC  
> Url: https://github.com/boostorg/python/issues/359#issuecomment-983871151  

I see still in boost 1.76 direct inclusion of boost/bind.hpp from  
  
boost/python/exception_translator.hpp  
boost/python/iterator.hpp

---

## Comment 2

> Username: ijnek  
> Created at: 2022-07-31 21:20:39 UTC  
> Updated at: 2022-07-31 21:21:04 UTC  
> Url: https://github.com/boostorg/python/issues/359#issuecomment-1200502503  

#315 should have fixed this, but is only available from 1.77 onwards.  
@stefanseefeld Wondering if you do backports into older versions?  
[Ubuntu22.04 uses 1.74.0](https://packages.ubuntu.com/jammy/libboost-all-dev) and these warnings arise in any packages that use the system libraries.  
  
For example: https://github.com/ros-perception/vision_opencv/issues/449
