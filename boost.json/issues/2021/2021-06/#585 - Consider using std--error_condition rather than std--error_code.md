# #585 - Consider using std::error_condition rather than std::error_code [Closed]

> Username: accelerated  
> Created at: 2021-06-20 14:48:11 UTC  
> Updated at: 2021-06-21 14:52:13 UTC  
> Closed at: 2021-06-21 14:52:13 UTC  
> Url: https://github.com/boostorg/json/issues/585  

`std::error_code` is not platform independent. Since this is a generic json library, I don't see a particular reason for not using platform-independent `std::error_condition`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-20 17:24:49 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864586320  

Beast and Asio use `error_code`. Maybe `error_condition` is more correct (@pdimov ?), maybe not - but if Boost.JSON uses a different type then the user experience would be impacted.

---

## Comment 2

> Username: accelerated  
> Created at: 2021-06-20 17:31:46 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864587065  

@vinniefalco I think running as JSON_STANDALONE gives you the proper conversions...currently looking into it, as there are too many type conversion friction points. If Asio uses error_code then perhaps it's better to stick to that. It's just a suggestion. The whole error_code vs error_condition is quite confusing for everyone, myself included. Supposedly error_condition is meant to categorize the platform-specific error_codes.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-20 17:44:15 UTC  
> Updated at: 2021-06-20 17:44:52 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864588567  

> The whole error_code vs error_condition is quite confusing for everyone  
  
Confusing for me as well.  
  
Can you find any examples of open source libraries (perhaps on GitHub) which use `error_condition&` in their public interfaces to return errors?  
  
Note that the standard library itself uses `error_code`, e.g.:  
  
https://en.cppreference.com/w/cpp/filesystem/directory_iterator/increment

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-06-20 18:25:38 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864593555  

https://eel.is/c++draft/syserr.errcondition#overview-1  
> The class error_­condition describes an object used to hold values identifying error conditions.  
> [Note 1: error_­condition values are portable abstractions, while error_­code values ([syserr.errcode]) are implementation specific. — end note]  
  
There's nothing in the standard about platform dependence, only about specificity. Apparently, cppreference says `error_condition` is about platform-independence. Well, it's not entirely correct.  
  
Our `error_code`s describe specific situations which prevent the functions to produce a result. Our `error_condition`s describe higher level errors: parsing error or  conversion error.  
  
Interesting reading:  
http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-5.html  
https://akrzemi1.wordpress.com/2017/07/12/your-own-error-code/

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-06-20 18:31:23 UTC  
> Updated at: 2021-06-20 18:31:40 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864594260  

I guess, the simplest approach to describing the difference is this: libraries (including the standard library) give users `error_code`s. Users can disregard specificity and convert those to `error_condition`s that group similar error codes coming from a library, or coming from several similar libraries (useful in software that deals with devices or services from different vendors), or coming from different implementations of the same library (e.g. the standard library on different OSes).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-06-20 19:14:55 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864599349  

Boost.JSON does use the groupings, and comparisons work:  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__condition.html

---

## Comment 7

> Username: pdimov  
> Created at: 2021-06-20 22:35:21 UTC  
> Url: https://github.com/boostorg/json/issues/585#issuecomment-864620679  

> Maybe `error_condition` is more correct  
  
No, it isn't. Functions should never return errors using `error_condition`, only with `error_code`. `error_condition` is _only_ for comparing codes to.
