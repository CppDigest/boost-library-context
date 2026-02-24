# #104 - Optional constexpr support [Closed]

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 23:48:26 UTC  
> Updated at: 2020-06-09 23:14:30 UTC  
> Closed at: 2020-06-09 23:14:30 UTC  
> Url: https://github.com/boostorg/json/issues/104  

Just an idea, I don't think it is necessary at the moment.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-31 02:34:11 UTC  
> Updated at: 2020-05-31 02:36:33 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636411635  

But constexpr how? constexpr parsing? What? Note this library requires only C++11, which does not have robust support for constexpr in the first place.

---

## Comment 2

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-31 02:51:38 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636412787  

> constexpr parsing?  
  
Yes.  
  
> Note this library requires only C++11, which does not have robust support for constexpr in the first place.  
  
I mean constexpr can be enabled only for C++14 (or 17/20 if something is not available) and higher.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-31 03:24:48 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636414943  

I don't see much utility in constexpr parsing. It would basically have to be a completely separate implementation. And then how could it allocate the necessary memory? The `memory_resource` interface uses virtual functions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-31 03:29:55 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636415251  

Please descrinbe the use-case(s) for `constexpr` JSON parsing?

---

## Comment 5

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-31 04:02:57 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636417420  

> And then how could it allocate the necessary memory?  
  
C++20 allows memory allocation inside constexpr functions, however allocated memory cannot escape a constexpr context.  
  
> The `memory_resource` interface uses virtual functions.  
  
C++20 allows constexpr virtual functions.  
  
> Please describe the use-case(s) for `constexpr` JSON parsing?  
  
No idea :smiley:, but I can imagine the following:  
1. Embed content of json data file in source code.  
2. Then parse it at compile-time.  
3. Then convert it to a specific type.  
4. Finally store the result of (3) in read-only memory of application and use it at run-time.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-05-31 04:54:03 UTC  
> Url: https://github.com/boostorg/json/issues/104#issuecomment-636421701  

That doesn't sound very compelling, considering the enormous amount of work needed. The purpose of this library is to parse JSON at run-time and produce a DOM.
