# #120 - Compilation failure while building CppRestSDK [Closed]

> Username: NAThompson  
> Created at: 2020-01-02 15:24:20 UTC  
> Updated at: 2020-01-04 01:28:37 UTC  
> Closed at: 2020-01-04 01:28:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/120  

While building CppRestSDK from commit 96e7d20e398b629de2935f9ac32cfa2780cd0b0b, the build fails due to:  
  
```  
                 from /cpprestsdk/Release/src/http/client/http_client.cpp:16:  
/boost/boost/date_time/wrapping_int.hpp: In instantiation of ‘IntT boost::date_time::wrapping_int2<int_type_, wrap_min, wrap_max>::calculate_wrap(IntT) [with IntT = int; int_type_ = short int; int_type_ wrap_min = 1; int_type_ wrap_max = 12]’:  
/boost/boost/date_time/wrapping_int.hpp:126:26:   required from ‘IntT boost::date_time::wrapping_int2<int_type_, wrap_min, wrap_max>::add(IntT) [with IntT = int; int_type_ = short int; int_type_ wrap_min = 1; int_type_ wrap_max = 12]’  
/boost/boost/date_time/adjust_functors.hpp:71:118:   required from ‘boost::date_time::month_functor<date_type>::duration_type boost::date_time::month_functor<date_type>::get_offset(const date_type&) const [with date_type = boost::gregorian::date; boost::date_time::month_functor<date_type>::duration_type = boost::gregorian::date_duration]’  
/boost/boost/date_time/date_duration_types.hpp:67:16:   required from ‘boost::date_time::months_duration<base_config>::duration_type boost::date_time::months_duration<base_config>::get_offset(const date_type&) const [with base_config = boost::gregorian::greg_durations_config; boost::date_time::months_duration<base_config>::duration_type = boost::gregorian::date_duration; boost::date_time::months_duration<base_config>::date_type = boost::gregorian::date]’  
/boost/boost/date_time/posix_time/date_duration_operators.hpp:33:37:   required from here  
/boost/boost/date_time/wrapping_int.hpp:151:14: error: conversion to ‘boost::date_time::wrapping_int2<short int, 1, 12>::int_type {aka short int}’ from ‘int’ may alter its value [-Werror=conversion]  
       value_ -= (wrap_max - wrap_min + 1);  
       ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/boost/boost/date_time/wrapping_int.hpp:156:14: error: conversion to ‘boost::date_time::wrapping_int2<short int, 1, 12>::int_type {aka short int}’ from ‘int’ may alter its value [-Werror=conversion]  
       value_ += (wrap_max - wrap_min + 1);  
       ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
```  
  
The `date_time` commit is  061aec856d4fdb2b53cc17cfa565b4e1c923b62a, though the issue remains active in as many commits as I can find . . .

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-01-03 22:21:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570715850  

Hi @NAThompson -- which compiler version and platform is this?  This change in the commit was in boost 1.72

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-01-03 22:22:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570716110  

```  
 g++ --version  
g++ (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0  
```

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-01-03 22:41:26 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570720010  

ok thanks -- not really familiar with cppRestSDK -- any inclusion of date-time must be upstream since it doesn't appear directly in the first few includes I looked at  The closest platform in the testing looks like teeks99-04-mg7-1z-Ubuntu-64onarm64  which seems to be passing all the tests.  Which might mean that something is happening in cppRestSDK that isn't in the test.  Any chance you could narrow down where in cppRestSDK the dependency is?  
  
https://www.boost.org/development/tests/master/developer/date_time.html

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-01-03 23:18:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570726833  

The problem is that the `cppRestSDK` uses `-Wall -Wextra -Werror` by default; this is what is breaking the build.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2020-01-04 00:13:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570736286  

Ok sure -- and the complaint is that we're narrowing from an int to a short int.  And I suspect that's for greg_month given that 1..12 range.  But that code hasn't change for eons -- so maybe it's a change on the cppRestSDK options?  About the only thing that can be done is to static_cast and actually that's already been done to silence warnings:  
  
```  
  greg_month_rep(static_cast<greg_month_rep::value_type>(theMonth)) {}  
  greg_month(value_type theMonth) : greg_month_rep(theMonth) {}  
```  
So now I'm really not sure what the root of the issue is...

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-01-04 01:28:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/120#issuecomment-570744957  

Yeah I think this is probably more an issue with CppRestSDK being very picky; perhaps it should go to them.
