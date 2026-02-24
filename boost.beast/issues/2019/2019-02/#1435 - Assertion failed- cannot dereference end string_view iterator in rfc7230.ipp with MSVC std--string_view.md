# #1435 - Assertion failed: cannot dereference end string_view iterator in rfc7230.ipp with MSVC std::string_view [Closed]

> Username: dsewtz  
> Created at: 2019-02-07 15:18:59 UTC  
> Updated at: 2019-02-08 02:54:46 UTC  
> Closed at: 2019-02-08 02:54:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1435  

Assertion failed: cannot dereference end string_view iterator  
when compiling with BOOST_BEAST_USE_STD_STRING_VIEW in Debug mode  
  
The "culprit" is in rfc7230.ipp lines 363,364:  
v_.second = param_list{string_view{&*it_,  
                static_cast<std::size_t>(pi.it - it_)}};  
when checking "permessage_deflate\r\n".  
  
I know this is just because of iterator debug helpers being enabled in debug mode. So it's not critical.   
  
### Version of Beast 189 (Boost 1.69.0 release)  
  
### Steps necessary to reproduce the problem  
Compile CppCon2018 example with BOOST_BEAST_USE_STD_STRING_VIEW and run  
connect to Websocket  
  
### All relevant compiler information  
MSVC v141 cl 19.16.27026.1 x64  
VS 2017 15.9.6 /std::c++17

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-07 15:24:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461467250  

Appveyor build should be setting `BOOST_BEAST_USE_STD_STRING_VIEW` to catch this sort of thing, although the examples aren't really tested they are only compiled.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-07 15:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461469476  

Do you have a fix?

---

## Comment 3

> Username: dsewtz  
> Created at: 2019-02-07 16:03:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461486676  

I'm not sure if it handles all cases correctly:  
if(!pi.empty())  
            v_.second = param_list{string_view{&*it_,  
                static_cast<std::size_t>(pi.it - it_)}};  
It's also a bit inelegant, because it checks pi.empty() again, which is already checked the line before.

---

## Comment 4

> Username: dsewtz  
> Created at: 2019-02-07 16:35:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461501289  

Hm... it_ is the end-iterator of the extension list string, only if the extension is parameterless.   
"chunked;a=b;i=j,gzip;windowBits=12" is okay, but  
"chunked;a=b;i=j,gzip;windowBits=12;permessage-deflate" isn't  
so also: it_ = pi.it = pi.first = pi.last = last_  
so, it's probably better to test after lines 351,352:  
  
 v_.first = string_view{&*p0,  
                static_cast<std::size_t>(it_ - p0)};  
  if (it_ == last_) return;  
  
and skip the whole detail::param_iter pi;

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-07 16:41:00 UTC  
> Updated at: 2019-02-07 16:41:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461503625  

Any chance I can talk you into submitting a pull request with a test, and the fix?  
  
https://github.com/boostorg/beast/blob/develop/test/beast/http/rfc7230.cpp#L344  
  
No idea why those tests are commented out...might look into that too...  
  
If not, that's fine too I will take care of it before the next Boost release.

---

## Comment 6

> Username: dsewtz  
> Created at: 2019-02-08 00:09:21 UTC  
> Updated at: 2019-02-08 00:28:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461643482  

seems that you can ;-)  
  
Thank you for your great work on Beast, it's highly appreciated! ... I hope you get rich quick!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-02-08 00:44:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461650385  

Release and Debug behave different?

---

## Comment 8

> Username: dsewtz  
> Created at: 2019-02-08 00:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461652441  

Yes, that's why I don't deem it critical. Without iterator debug helper assertions, everything worked fine. But it was annoying. It's also hard to turn those elemental checks off, in a way that only affects beast includes. Because without intermediate precompiled headers the define influences all includes first seen through beast...

---

## Comment 9

> Username: dsewtz  
> Created at: 2019-02-08 01:06:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1435#issuecomment-461654795  

Oh, I see that you mean the pull request comment(?)  
The lower part was auto constructed from my local commits. It wasn't those two tests, but my own new tests that failed. I just mistook the #number from the output at first.  
But yes, previously them and some others produced errors in debug but not in release.
