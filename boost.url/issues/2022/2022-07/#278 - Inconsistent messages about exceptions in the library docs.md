# #278 - Inconsistent messages about exceptions in the library docs [Closed]

> Username: akrzemi1  
> Created at: 2022-07-30 18:31:21 UTC  
> Updated at: 2023-02-22 18:48:45 UTC  
> Closed at: 2023-02-22 18:48:44 UTC  
> Url: https://github.com/boostorg/url/issues/278  

In the docs for develop branch we read in the [Overview.Features](https://develop.url.cpp.al/url/overview.html#url.overview.features) section:  
  
> Interfaces are provided for using error codes instead of exceptions as needed.  
  
And later:  
  
> Works without exceptions   
  
This could be read as "this library does not throw exceptions". But this is far from being true. Consider function [`url::set_scheme`](https://develop.url.cpp.al/url/ref/boost__urls__url/set_scheme/overload1.html).  
  
It can throw in two situations:  
  
 1. When an allocation is needed and this allocation throws. (This is a "lack of resources" situation.) (The type of the exceptions is not well described in the docs.)  
 2. When the input argument is not a valid scheme under [rfc3986](https://tools.ietf.org/html/rfc3986). (This is either an "input validation" or a "contract violation" situation depending on how you want to see -- and document -- the contract of the function.)  
  
The documentation of the function seem to say that passing an invalid scheme to the function is a valid thing to do (no contract violation). This means that this function can be used for validating strings: if they represent valid URL schemes.  
  
And this is all fine by itself, but inconsistent with the statement "Interfaces are provided for using error codes instead of exceptions as needed." What does it mean for function [`url::set_scheme`](https://develop.url.cpp.al/url/ref/boost__urls__url/set_scheme/overload1.html), which doesn't have an overload taking an error code argument?  
  
How can I safely set a scheme name that comes from the external source in the program with exceptions disabled? An invalid input will then terminate my program.  
  
I think the correct summary of the approach to exceptions in this library is:  
  
* Functions that parse strings representing URLs report syntax errors via error codes.  
* Functions throw exceptions on resource limits, such as "insufficient memory" or "too long input".  
* Member functions throw from mutating operations to protect class invariants.  
* Exception throws can be turned into `abort()` via configuration.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-30 18:34:14 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1200273642  

You have a point but this could be easily fixed by simply rewording it to  
  
"**Mostly** works without exceptions"

---

## Comment 2

> Username: akrzemi1  
> Created at: 2022-07-30 21:04:07 UTC  
> Updated at: 2022-07-30 21:04:45 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1200293265  

I might be missing something here, but to me throwing exceptions for resource shortages and invariant protection look like, "works with exceptions as any other library" (which is fine, by the way).   
  
Or maybe the following would be more accurate.  
  
> We have low-level functions, like [pct_decode](https://develop.url.cpp.al/url/ref/boost__urls__pct_decode/overload1.html) that signal errors via error codes, and high-level abstractions, like [url](https://develop.url.cpp.al/url/ref/boost__urls__url.html), that throw exceptions.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-07-30 22:39:39 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1200306217  

Well, no you aren't missing anything. I agree that the docs need to be updated (we are working on that now). The current description on the overview page was written before we started using `result` return types for everything, instead of sometimes passing `error_code&` as an out-param. It will be updated before the review period starts (maybe the night before, lol).

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-22 21:25:49 UTC  
> Updated at: 2022-08-22 21:25:57 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1223089676  

I'm reopening this since it came up in the review again.  
  
> There is one thing that I do not understand though. I read that:  
> 1. The library is tailored for environments that do not use exceptions  
> 2. url guarantees that it maintains its invariant of always storing a  
> string that represents a valid url.  
>   
> So given the following code executed on a platform with exceptions  
> disabled, what happens?  
>   
> void test(url my_url)  
> {  
>   my_url.set_port("twenty-one");  
> }  
>   
> is it going to kill the program? If so, is this considered a satisfactory  
> solution? I use exceptions in my job, so this is a rather theoretical issue  
> for me, but if I were using urls in no-exceptions environment, I think I  
> would expect an interface like:  
>   
> error_code ec;  
> my_url.set_port("twenty-one", ec);  
>   
> Or something similar.  
>

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-10-13 17:02:55 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1277919530  

I believe reopening this issue no longer makes sense at this moment because the documentation has changed and the promises about exceptions that motivate the issue are no longer in the documentation.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-10-13 18:13:48 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1277995594  

we still have to think about `try_set_port` and `try_set_scheme`

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-10-19 21:35:18 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1284598969  

```cpp  
bool  
url_base::  
try_set_port(  
    string_view s)  
{  
    op_t op(*this, &s);  
    auto rt = grammar::parse(s,  
        detail::port_rule{});  
    if (!rt)  
        return false;  
    auto t = *rt;  
    auto dest =  
        set_port_impl(t.str.size(), op);  
    std::memcpy(dest,  
        t.str.data(), t.str.size());  
    if(t.has_number)  
        impl_.port_number_ = t.number;  
    else  
        impl_.port_number_ = 0;  
    return true;  
}  
```  
  
or  
  
```cpp  
result<void>  
url_base::  
try_set_port(  
    string_view s);  
```  
  
?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-11-03 23:58:07 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1302804354  

hmm that is a difficult question

---

## Comment 9

> Username: akrzemi1  
> Created at: 2022-11-04 07:52:22 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1303084605  

I would say that if all other functions return `result<T>` for signalling potential failure, then use `result<void>` for consistency, even if `result<void> try_set_result()` looks awkward by itself.  
  
Is `result<T>` now the preferred way of signalling failures in the library?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-11-04 16:24:14 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1303834899  

> Is result<T> now the preferred way of signalling failures in the library?  
  
For the return values yes but we also use exceptions in some cases. For example if you pass an invalid percent-encoded string to a function that requires a percent-encoded string.

---

## Comment 11

> Username: akrzemi1  
> Created at: 2022-11-04 20:48:56 UTC  
> Updated at: 2022-11-04 20:49:12 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1304231609  

Then if you have to use exceptions, and you use `result<>` then this is quite enough. No need for a third way in the form of `bool`.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-11-04 20:58:48 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1304253917  

You don't _have_ to use exceptions, you could for example first validate percent-encoded string arguments that you intend to pass to library function. This validation function returns `result`.  
  
But yes two is quite enough :) `result<void>` sounds like the cleanest dirty shirt.

---

## Comment 13

> Username: alandefreitas  
> Created at: 2022-11-04 21:21:23 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1304271074  

Yes. `set_port` is the only exception because not any encoded string is valid there.   
  
For all other functions you can:  
  
```  
result<pct_string_view> rs = make_pct_string_view("hello%20world");  
if (rs)  
    u.set_encoded_fragment(*rs);  
```  
  
which only throws if we run out of space in the url.  
  
If we want `try_set_port` and existing functions to not throw when we run out of space, we need a deeper change in how `reserve_impl(std::size_t, op_t&)` works.

---

## Comment 14

> Username: alandefreitas  
> Created at: 2022-11-04 21:21:25 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1304271112  

I think it's worth noting that `url_base::resolve` already returns `result<void>`. If `url_base::try_set_port` returns `bool`, this could become a mess.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2022-11-04 22:28:14 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1304309492  

well that settles it

---

## Comment 16

> Username: alandefreitas  
> Created at: 2023-02-22 18:48:44 UTC  
> Url: https://github.com/boostorg/url/issues/278#issuecomment-1440613243  

https://github.com/boostorg/url/pull/383 has been merged
