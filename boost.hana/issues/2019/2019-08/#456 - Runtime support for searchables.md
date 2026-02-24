# #456 - Runtime support for searchables [Open]

> Username: jupp0r  
> Created at: 2019-08-06 00:43:50 UTC  
> Updated at: 2025-12-13 00:51:08 UTC  
> Url: https://github.com/boostorg/hana/issues/456  

This doesn't compile  
  
    #include <boost/hana.hpp>  
    #include <boost/hana/ext/std/tuple.hpp>  
    #include <boost/optional.hpp>  
  
    bool f() {  
        auto t = std::make_tuple(boost::optional<int>{boost::none}, boost::optional<int>{4});  
        return boost::hana::all(t);  
    }  
  
This does, but not under MSVC (it rightfully complains that the lambda passed to `all_of` needs to return a `Logical`.  
  
    #include <boost/hana.hpp>  
    #include <boost/hana/ext/std/tuple.hpp>  
    #include <boost/optional.hpp>  
  
    bool f() {  
        auto t = std::make_tuple(boost::optional<int>{boost::none}, boost::optional<int>{4});  
        return boost::hana::all_of(t, [](auto v) { return static_cast<bool>(v); });  
    }  
  
This compiles everywhere, but is suboptimal due to lack of early return:  
  
    #include <boost/hana.hpp>  
    #include <boost/hana/ext/std/tuple.hpp>  
    #include <boost/optional.hpp>  
  
    bool f() {  
        auto t = std::make_tuple(boost::optional<int>{boost::none}, boost::optional<int>{4});  
        return boost::hana::fold(t, true, [](bool state, auto v) {  
            return state && static_cast<bool>(v);  
        });  
    }  
  
Reading through the documentation, I couldn't find any information about runtime support of algorithms. Is this a bug or an expected limitation?

---

## Comment 1

> Username: jonesmz  
> Created at: 2025-12-09 06:20:44 UTC  
> Url: https://github.com/boostorg/hana/issues/456#issuecomment-3630544031  

Waking this issue up 6+ years later  
  
Did you ever determine anything further on this?  
  
I'm looking to replace a function that does:  
  
```  
std::string someRuntimeValue = ...;  
boost::hana::for_each(  
                boost::hana::keys(mapObj),  
                [&](auto const key)  
                {  
                    constexpr std::string_view keyString = boost::hana::to<char const*>(key);  
                    if(keyString == someRuntimeValue)  
                    {  
                        // do the work  
                    }  
                });  
```  
with something more like:  
  
```  
std::string someRuntimeValue = ...;  
auto const& foundItem = boost::hana::find(mapObj, someRuntimeValue);  
// do the work  
```

---

## Comment 2

> Username: ricejasonf  
> Created at: 2025-12-13 00:51:08 UTC  
> Url: https://github.com/boostorg/hana/issues/456#issuecomment-3648637730  

That looks like you could use `boost::hana::filter` if you are not trying to do the "early return" thing.
