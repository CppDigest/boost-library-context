# #538 - Add support for json::string to std::string_view conversion [Closed]

> Username: elegracer  
> Created at: 2021-03-20 10:57:02 UTC  
> Updated at: 2021-04-26 00:29:55 UTC  
> Closed at: 2021-04-26 00:29:55 UTC  
> Url: https://github.com/boostorg/json/issues/538  

When I'm using Boost.JSON along with Boost.Beast, it's common to parse a json frame from a remote http/websocket server and do some processing with it.  
  
A typical scenario is, I need to pass a `json::string` to a function where a `std::string_view` is expected.  
  
I can use standalone Boost.JSON with macro `BOOST_JSON_STANDALONE` to replace `boost::string_view` with `std::string_view`. But since I also need Boost.Beast, mixing Boost and standalone Boost.JSON would result in a lot of "macro redefinition" error.  
  
So now I have to tediously do the conversion like this everytime  
  
```cpp  
void process_sv(std::string_view sv);  
  
auto j_str = j_v.as_string();  
auto j_sv = std::string_view(j_str.data(), j_str.size());  
  
process_sv(j_sv);  
```  
  
It would make it much more convenient if a direct conversion from `json::string` to `std::string_view` is available.  
  
Thank you!

---

## Comment 1

> Username: elegracer  
> Created at: 2021-03-20 11:05:41 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-803291618  

Much better if in non-standalone Boost.JSON, `json::parse()` is able to receive a `std::string_view`.

---

## Comment 2

> Username: saurik  
> Created at: 2021-04-19 03:04:31 UTC  
> Updated at: 2021-04-19 03:06:59 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822134388  

I'd also really love some easy way to get std::string from boost::json::string (maybe `.str()` or something).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-04-19 16:03:21 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822586523  

Could this be done by adding `json::string::operator string_view()` ? Could this allow passing to functions which accept `std::string` and would it create implicit conversion / ambiguous conversion problems? We could check for the availability of the `string_view` header and make it conditional.

---

## Comment 4

> Username: saurik  
> Created at: 2021-04-19 17:30:05 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822645365  

Oh! I had somehow never looked into how std::string is created from std::string_view: FWIW, assuming the (explicit) templated constructor on std::string that uses std::is_convertible (to check if the arbitrary argument can be converted to an std::string_view) would apply if you added this new operator (which I presume it would), it seems like the usage through to std::string would be as easy as it currently is directly from std::string_view (which is only as easy as is likely appropriate ;P); had I understood that, I wouldn't have suggested `.str()`.

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-04-19 18:34:57 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822686087  

If we add a conversion operator to `std::string_view` we'd automatically get implicit conversion to `std::string`. The thing is, we already have this conversion in standalone mode. We can try providing it non-standalone mode when `std::string_view` is avaliable.

---

## Comment 6

> Username: grisumbras  
> Created at: 2021-04-19 18:36:23 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822686987  

IMO, it would have been great if `boost::string_view` simply provided that conversion operator itself, but AFAIK this is not going to happen.

---

## Comment 7

> Username: saurik  
> Created at: 2021-04-19 19:42:38 UTC  
> Updated at: 2021-04-19 19:57:32 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822735511  

FWIW, I am not using standalone mode as I am using a lot of other parts of boost (particularly asio and beast); but using boost shouldn't opt me entirely out of being able to easily use libraries that use std::string ;P. (Also: if boost::string_view had an operator to implicit cast to std::string_view, it wouldn't actually help either the people who want to use json::string with std::string APIs or std::string_view APIs, as that would involve a two-level indirect conversion; it in fact does have an explicit cast operator for std::string, but to even get to use it I first have to `.operator boost::string_view()`, which seems excessive since I still have to explicit `std::string()`.)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-04-19 19:47:12 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-822738216  

Yes I agree we need to make it Just Work, somehow.

---

## Comment 9

> Username: Mike-Devel  
> Created at: 2021-04-20 06:24:56 UTC  
> Updated at: 2021-04-20 09:17:49 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823008074  

Personal opinion: Implicit conversion to `std::string_view` (when available) is a must have for any custom string type ( As pointed out by saurik, going through `boost::string_view` isn't good enough, even if it was supported) .   
  
For everything else, I prefer explicit conversion memberfunctions (`.to_std_str()`) instead of conversion operators EDIT: in particular, if there is the "danger" of allocation.

---

## Comment 10

> Username: grisumbras  
> Created at: 2021-04-20 08:03:20 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823068758  

Can you explain why would it not solve the issue, if `boost::string_view` had an implicit conversion operator to `std::string_view`?

---

## Comment 11

> Username: saurik  
> Created at: 2021-04-20 08:54:35 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823105207  

@grisumbras C++ doesn't attempt to follow multiple indirect conversion operators (for reasonable reasons), so it wouldn't actually do anything: you have a boost::json::string and you want to get to an std::string_view, but there is no direct implicit conversion from json::string to std::string_view, so you would have to manually perform one of the casts (which is not at all much better than what you currently have).

---

## Comment 12

> Username: Mike-Devel  
> Created at: 2021-04-20 09:15:56 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823119769  

A minimal example demonstrating this: https://godbolt.org/z/Pj1xxfvTh

---

## Comment 13

> Username: grisumbras  
> Created at: 2021-04-20 12:28:07 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823232730  

Sorry, my bad. Somehow that completely escaped me. So, in the end, `operator std::string_view` in `boost::string_view` would not have simplified this conversion much. Meanwhile, `operator std::string_view` in `json::string` would allow implicit conversion to `std::string_view` **and** explicit conversion to `std::string` (although, only with C++17 standard library).  
  
```c++  
json::string  jv = "foobar";  
foo(std::string(jv))  
```  
Is this good enough?

---

## Comment 14

> Username: grisumbras  
> Created at: 2021-04-20 12:46:42 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823244485  

BTW, @elegracer,  the "macro redefinition" error you had most likely had been already solved and standalone JSON should work alongside Boost. The fix is now not only in develop and master, but also in the latest Boost release. You can try it while we are deciding on the best way to handle this issue in non-standalone mode.

---

## Comment 15

> Username: elegracer  
> Created at: 2021-04-20 12:55:03 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823250387  

@grisumbras That's great! I will give it a try, and wait for good news about the case in non-standalone mode.

---

## Comment 16

> Username: saurik  
> Created at: 2021-04-20 14:44:06 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823332950  

> Is this good enough?  
  
@grisumbras For avoidance of any doubt, as the person who had brought up std::string, that is good enough for me (under the same argument for why std::string_view also lacks an implicit cast for that case; as noted before: when I first had mentioned `.str()`, I hadn't understood that std::string had that nifty templated explicit constructor to give just a bit more indirect conversion mojo).

---

## Comment 17

> Username: Mike-Devel  
> Created at: 2021-04-20 15:50:14 UTC  
> Url: https://github.com/boostorg/json/issues/538#issuecomment-823389322  

Fine for me, as I luckily use almost only c++17 and up.
