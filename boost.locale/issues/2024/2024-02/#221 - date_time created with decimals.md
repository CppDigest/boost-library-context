# #221 - date_time created with decimals [Closed]

> Username: itprofn  
> Created at: 2024-02-14 04:59:26 UTC  
> Updated at: 2024-02-21 07:38:02 UTC  
> Closed at: 2024-02-21 07:38:02 UTC  
> Url: https://github.com/boostorg/locale/issues/221  

I've updated to fedora 39 from 38, so the boost library is now 1.81.0 and errors start happening.  
when created a new date_time var0 = period::year(2024) + period::month(0) + period::day(1) + period::hour(0) + period::minute(0) + period::second(0)  
you can discover the var0.time() has decimals.  and the decimal changes every other run of the program.  
This seems the subseconds was set from the current time but there is no way the library can set it. say period::microsec or so.  
  
This also create inaccuracy on difference() and  (var1 - var0) / period::second()  
  
I use temporary workaround as:  
- floor(var0.time())  
- var0.time(floor(var0.time()) at the variable initialization such that the subsecond can be set zero.  
  
```  
#include <iostream>  
#include <string>  
#include <boost/locale.hpp>  
#include <cmath> //floor  
#include <iomanip>  //setprecision  
#include <thread>  
  
namespace bl = boost::locale;  
namespace blp = bl::period;  
namespace blas = bl::as;  
typedef bl::date_time bldt;  
  
// g++ -lboost_locale -o blptime blptime.cpp  
  
int main() {  
    bl::localization_backend_manager my = boost::locale::localization_backend_manager::global();  
    my.select("icu");  
  
    bl::generator gen0(my);  
    bl::localization_backend_manager::global(my);  
    bl::generator gen;  
  
    //std::locale loc = gen.generate(""); // Use the default system locale  
    std::locale loc = gen.generate("en_US.UTF8");  
    std::locale::global(loc);  
  
    std::cout.imbue(loc);  
  
    bldt var0 = blp::year(2024) + blp::month(0) + blp::day(1) + blp::hour(0) + blp::minute(0) + blp::second(0);  
    std::cout << blas::datetime << var0 << std::endl;  
    std::cout << std::setprecision(15);  
    std::cout << blas::posix << var0.time() << std::endl;  
    std::cout << blas::posix << floor(var0.time()) << std::endl;  
    std::cout << blas::posix << (long) var0.time() << std::endl;  
  
    bldt now;  
    bldt tomorrow = now + boost::locale::period::day(1); //this will likely get same subsecond  
    std::this_thread::sleep_for(std::chrono::milliseconds(500));  //delay a bit to get difference subsecond  
    bldt tomorrow1 = blp::year(2024) + blp::month(1) + blp::day(15) + blp::hour(0) + blp::minute(0) + blp::second(0);  
  
    std::cout << blas::datetime << now << std::endl;  
    std::cout << blas::datetime << tomorrow << std::endl;  
    std::cout << blas::datetime << tomorrow1 << std::endl;  
    std::cout << blas::posix << now.time() << std::endl;  
    std::cout << blas::posix << tomorrow.time() << std::endl;  
    std::cout << blas::posix << tomorrow1.time() << std::endl;  
  
    std::cout << blas::posix << tomorrow.difference(now, blp::second()) << std::endl;  
    std::cout << blas::posix << tomorrow.time() - now.time() << std::endl;  
    std::cout << blas::posix << tomorrow1.difference(now, blp::second()) << std::endl;  
    std::cout << blas::posix << tomorrow1.time() - now.time() << std::endl;  
}  
  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-02-14 18:48:26 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1944403471  

Your observation is right that Boost 1.81 fixed the behavior of `date_time` with d10de509ad9480e49541e975b0c07ff45ba0cf29 and now correctly handles sub-seconds while before a bug in the code always truncated them. This is directly observable as `date_time::time` now returns split seconds in the decimals as (likely) intended and reasonably expected given the type is `double`.  
  
Most notably the code before had a bug which is clearly visible on inspection:   
```  
res.nanoseconds = static_cast<uint32_t>((rtime - secs) / 1e9);  
if(res.nanoseconds > 999999999)  
...  
other_time = o_time.seconds * 1000.0 + o_time.nanoseconds / 1000000.0;  
```  
  
I.e. there is a division which should have been a multiplication.  
  
> errors start happening.  
  
What actual errors? Or did you just refer to the changed behavior?  
  
>  This seems the subseconds was set from the current time but there is no way the library can set it. say period::microsec or so.  
  
This is true: `date_time` offers no way to set those nanoseconds similar to the other fields. However the calendar class/implementation `abstract_calendar` (that can e.g. be accessed via the facet) which implements the time operations uses `posix_time` in the interface and that explicitly has that nanoseconds field.     
However I do fail to see the reason behind that. Maybe @artyom-beilis remembers?  
  
I also agree that the "fixed" behavior is odd as it only applies to the ICU backend and all other backends set the nanoseconds directly to zero: https://github.com/boostorg/locale/blob/299381bd770e6e40d4cbac3fdfcebdd7d042545d/src/boost/locale/util/gregorian.cpp#L477  
  
> This also create inaccuracy on difference() and (var1 - var0) / period::second()  
  
I don't understand this. As the difference caused by the change is always less than a second it won't be noticeable in `second` resolution. This can be shown using the largest possible difference:  
  
```  
        var0.time(floor(var0.time()));  
        var1.time(std::nextafter(var0.time() + 1, var0.time()));  
        // Both output 0  
        std::cout << blas::posix << var0.difference(var1, blp::second()) << std::endl;  
        std::cout << blas::posix << (var1 - var0) / blp::second() << std::endl;  
```  
  
  
Still to avoid any confusion I'd actually opt for a (breaking!) change modifying `time()` to take/return `int64_t` and similarly remove `posix_time` and replace all its uses by `int64_t`, i.e. storing only seconds.     
This has the only disadvantage that the range will be limited to the year "5838270" ;-)  
  
But then: Why introduce a breaking change if the only current issue is, that `date_time::time` now shows decimals which is actually good as it increases the resolution in which you can distinguish 2 `date_time` instances.     
So what is the actual issue?

---

## Comment 2

> Username: itprofn  
> Created at: 2024-02-16 14:06:36 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1948447781  

Thanks for your explanation that I can understand more on the change.  
  
1. 'errors' refer old code that subseconds is zero, but the update make it nonzero.  I have to implement floor(timevar) as workaround.  
2. On the difference problem, when you add 1second from one var, then sure the difference result is 1.  How about two system times.  They have difference millisecond and their difference will have various miniseconds values on every run.  Thus there can be results with 1 second difference.  See the code example   
3. As there is no way to set the subseconds, when I set a time, the subsecond is random and has to usecase meaning.  Thus for add and subtract operations, we have to trim the subseconds to make the result correct and the precision is then back to second.  It seems no benefits only system time having nanoseconds when client applications have to settle to second precision.   
4. It is sure good to have subseconds support as std::chrono::zoned_time.  There has to be possible to set the values.

---

## Comment 3

> Username: Flamefire  
> Created at: 2024-02-16 15:29:42 UTC  
> Updated at: 2024-02-16 15:30:04 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1948623113  

> 1. 'errors' refer old code that subseconds is zero, but the update make it nonzero.  I have to implement floor(timevar) as workaround.  
  
Yes this is a difference. But I don't understand why it has to be an integer seconds. I.e. what error the fractional part actually causes.  
  
> 2. On the difference problem, when you add 1second from one var, then sure the difference result is 1.  How about two system times.  They have difference millisecond and their difference will have various miniseconds values on every run.  Thus there can be results with 1 second difference.  See the code example  
  
If you add 1 second to a timepoint than the difference is exactly 1. If you create time_points at different times than the difference between them in seconds will be the same independent of their fractional part/milliseconds. So I don't understand what you are referring to:  
- `(var1 - var0) / period::second()` will always be the same as `var0.time(floor(var0.time())); var1.time(floor(var1.time())); (var1 - var0) / period::second()` or am I missing anything?  
- What am I supposed to see in your code example? Did you make a mistake with `tomorrow1` & `tomorrow` because they don't seem to be related at all. What is the expected output of that code and what do you see instead? Except of course for `var0.time()` etc. having a fractional part which shouldn't be unexpected given it is defined as a `double`  
  
> 3. As there is no way to set the subseconds, when I set a time, the subsecond is random and has to usecase meaning.  Thus for add and subtract operations, we have to trim the subseconds to make the result correct and the precision is then back to second.  It seems no benefits only system time having nanoseconds when client applications have to settle to second precision.  
  
You are right that the subseconds are only settable via `.time(...)`. However they are also only observable via `.time()`. So I don't understand why you "have to trim the subseconds to make the result correct". In which case is it not correct? Can you provide a short code example with expected and actual output? Also what do you mean by "add operations"? You cannot add 2 `date_time` instances, can you?  
  
> 4. It is sure good to have subseconds support as std::chrono::zoned_time.  There has to be possible to set the values.  
  
A `nanoseconds` period similar to `seconds` can surely be added. The question is just how useful it is if that isn't observable outside of `.time()`. Of course it would allow stuff like `time0 + nanoseconds(500) + nanoseconds(500)` but might lead to surprising results such as adding `nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) + nanoseconds(100) ` might not be exactly the same as `nanoseconds(100) * 10` due to float inaccuracies.

---

## Comment 4

> Username: itprofn  
> Created at: 2024-02-17 06:10:06 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1949753327  

thanks for your patience to understand  
1. my usecase is to save some stock prices with the end of day session time, say 4:00pm.  I set the time with period::hour(4) + period::minute(0) + period::second(0).  But the update make this time different every day with various subsecond values.  Thus I have to trim the subseconds to make the time exact 4:00pam, not 4:0:0.xxx  
2. when creating two time_points at different times, the difference is seoncds DEPENDS on the fractional part. say var1 is .9 and var2 is .1, the diffence will be .8.  If var1 is .1 and var2 is .9, the differenc is -0.8.  Using floor or round will cause 1 second difference unterdeterminated  
3. just my way to describte, it is the same problem on difference.  The need to trim subseconds is in point 1  
4. the problem on float inaccuracies are known as well for other libraries.  It seems the usual way is to multiply it by 1e9 to use integer calculation.  It should have no conflicts to add the period::subsecond as there is usecase need to set a meaningful subseconds rather than random values that we have to trim anyway to get a constant time_point  
  
hope the above better explain my thought

---

## Comment 5

> Username: Flamefire  
> Created at: 2024-02-17 20:01:31 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1950295923  

Sorry I still am missing something:  
  
>     1. my usecase is to save some stock prices with the end of day session time, say 4:00pm.  I set the time with period::hour(4) + period::minute(0) + period::second(0).  But the update make this time different every day with various subsecond values.  Thus I have to trim the subseconds to make the time exact 4:0:0pam, not 4:0:0.xxx  
  
But the time as printed and shown for all purposes of Boost.Locale is 4:0:0 as set. The subseconds are not observable outside of `time()` are they? So where exactly do they cause an issue?  
  
>     2. when creating two time_points at different times, the difference is seoncds DEPENDS on the fractional part. say var1 is .9 and var2 is .1, the diffence will be .8.  If var1 is .1 and var2 is .9, the differenc is -0.8.  Using floor or round will cause 1 second difference unterdeterminated  
  
Again: In the context of Boost.Locale I don't see this. I adjusted your code a bit to try it just now:  
```  
        bldt mydate = blp::year(2024) + blp::month(0) + blp::day(1) + blp::hour(0) + blp::minute(0) + blp::second(0);  
        const double floorTime = floor(mydate.time());  
        bldt var0, var1;  
        var0.time(floorTime + 0.1);  
        var1.time(floorTime + 0.9);  
  
        std::cout << (var0.time() - var1.time()) << std::endl; // -0.8  
        std::cout << (var1.time() - var0.time()) << std::endl; // 0.8  
        std::cout << (var0 - var1) / blp::second() << std::endl; // 0  
        std::cout << (var1 - var0) / blp::second() << std::endl; // 0  
```  
  
So when using Boost.Locale to calculate the difference in seconds you get exactly zero as expected and as before. And using the `time() - time()` also gives you the difference in seconds as a double. So if you wanted the difference in whole seconds you can you `floor()` on the result.  
  
>     3. just my way to describte, it is the same problem on difference.  The need to trim subseconds is in point 1  
  
But what exactly do you do that you run into the problem? As shown using `.difference` and similar doesn't show any different behavior.  
  
>     4. the problem on float inaccuracies are known as well for other libraries.  It seems the usual way is to multiply it by 1e9 to use integer calculation.  
  
Yes, that would mean storing the value internally as nanoseconds. However e.g. ICU, i.e. the backend you are using, stores it as a double so there is no way to change that.  
  
> It should have no conflicts to add the period::subsecond as there is usecase need to set a meaningful subseconds rather than random values that we have to trim anyway to get a constant time_point  
  
Yes that would be possible but as mentioned it may lead to another kind of unexpected behavior as the value won't be as set in all cases while currently an addition of 1 second n times leads to a difference of n seconds.  
  
So I'm rather inclined to never set subseconds by default. I.e. the default ctor will return integer seconds and subseconds are only supported through custom calendars or `time(5.75)`

---

## Comment 6

> Username: itprofn  
> Created at: 2024-02-18 05:41:23 UTC  
> Updated at: 2024-02-18 17:01:28 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1950969544  

1. Sorry for confusion.  The case is as below:  
var0.time(floorTime + 0.9);  
var1.time(floorTime + 1.2);  
std::cout << (var0.time() - var1.time()) << std::endl; // -0.3  
std::cout << (var1.time() - var0.time()) << std::endl; // 0.3  
std::cout << (var0 - var1) / blp::second() << std::endl; // 0   should be -1  
std::cout << (var1 - var0) / blp::second() << std::endl; // 0   should be +1  
std::cout << var0.difference(var1, blp::second()) << std::endl; // 0   should be -1  
std::cout << var1.difference(var0, blp::second()) << std::endl; // 0   should be +1  
std::cout << (floor(var0.time()) - floor(var1.time())) << std::endl;  //-1  workaround correct  
std::cout << (floor(var1.time()) - floor(var0.time())) << std::endl;  //1   workaround correct  
  
since difference internal will just subtract the values (with subseconds), error occurs.  workaround is to trim (floor) to seconds before calling difference.  
Thus boost::library internals may not have adapted to handle subseconds.  difference is one case, not sure any others exist.  
  
2. My use case is to save a stock price at a time (e.g. 2014-2-1 4:0:0) in database and retrieve given the time.  
I get the timestamp by setting var0 = (period::year(2014) + ... period::second(0)).time() <= get the timestamp to save at database  
then retrieve with save setting.  But the update make the save and retrieve at the date with difference decimals.  I have to trim the subseconds.  
  
In summary, the recent update only benefits get now() with subseconds if it is not handling it. But in some use cases as mine I need to trim the subseconds.  As long as the library remain precision in seconds and users may not have intention or benefit to get current time with subseconds.  
  
The library internals like 'difference' may not handle subseconds right.  To be safe, all vars to have be trimmed when create and  before calling any library function which should be correct with ZERO subseconds.  
  
If it is believed good to save the subsecond value, it seems having a way to set the subseconds other than with a random system subseconds should work.  Same behavior as before, if set period::nanosecond(something), it is set.  Otherwise get the system portion.  Then difference is correct.  And may also be other internal functions of the library.  The fractional part should have no impacts as long as the value can be set.  
  
I am not sure if it need huge effort to enhance.  Just for your consideration.  
- add period::micro/nanoseconds without users to use workaround time(x.yyy), including  
   (var1 - var0) / period::micro/nanoseconds()  
   var0.difference(var1, period::micro/nanoseconds())  
  
The library features will just as it is now and extended to support subseconds with ICU backend..  ICU itself should have tackle operations on subseconds if it supports it.  
Regards

---

## Comment 7

> Username: Flamefire  
> Created at: 2024-02-18 11:00:00 UTC  
> Updated at: 2024-02-20 11:11:47 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1951150714  

I see now, thank you! More specifically it seems that:  
```  
var0.time(floorTime + 0.9);  
var1.time(floorTime + 0.2);  
std::cout << (var1 - var0) / blp::second() << std::endl; // 0 as before  
var1 += blp::second(1);  
std::cout << (var1 - var0) / blp::second() << std::endl; // 0 but should be 1  
```  
  
Note however that this is at least consistent with how e.g. days vs hours are handled. But at least there you can set both.  
  
TBH I don't really see a value in having the subseconds anymore especially given the confusing semantic as above where the subseconds lead to unexpected second-differences when not handled explicitely. Especially as the fact that the subseconds were always zero went unreported for all that time.  
  
Not only that: All other backends store the time as `std::time_t`, i.e. integer seconds. So you'd have different behavior depending on the backend used. Hence I'd rather remove the "feature" of having subseconds (again) than having more confusion pop up later on.  
  
This means that `var0.time(floorTime + 0.9) == var0.time(floorTime + 0.2) == var0.time(floorTime);` i.e. the "floor" behavior you are already using.

---

## Comment 8

> Username: itprofn  
> Created at: 2024-02-18 12:41:35 UTC  
> Url: https://github.com/boostorg/locale/issues/221#issuecomment-1951314637  

good.  It is rather to give back time_t.  Some of the unittests may also have assumptions on integers so similar problems may just pass.  More work may need to enable subseconds.  
  
anyhow I would like to thank you for maintenance of the library.  It is crucial for c++ programs to deal with locale issues.
