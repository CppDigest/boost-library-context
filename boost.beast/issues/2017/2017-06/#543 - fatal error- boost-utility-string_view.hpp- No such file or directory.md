# #543 - fatal error: boost/utility/string_view.hpp: No such file or directory [Closed]

> Username: ksergey  
> Created at: 2017-06-26 09:54:35 UTC  
> Updated at: 2017-06-27 02:49:08 UTC  
> Closed at: 2017-06-27 02:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/543  

Catch compilation error:  
```  
/home/ksergey/dev/gdax_feed_handler/deps/Beast/include/beast/core/string.hpp:11:41: fatal error: boost/utility/string_view.hpp: No such file or directory  
```  
  
In my system installed boost 1.58. Which version of boost is required now?  
  
Will be good to see in Beast/CMakeLists.txt something like `find_package(Boost 1.60.0 REQUIRED COMPONENTS  ${BOOST_COMPONENTS})`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-26 09:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311015838  

You need a more recent Boost. I think 1.63, or 1.64.

---

## Comment 2

> Username: ksergey  
> Created at: 2017-06-26 10:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311016562  

Thank you!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-26 12:17:05 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311042624  

Keeping this open since there's an actionable request here:  
```  
find_package(Boost 1.60.0 REQUIRED COMPONENTS ${BOOST_COMPONENTS})  
```

---

## Comment 4

> Username: cmannett85  
> Created at: 2017-06-26 15:25:44 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311093058  

I'm running Fedora 25 (the latest stable release), and it only has Boost 1.60 available in its main repositories - which means I now can't compile Beast.  Boost 1.60 was only released 6 months ago.  
  
Is the thinking behind this requirement that Beast will be released soon within Boost, so it only needs to support the Boost version it ships in and nothing earlier?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-26 15:29:31 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311094189  

>Is the thinking behind this requirement that Beast will be released soon within Boost, so it only needs to support the Boost version it ships in and nothing earlier?  
  
Yes. Although, maybe to get you going I could `#ifdef` the `string_view` depending on the Boost version, would you like to make that change? Its in `<beast/core/string.hpp>`. Something like this:  
  
```  
#include <boost/version.hpp>  
#if BOOST_VERSION < 106400  
#include <boost/utility/string_ref.hpp>  
using string_view = boost::string_ref  
#else  
#include <boost/utility/string_view.hpp>  
using string_view = boost::string_view  
#endif  
```

---

## Comment 6

> Username: cmannett85  
> Created at: 2017-06-26 15:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/543#issuecomment-311098435  

That's fine, thank you!
