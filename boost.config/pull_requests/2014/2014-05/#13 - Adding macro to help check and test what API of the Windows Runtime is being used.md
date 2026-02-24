# #13 [winrt support] Adding macro to help check and test what API of the Windows Runtime is being used. [Closed]

> Username: stgates  
> Created at: 2014-05-06 18:40:00 UTC  
> Updated at: 2014-05-13 23:44:48 UTC  
> Closed at: 2014-05-13 20:57:27 UTC  
> Url: https://github.com/boostorg/config/pull/13  

This change adds a macro BOOST_WINDOWS_FAMILY that can be used by individual Boost libraries to determine which Windows APIs are available for use. I received some feedback from chriskohlhoff that BOOST_WINDOWS_RUNTIME is a better name. Before making any changes I'm interested in hearing what others think.  
  
This is part of the work mentioned on the Boost dev mailing list:  
  
http://boost.2283326.n4.nabble.com/winrt-support-Adding-support-for-Windows-8-store-phone-to-Boost-libraries-tc4661713.html  
  
I also fixed up a failing test case boost_no_cxx11_hdr_regex.ipp.

---

## Comment 1

> Username: glenfe  
> Created_at: 2014-05-09 01:58:43 UTC  
> Url: https://github.com/boostorg/config/pull/13#issuecomment-42626789  

I agree with Chris that BOOST_WINDOWS_RUNTIME (defined or undefined) is a better choice. If you want to provide a way for developers to detect what specific OS family is available, that is a better fit for Boost.Predef instead of Boost.Config.

---

## Comment 2

> Username: stgates  
> Created_at: 2014-05-13 19:48:49 UTC  
> Url: https://github.com/boostorg/config/pull/13#issuecomment-43003436  

Ok I'll rename to BOOST_WINDOWS_RUNTIME and look for the correct location to add in Boost.Predef instead. Thanks for the feedback. I'll resubmit this pull request to only contain the test fix.

---

## Comment 3

> Username: stgates  
> Created_at: 2014-05-13 20:59:26 UTC  
> Url: https://github.com/boostorg/config/pull/13#issuecomment-43011953  

Submitting a new pull request with just the test changes. And moving this over to Boost.predef.

---
