# #394 Prevent C++17 stringview inclusion in C14 build-configs [Closed]

> Username: ngroendyk  
> Created at: 2022-07-27 19:28:44 UTC  
> Updated at: 2022-08-03 07:17:54 UTC  
> Closed at: 2022-08-03 07:17:53 UTC  
> Url: https://github.com/boostorg/asio/pull/394  

BOOST_BUGFIX_ASIO_ISSUE_1098  
  
* This is a fix for: https://github.com/chriskohlhoff/asio/issues/1098  
  pulled into the boost implementation of ASIO  
* According to the C++17 lib-extensions document found at:  
  https://en.cppreference.com/w/cpp/experimental/lib_extensions  
  StringView was not included until compiler __cplusplus version 201411.  
  Examining the source reveals that this compiler condition is evaluated at  
  201402. This change adjusts the check from 201402 to 201411.

---

## Comment 1

> Username: ngroendyk  
> Created_at: 2022-07-27 19:31:19 UTC  
> Url: https://github.com/boostorg/asio/pull/394#issuecomment-1197276854  

Also have a related pull-request in the upstream asio repo (https://github.com/chriskohlhoff/asio/pull/1099). Created bugfix here in the event someone wanted this pulled in/evaluated sooner in boost.

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2022-08-03 07:17:53 UTC  
> Url: https://github.com/boostorg/asio/pull/394#issuecomment-1203577841  

See chriskohlhoff/asio#1098.

---
