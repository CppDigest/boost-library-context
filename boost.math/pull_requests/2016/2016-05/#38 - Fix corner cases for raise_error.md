# #38 Fix corner cases for raise_error [Merged]

> Username: kundor  
> Created at: 2016-05-05 22:46:29 UTC  
> Updated at: 2016-05-10 08:57:37 UTC  
> Merged at: 2016-05-08 15:54:43 UTC  
> Closed at: 2016-05-08 15:54:43 UTC  
> Url: https://github.com/boostorg/math/pull/38  

This addresses three issues in `raise_error(const char* function, const char* message, const T& val)`.  
  
First, when BOOST_NO_RTTI is defined, the code was replacing the `%1%` in both the `function` string and the `message` string with the value of `val`, with nonsensical results. Now it formats `message` separately before appending it to the string.  
  
Second, when either `function` or `message` did not contain `%1%`, `boost::format` was throwing a `boost::too_many_args` exception (instead of the exception we are trying to throw!)  
The [documentation](http://www.boost.org/doc/libs/1_60_0/libs/math/doc/html/math_toolkit/pol_ref/error_handling_policies.html#math_toolkit.pol_ref.error_handling_policies.h4) suggests this should work:  
  
> Note that the strings _function_ and _message_ may contain "%1%" format  
> specifiers designed to be used in conjunction with Boost.Format. If  
> these strings are to be presented to the program's end-user then the  
> "%1%" format specifier should be replaced with the name of type T in  
> the _function_ string, and if there is a %1% specifier in the _message_  
> string then it should be replaced with the value of _val_.  
  
Third, whenever the type `T` did not have a `std::numeric_limits` specialization, there was a compile-time failure because of a static assertion of `::std::numeric_limits<T>::is_specialized` in `tools::digits` (in tools/precision.hpp.)  
  
Note that in `policies::digits()` (in policies/policy.hpp) there seems to be an attempt to avoid this, by delegating to one function when `std::numeric_limits<T>::is_specialized` is `true` and calling `tools::digits` when it is `false`, but then `tools::digits` asserts the opposite.  
  
Instead, this proposal just avoids setting the precision at all when `T` has no `std::numeric_limits` specialization.  
  
This allows, e.g., to call `raise_domain_error` for a function dealing with `tools::polynomial<T>`.

---

## Comment 1

> Username: pabristow  
> Created_at: 2016-05-07 13:58:29 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-217637322  

I ahven't had a chance to check this out in detail yet (and I'm sure John is busy with the lat stages of the Bost release), but it all looks very plausible.  As we have discovered, providing good error messages for any built-in or user-defined type is not easy as it might appear - until you try it.  But at least John rose to my challenge to do better than many packages.  Testing is also tricky because of the myriad of possible combinations, and testing error reporting is even worse.  Thanks for looking closely at this.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-05-07 16:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-217651613  

On 07/05/2016 14:58, Paul A. Bristow wrote:  
  
> I ahven't had a chance to check this out in detail yet (and I'm sure   
> John is busy with the lat stages of the Bost release), but it all   
> looks very plausible. As we have discovered, providing good error   
> messages for any built-in or user-defined type is not easy as it might   
> appear - until you try it. But at least John rose to my challenge to   
> do better than many packages. Testing is also tricky because of the   
> myriad of possible combinations, and testing error reporting is even   
> worse. Thanks for looking closely at this.  
  
I have the tests running as we speak... but I had to improve our test   
case first.  
  
BTW, it's been on my todo list for a while to remove all uses of   
Boost.Format from this: we don't strictly need it for what we're doing.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-05-08 17:28:53 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-217735022  

Looking at this again, it's not quite the whole answer (or right answer), the intention was that whenever the library is used with a type with no numeric_limits support, was that it would specialize a small number of functions, tools::digits being one of them.  Unfortunately, because you can't partially specialize a function, we can't do that for a generic polynomial<T>  :(

---

## Comment 4

> Username: pabristow  
> Created_at: 2016-05-09 17:44:03 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-217935503  

If you are redesigning the error reporting mechanism...  
  
I vaugely remember wanting to provide more values in an error report than were available as standard and starting to write a new set a macro before realising the explosion of variants that would result.  Is suspect C++11 and up would make this easier, but is not retro-portable.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-05-09 18:01:48 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-217940562  

On 09/05/2016 18:44, Paul A. Bristow wrote:  
  
> If you are redesigning the error reporting mechanism...  
>   
> I vaugely remember wanting to provide more values in an error report   
> than were available as standard and starting to write a new set a   
> macro before realising the explosion of variants that would result. Is   
> suspect C++11 and up would make this easier, but is not retro-portable.  
  
I really don't want to redesign things at this juncture, I have no time   
for that for one thing.  For now I've just removed all uses of   
Boost.Format since we were only making cursory use of it, and glossed   
over the issue...

---

## Comment 6

> Username: pabristow  
> Created_at: 2016-05-10 08:57:37 UTC  
> Url: https://github.com/boostorg/math/pull/38#issuecomment-218098368  

I'm sure it is not a priority on your time.  I concluded it was outside my skill set :-(

---
