# #25 - Result of `BOOST_PP_ARRAY_ELEM` can't be included in vs2017 using `/experimental:preprocessor` option [Open]

> Username: gnaggnoyil  
> Created at: 2019-11-11 05:50:17 UTC  
> Updated at: 2020-05-27 09:57:12 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25  

The following code does not compile under VS2017 using `/experimental:preprocessor` option:  
  
```cpp  
#include <iostream>  
#include <boost/preprocessor.hpp>  
  
#define FOO (1,(<type_traits>))  
#define BAR BOOST_PP_ARRAY_ELEM(0, FOO)  
  
#define HOGE_1(x) x  
#define HOGE_2(x) HOGE_1(x)  
#define HOGE() HOGE_2(BAR)  
#define PUYO_1(x) #x  
#define PUYO_2(x) PUYO_1(x)  
#define PUYO() PUYO_2(BAR)  
  
#include BAR  //C2006  
#include HOGE()  //C2006  
  
int main(){  
    std::cout << PUYO() << std::endl;  
    return 0;  
}  
```  
Both `#include BAR` and `#include HOGE()` directives result in C2006 error, yet, `PUYO` macro could successfully get the string literal formed by `BAR`.  
Switching to vs2019 eliminates this issue.  
  
This issue breaks the usage of `<boost/preprocessor/iteration/detail/iter/forward1.hpp>` in `<boost/function.hpp>

---

## Comment 1

> Username: eldiener  
> Created at: 2019-11-11 13:09:57 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-552439089  

The VC++ experimental processor has bugs even in VS2019, but more obviously in VS2017. See my reports to Microsoft at:  
  
https://developercommunity.visualstudio.com/content/problem/364698/standard-conforming-preprocessor-invalid-warning-c.html  
  
and  
  
https://developercommunity.visualstudio.com/content/problem/362391/preprocessor-warning-should-be-an-error.html  
  
But thanks for your example because I will add it to the Boost PP tests and add this as a bug report to Microsoft regarding VS2017.  
  
I can not fix this for you in Boost PP because VC+ with the /experimental:preprocessor option is treated as a fully conforming C++ standard conforming preprocessor. This is as opposed to VC++ with its default preprocessor, which is not a C++ standard conforming preprocessor in many respects and for which there are numerous workarounds in Boost PP that have been made to get it to work.

---

## Comment 2

> Username: eldiener  
> Created at: 2019-11-12 09:54:51 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-552819714  

You might also want to see   
  
https://developercommunity.visualstudio.com/content/problem/364688/standard-conforming-preprocessor-failing-boost-pre.html.   
  
I reported this bug and it was fixed, but only for the latest rel;ease of VC++ in VS2019.

---

## Comment 3

> Username: gnaggnoyil  
> Created at: 2019-11-14 03:17:50 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-553705529  

Thanks for your reply here! Yes it's a VS2017 bug, but I thought there could still be workarounds that boost can do, as there has already been workarounds for many other corner cases in boost. Now since there won't be any fixes in boost regarding this issue, what about just emitting an error for VS2017 with `/experimental:preprocessor` on during configuration time and/or in cmake package config files? This could at least make less confusion for users as users will soon realized such building options is not supported.

---

## Comment 4

> Username: eldiener  
> Created at: 2020-05-20 21:38:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-631741229  

The current VS2019 with the new experimental preprocessor can work with all functionality in Boost PP without errors. I suggest you switch to VS2019 and you will have no problems with Boost PP. You should now use the "/Zc:preprocessor" switch to invoke the preprocessor for VS2019.

---

## Comment 5

> Username: gnaggnoyil  
> Created at: 2020-05-25 08:02:43 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-633438220  

I have already switched as you have suggested. Could there be changes in documents so that this issue can be mentioned?

---

## Comment 6

> Username: eldiener  
> Created at: 2020-05-25 13:07:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-633563730  

What would you want mentioned in the documentation ?

---

## Comment 7

> Username: gnaggnoyil  
> Created at: 2020-05-27 09:56:20 UTC  
> Updated at: 2020-05-27 09:57:12 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/25#issuecomment-634556336  

Probably some "warning"s in instrutions for building under msvc, indicating that `/experimental:preprocessor` of VS2017 is not supported? Or better, some code in generated CMake package config file that checks if this flag is enabled in VS2017 and emit an error if it is? Not sure if it's easy to do this in current code base though.
