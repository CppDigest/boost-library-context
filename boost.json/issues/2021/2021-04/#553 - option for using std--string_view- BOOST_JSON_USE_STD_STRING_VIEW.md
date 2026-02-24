# #553 - option for using std::string_view: BOOST_JSON_USE_STD_STRING_VIEW [Closed]

> Username: rbruenig  
> Created at: 2021-04-30 12:14:58 UTC  
> Updated at: 2024-04-08 17:02:24 UTC  
> Closed at: 2024-04-08 17:02:24 UTC  
> Url: https://github.com/boostorg/json/issues/553  

In Boost.Beast users can define `BOOST_BEAST_USE_STD_STRING_VIEW` to choose to use `std::string_view` instead of `boost::string_view` for interacting with beast (added [here](https://github.com/boostorg/beast/issues/1133)). It would be great if Boost.JSON had a similar option `BOOST_JSON_USE_STD_STRING_VIEW` that works in the same way.   
  
This would make compatibility between libraries like Boost.JSON, Boost.Beast and other libraries like fmtlib (soon to be std::format when compilers implement it) easier. In my specific case I had an application that was already using Boost.Beast with `BOOST_BEAST_USE_STD_STRING_VIEW` defined and fmtlib and everything was working perfectly. I added Boost.JSON and needed to turn off `BOOST_BEAST_USE_STD_STRING_VIEW` and add a fmtlib formatter for boost::string_view to make everything work together. Having the option to have everything work with `std::string_view` will make use cases like this easier and also ease future cross compatibility between libraries.  
  
There currently already is an option `BOOST_JSON_STANDALONE` that switches the string_view implementation to `std::string_view` (see [string_view.hpp](https://github.com/boostorg/json/blob/develop/include/boost/json/string_view.hpp)). However `BOOST_JSON_STANDALONE` entails other changes as well and it would be useful to be able to choose `std::string_view` while using other parts of boost (like Boost.Beast ;) ). It looks to me like the needed changes for this would be limited to the that file.   
  
If this feature request is deemed sensible, I can prepare a pull-request.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-05-01 00:41:45 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-830475513  

I don't like the macro, because it effectively creates a different library. We are pursuing an alternative solution that will hopefully achieve the same result.

---

## Comment 2

> Username: cjwijtmans  
> Created at: 2021-09-07 11:46:08 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-914234348  

any progress on this? i solved it by using `{string_view.data(), string_view.length()}` for now though.

---

## Comment 3

> Username: kamrann  
> Created at: 2022-08-18 09:49:03 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219283928  

> We are pursuing an alternative solution that will hopefully achieve the same result.  
  
Did something happen on this front? I've read through a number of issues but not sure what you were referring to here.  
Attempting to switch from `nlohmann`, the biggest usability issue I'm facing is without doubt having to mess around to get a `std::string_view` into `boost.json`. Though I do note the irony that the main factor in me wanting to dump `nlohmann` is its propensity for implicit conversions!  
  
I can understand the difficulties here in relation to trade-offs with `std` vs `boost` and standalone deprecation, but I want to add one note that may not have been immediately obvious - providing its own vocab types for everything results in substantially increased compile times when using the library in a project that relies heavily on `std` and in a limited way on `boost`. A huge amount of machinery that is essentially equivalent ends up being `#include`d twice. My compile times are higher with `boost.json` than they were with `nlohmann`, which is not what I was expecting.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-18 13:01:05 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219464950  

it should work fine, because `boost::string_view` is compatible with `std::string_view`. Not sure why your compile times are longer. Mine are much faster relative to nlohmann. Although you're right that the types which mirror std do require additional include material (e.g. `boost::system::error_code`). If it makes you feel better, the Boost equivalents have more features. error_code records the file and line number for example. And `boost::string_view` is available in C++11.

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-08-18 16:29:11 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219696866  

@kamrann can you please describe how you use the library. Are you using the header-only mode? If so, have you put `#include <boost/json/src.hpp>` in a dedicated source file, that you don't usually touch? Also, maybe you have any guesses what increases those compilation times? Our error_code and string_view are relatively small components and shouldn't result in any significant increase of compilation time.

---

## Comment 6

> Username: kamrann  
> Created at: 2022-08-18 16:33:19 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219700619  

> it should work fine, because `boost::string_view` is compatible with `std::string_view`.   
  
This isn't the case as far as I can see. `std::string_view` appears to be constructible from `boost::string_view`, but only with `c++23`. Conversion from `std::string_view` to `boost::string_view` [doesn't exist at all](https://github.com/boostorg/utility/issues/40), meaning neither calls to `json::parse` nor assignment of a `std::string_view` to a `json::value` works without manually converting.  
  
> Not sure why your compile times are longer. Mine are much faster relative to nlohmann. Although you're right that the types which mirror std do require additional include material (e.g. `boost::system::error_code`).   
  
Yep, it's hard to compare of course as practical compile time hit is so dependent on what transitive includes are included by other things (I'm not doing isolated benchmarks, just looking at effect of compile time on my project). `error_code.hpp`, along with `diagnostic_information.hpp`, are by far the biggest hits in my case.  
  
> If it makes you feel better, the Boost equivalents have more features. error_code records the file and line number for example. And `boost::string_view` is available in C++11.  
  
Yep, the problem really is just that if you don't want/already need these additional features/lower standard requirements, then you take a hit. So the end result is a feeling of being forced to go one of two ways - not use `boost` at all, or shift entirely from `std` to `boost`. Which is a pity, but I don't have any particularly good suggestions for improving the situation.

---

## Comment 7

> Username: kamrann  
> Created at: 2022-08-18 16:43:07 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219709470  

@grisumbras No, currently I'm building shared libraries. Here's an example include time chart, but not sure how useful it is as it's so context dependent (in this case, `std` `<memory>` and `<string>` were already included before the covered breakdown).  
  
![image](https://user-images.githubusercontent.com/2475657/185449465-99205d91-4391-4ef9-aa41-ccf98aa64115.png)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-08-18 16:48:55 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219714237  

OH... I am sorry. When I said `boost::string_view` what I meant was `boost::core::string_view` which is an improved version that compiles faster and is compatible with `std::string_view`. The latest JSON uses it:  
https://github.com/boostorg/json/blob/7ecbeccee63a2143820dd4448f3672dd161eca4e/include/boost/json/string_view.hpp#L33

---

## Comment 9

> Username: kamrann  
> Created at: 2022-08-18 17:15:00 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219737741  

Ah that's great, wasn't aware of that. I should have mentioned actually that I'm on `1.78` currently due to using `build2` as package manager, but will upgrade when I can. Thanks!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-08-18 18:13:24 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219790280  

>  I'm on 1.78  
  
Obviously :)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-08-18 18:14:10 UTC  
> Url: https://github.com/boostorg/json/issues/553#issuecomment-1219790889  

When you upgrade, if you have the time I would like to know if this improved version of `string_view` addresses your issues with `std::string_view` compatibility, as this is a new type and we are looking for feedback. Thanks!
