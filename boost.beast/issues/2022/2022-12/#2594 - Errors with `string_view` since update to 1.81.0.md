# #2594 - Errors with `string_view` since update to 1.81.0 [Closed]

> Username: jcmonnin  
> Created at: 2022-12-19 09:27:04 UTC  
> Updated at: 2024-02-14 17:45:56 UTC  
> Closed at: 2024-02-14 17:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2594  

When updating to boost 1.81.0, I faced some compile errors in my code along with deprecation warnings  
  
    BOOST_BEAST_USE_STD_STRING_VIEW is deprecated, use BOOST_NO_CXX17_HDR_STRING_VIEW instead  
  
This links to #2451.  
Looking into that, it looks like beast now uses always `boost::core::string_view` which seems to be a separate string_view implementation (with some implicit converstions to `std::string_view`). As it's in `details`, it's not documented.  
  
Loosing the ability to directly use `std::string_view` looks like a step back to me. Some things that used to work don't work anymore, like implicitly assigning a `string_view` to a `std::filesystem::path`. Obviously this is nothing that can't be worked around, but this illustrates why having multiple `string_view` implementations isn't necessarily a good idea.  
  
The deprecation warning seems also a bit misleading. `BOOST_NO_CXX17_HDR_STRING_VIEW ` seems to do the opposite to `BOOST_BEAST_USE_STD_STRING_VIEW` (eg. completely switch off conversions to `std::string_view`).  
  
Please correct me if I got something wrong.  
  
Would you consider bringing back the option to use `std::string_view`?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-20 07:21:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1358938782  

The idea was to unify this, because otherwise it gets weird. With the header set, boost::core::string_view can interact with std::string_view, but it doesn't work the other way.   
  
The problem is not only that beast is a C++11 library and `std::string_view` C++17, it's also that the standard versions tend to become stale; e.g. `boost::system::error_code` now has a few nifty features not available with `std::error_code`. This is why there was an effort to unify towards the boost types.  
  
I am surprised `std::filesystem::path` can't be constructed from a `boost::core::string_view`, tbh.  
  
@vinniefalco what do you think?

---

## Comment 2

> Username: jcmonnin  
> Created at: 2022-12-20 10:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1359167139  

I see your point about the standard libraries becoming stale from the point of view of the library author. For the specific case of `std::string_view` , if it's missing important features and there are plans to have bring a improved version, this is understandable. In other cases, having `boost::core::string_view` defined as a type alias of `std::string_view` when targeting C++17 or higher is going to give a better user experience (both to interact with other libraries and in the debugger).  
  
As a library user targeting C++20, I prefer using the standard library where possible and see the boost libraries as one source of libraries to provide me the features I need (here mostly networking); similar to any other library on github. I that sense I would prefer if boost's effort for fallback implementations for old C++ standards and improved versions of existing standard library code were in a separate project. I understand this is personal opinion and doesn't align with the general direction of the boost project.  
  
Btw, `asio` and `beast` are great!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-20 21:07:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1360245150  

> @vinniefalco what do you think?  
  
I prefer not to have the API/ABI of the library change based on a macro.

---

## Comment 4

> Username: jcmonnin  
> Created at: 2022-12-21 07:35:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1360956011  

Feel free to close the issue, unless you think it's worth keeping it open for one of the following points:  
  
1) Deprecation warning is misleading (a better message would be something like `BOOST_BEAST_USE_STD_STRING_VIEW is deprecated, boost::core::string_view is always used`  
2) Beast uses undocumented private type `std::core::string_view` (in `boost/core/detail/string_view.hpp`) in it's public interface.  
3) Cannot construct `std::filesystem::path` from a `boost::core::string_view` (this is an issue that belongs to boost/core)  
  
About 2) I saw [#110](https://github.com/boostorg/core/issues/100), saying:  
  
```  
Also, it would be a good idea to mention that boost::core::string_view is not intended to be  
used by users (hence it is in detail) and is supposed to be used by Boost library writers.  
```  
  
and following the link there, I saw some comment in an older version of the source file  
  
```  
Unlike `boost::string_view`, `boost::core::string_view` has implicit  
conversions from and to `std::string_view`, which allows Boost libraries that  
support C++11/C++14 to use it in interfaces without forcing users to forgo the  
use of `std::string_view` in their code.  
```  
Previously, I was using `beast::string_view` in my code when interfacing to beast for consistency. I changed all the `beast::string_view` in my code to `std::string_view` and this fixed the compile errors. I guess the missing documentation and description of the design rationale of `boost::core::string_view` is not ideal.

---

## Comment 5

> Username: heitbaum  
> Created at: 2022-12-21 11:20:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1361189793  

With this change and the deprecation of to_string() - snapcast fails to compile.    
  
https://github.com/badaix/snapcast/blob/e30a9f335badc81e743c61ceda6ef2dfc698573b/server/control_session_http.cpp#L127-L185  
  
is the fix to cast `static_cast<std::string>(sv)`?   
Or is there a better way to fix this?  
  
https://www.learncpp.com/cpp-tutorial/introduction-to-stdstring_view/

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-12-21 16:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1361630118  

The documentation needs to be updated, sorry for this confusion!

---

## Comment 7

> Username: jcmonnin  
> Created at: 2022-12-22 09:04:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2594#issuecomment-1362583938  

Thanks Vinnie and Klemens for all your work. Unfortunately I'm a bit too far away from the code to be able to send a PR for the doc.
