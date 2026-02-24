# #628 - Deprecate Standalone for 1.78 [Closed]

> Username: vinniefalco  
> Created at: 2021-10-03 04:00:48 UTC  
> Updated at: 2021-10-20 19:35:59 UTC  
> Closed at: 2021-10-20 19:35:59 UTC  
> Url: https://github.com/boostorg/json/issues/628  

When standalone is defined we need to generate a warning message for 1.78. Then in 1.79 we will remove it (after forking it to a new repository on https://github.com/CPPAlliance)

---

## Comment 1

> Username: doganulus  
> Created at: 2021-10-04 19:48:57 UTC  
> Url: https://github.com/boostorg/json/issues/628#issuecomment-933802270  

How would you continue to develop then? Develop on standalone continuously and integrate/snapshot on Boost releases?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-10-04 19:51:06 UTC  
> Updated at: 2021-10-04 19:51:41 UTC  
> Url: https://github.com/boostorg/json/issues/628#issuecomment-933803621  

Well they would diverge. Some features might be ported but it is doubtful, because Boost offers facilities that are not available in C++17. For example, mp11. And Describe. A better `error_code` with source-file information. And other things. Keeping standalone features on parity with Boost.JSON means that the library cannot improve as much.  
  
We are working on making `string_view` and `error_code` compatible with their C++17 counterparts so that using the Boost-flavored JSON in C++17 or later is a more integrated experience.
