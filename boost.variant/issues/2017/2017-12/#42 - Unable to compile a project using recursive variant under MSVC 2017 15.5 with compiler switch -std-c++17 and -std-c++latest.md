# #42 - Unable to compile a project using recursive variant under MSVC 2017 15.5 with compiler switch /std:c++17 and /std:c++latest [Closed]

> Username: jhruby  
> Created at: 2017-12-08 08:00:19 UTC  
> Updated at: 2018-02-20 19:23:46 UTC  
> Closed at: 2018-02-20 18:54:33 UTC  
> Url: https://github.com/boostorg/variant/issues/42  

15.4 compiler compiles with all flags std flags without any issues. 15.5 version is unable to compile correctly  /std:c++17 and /std:c++latest.  
  
Check out the library:  
https://github.com/no1msd/mstch  
  
Configure CMAKE and CMAKE_CXX_FLAGS += /std:c++17  
  
After opening project under visual studio compilation fails:  
[compiler-output.txt](https://github.com/boostorg/variant/files/1541884/compiler-output.txt)  
  
Please note that switches /std:c++11 and /std:c++14 are working correctly.  
  
Thanks

---

## Comment 1

> Username: egorpugin  
> Created at: 2018-01-17 13:54:33 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-358311057  

Same for me.

---

## Comment 2

> Username: egorpugin  
> Created at: 2018-01-17 14:13:19 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-358316290  

Simple repro:  
```  
#include <boost/variant.hpp>  
  
#include <memory>  
  
int main()  
{  
    using node = boost::make_recursive_variant<  
        int,  
        std::shared_ptr<boost::recursive_variant_>  
    >::type;  
  
    node x = 1;  
}  
```  
  
(Almost) original case from mstch.  
```  
  
#include <boost/variant.hpp>  
  
#include <map>  
#include <memory>  
#include <vector>  
  
int main()  
{  
    using node = boost::make_recursive_variant<  
        std::nullptr_t, std::string, int, double, bool,  
        std::shared_ptr<boost::recursive_variant_>,  
        std::map<const std::string, boost::recursive_variant_>,  
        std::vector<boost::recursive_variant_>>::type;  
  
    node x = 1;  
}  
```  
  
Both works on VS15.5 in C++14 mode, does not work in C++17 mode.

---

## Comment 3

> Username: egorpugin  
> Created at: 2018-02-06 21:46:16 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-363575354  

This works for me as a workaround.  
```  
#define BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES  
#include <mstch/mstch.hpp>  
```

---

## Comment 4

> Username: jhruby  
> Created at: 2018-02-09 14:18:16 UTC  
> Updated at: 2018-02-09 14:18:31 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-364445678  

Thanks for workaround this could let me to upgrade visual studio :)

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-02-17 18:46:33 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-366462590  

Other workaround `#define BOOST_VARIANT_DETAIL_NO_SUBSTITUTE`

---

## Comment 6

> Username: apolukhin  
> Created at: 2018-02-20 19:23:46 UTC  
> Url: https://github.com/boostorg/variant/issues/42#issuecomment-367089534  

Many thanks for providing tests and fixes!  
Merged to develop, will be merged to master branch after the regression tests cycle.
