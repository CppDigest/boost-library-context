# #22 - build string in-place? [Closed]

> Username: jwdevel  
> Created at: 2021-05-01 20:11:59 UTC  
> Updated at: 2025-10-21 16:40:52 UTC  
> Closed at: 2025-10-21 16:40:52 UTC  
> Url: https://github.com/boostorg/static_string/issues/22  

I cannot find a way to do this, so I suppose it is a feature request (unless I miss something).  
  
I want to have a `static_string` allocated, then write some data into it via `fmt::format_to_n` (or you could imagine `snprintf`, etc), and then tell it "here is your new length." I am trying to avoid an extra copy.  
  
eg:  
  
```  
static_string<10> str;  
auto ret = fmt::format_to_n(  
   str.data(),  
   str.capacity(),  
   "foo {}"  
   123);  
auto length = ret.out - str.data();  
str.set_length_to(length);     // this function does not exist  
```  
  
I cannot use `resize()` because that will stomp the data with `char()`, which is `0`.  
  
Note: one alternative is to pass an insertion iterator, like so:  
  
```  
fmt::format_to_n(  
  std::back_inserter(str),  
  ...);  
  
```  
  
However, this repeatedly calls `push_back` on the string, which repeatedly null-terminates the data unnecessarily.  
Only the final null terminator matters, so the others are wasted work.  
  
  
Or maybe there is a way to do this already, which I am missing?

---

## Comment 1

> Username: sdkrystian  
> Created at: 2021-05-12 15:16:28 UTC  
> Url: https://github.com/boostorg/static_string/issues/22#issuecomment-839856956  

Currently, there is no way to do this. However, in Boost.JSON we permit access to characters in the range `[size(), capacity())` for this exact purpose -- @vinniefalco @pdimov thoughts?

---

## Comment 2

> Username: WPMGPRoSToTeMa  
> Created at: 2022-10-20 00:53:04 UTC  
> Updated at: 2022-10-20 00:55:22 UTC  
> Url: https://github.com/boostorg/static_string/issues/22#issuecomment-1284748997  

C++23 introduces [`resize_and_overwrite`](https://en.cppreference.com/w/cpp/string/basic_string/resize_and_overwrite) member for `basic_string`. I think implementing it for the `static_string` should solve the described problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-20 17:33:38 UTC  
> Url: https://github.com/boostorg/static_string/issues/22#issuecomment-1285912803  

Not strictly related to `static_string`, but the design of `resize_and_overwrite` is defective because of how it mandates an inversion of the flow of control. It makes some useful idioms impossible to express, in favor of "safety no matter what" (which was never a core C++ principle).

---

## Comment 4

> Username: gennaroprota  
> Created at: 2025-10-21 16:40:21 UTC  
> Url: https://github.com/boostorg/static_string/issues/22#issuecomment-3427614695  

I added `resize_and_overwrite()` to implement the arithmetic conversions (300781d9542f6cded7298981195d67535c9807ad). So I'm going to close this issue.
