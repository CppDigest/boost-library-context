# #398 - throw_exception in separate namespace in standalone [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:23:25 UTC  
> Updated at: 2021-03-04 15:20:43 UTC  
> Closed at: 2021-03-04 15:20:43 UTC  
> Url: https://github.com/boostorg/json/issues/398  

When exceptions are not disabled, the provided overload of `throw_exception` for standalone should be in the `json::detail` namespace to avoid collisions when linking with other libraries.

---

## Comment 1

> Username: Appendme  
> Created at: 2021-01-24 11:15:07 UTC  
> Updated at: 2021-01-24 17:07:49 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-766330515  

What overload of the throw_exception function is used if the library version is higher than 1.73.0?  
https://github.com/boostorg/json/blob/develop/include/boost/json/detail/impl/except.ipp#L48  
https://github.com/boostorg/json/blob/develop/include/boost/json/detail/impl/except.ipp#L61  
It seems that there is no such overload.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-01-24 21:24:02 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-766434658  

First of all Boost.JSON is only available in Boost 1.75 and later, the check for BOOST_VERSION is no longer necessary (I put that in there during development, before the first Boost release). Second, `throw_exception` is already provided by Boost unless you disable exceptions by defining `BOOST_NO_EXCEPTIONS`. In this case, you are responsible for providing it.

---

## Comment 3

> Username: Appendme  
> Created at: 2021-01-24 21:58:16 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-766439330  

@vinniefalco I collected the library via vcpkg (windows / static library) and have the following situation:  
If I use the library with Boost, I get a link error (there is no boost_json - *.lib in the output files).  
If I use it as a standalone library, then I get an error due to the lack of function overloading. (macros BOOST_VERSION set because I use the boost-beast)  
  
In the first situation, there may be a problem with vcpkg.  
In the second, it turns out that I cannot use this library if the project uses any other boost library?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-01-25 01:25:25 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-766482888  

If you want to use standalone Boost.JSON then you have to define `BOOST_JSON_STANDALONE` when building the library and when the headers are included.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-01-25 17:13:07 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-766968703  

I think there is actually a problem with the existing code and the way it checks `BOOST_VERSION`. See: https://github.com/boostorg/json/issues/490  
  
When #490 is fixed, it is possible that it will resolve your issue.

---

## Comment 6

> Username: grisumbras  
> Created at: 2021-02-15 12:58:16 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-779207534  

@Appendme can you check if your issue is resolved in master?

---

## Comment 7

> Username: Appendme  
> Created at: 2021-02-15 21:48:05 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-779462542  

@grisumbras solved.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-02-16 19:29:45 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-780066652  

Does _master_ have the fix?

---

## Comment 9

> Username: Appendme  
> Created at: 2021-02-16 20:31:48 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-780100078  

@vinniefalco my project built without errors when using the master branch

---

## Comment 10

> Username: grisumbras  
> Created at: 2021-03-04 05:36:22 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-790307108  

@vinniefalco #492 fixed this issue.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-03-04 15:20:43 UTC  
> Url: https://github.com/boostorg/json/issues/398#issuecomment-790695143  

Great, thanks!
