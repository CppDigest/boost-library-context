# #110 - [Q] What does `time_zone::global` do exactly? [Closed]

> Username: Flamefire  
> Created at: 2022-09-12 15:32:19 UTC  
> Updated at: 2022-09-15 12:16:27 UTC  
> Closed at: 2022-09-15 10:44:24 UTC  
> Url: https://github.com/boostorg/locale/issues/110  

While trying to fix/improve unittests I came across the `date_time` class and the `time_zone::global()` function.  
  
I can't really figure out what they are supposed to do.  
  
@artyom-beilis Can you explain what the observable effect of the `time_zone::global` call below should be?  
  
```  
                time_zone::global("GMT");  
                date_time tp_1;  
                time_zone::global("GMT+01:00");  
                date_time tp_2;  
```  
  
I see the default ctor uses the global TZ so I would expect that e.g. `tp_1 != tp_2` but that doesn't seem to be the case. In fact I fail to find any influence of the global TZ on anything observable of the `date_time` instances.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-09-13 03:05:53 UTC  
> Url: https://github.com/boostorg/locale/issues/110#issuecomment-1244837337  

`time_zone` defined how to manipulate with different period values. Comparison operators relate to time as absolute time point - Unix time. See: https://www.boost.org/doc/libs/1_79_0/libs/locale/doc/html/classboost_1_1locale_1_1date__time.html#a76c5b0bbc01bd561c25cb83f26e5f535  
  
So ignoring chances that clock second changed between two `date_time` constructors the universal time point is same;  
  
```  
#include <boost/locale.hpp>  
  
int main()  
{  
    using namespace boost::locale;  
    generator gen;  
    std::locale::global(gen("en_US.UTF-8"));  
    time_zone::global("GMT");  
    date_time tp_1;  
    time_zone::global("GMT+01:00");  
    date_time tp_2;  
    std::cout << tp_1.get(period::hour()) <<" " << tp_2.get(period::hour()) << " same time=" << (tp_1 == tp_2)<< std::endl;  
}  
```  
  
Would print something like  
  
    3 4 same time = 1  
  
Because they refer to same "universal time"

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-09-15 10:44:24 UTC  
> Updated at: 2022-09-15 10:44:36 UTC  
> Url: https://github.com/boostorg/locale/issues/110#issuecomment-1247924376  

Thanks, added a test for that in #112: https://github.com/boostorg/locale/pull/112/commits/ee67d090c0fe2eced0041ddc693b0b032a9d3a46

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2022-09-15 11:23:09 UTC  
> Url: https://github.com/boostorg/locale/issues/110#issuecomment-1247967466  

`TEST(tp_gmt == tp_gmt1);`  
  
Actually there is a small chance of failing of `tp_gmt` and `tp_gmt1` are created in different seconds. Basically if the clock accuracy is around 10um than once in 100,000 cases it will fail

---

## Comment 4

> Username: Flamefire  
> Created at: 2022-09-15 12:16:27 UTC  
> Url: https://github.com/boostorg/locale/issues/110#issuecomment-1248022505  

True... Fixing in https://github.com/boostorg/locale/tree/fix/timepoint_test
