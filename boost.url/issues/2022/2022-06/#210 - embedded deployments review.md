# #210 - embedded deployments review [Closed]

> Username: vinniefalco  
> Created at: 2022-06-26 21:55:15 UTC  
> Updated at: 2022-09-01 22:56:38 UTC  
> Closed at: 2022-09-01 22:56:38 UTC  
> Url: https://github.com/boostorg/url/issues/210  

The library needs to be reviewed for resource usage with an eye to embedded environments. The documentation should state what steps need to be taken to ensure that the resulting library consumes as little as possible. For example they might disable source locations in error codes and exceptions. And any implementation-defined internal stack buffers might be tuned downwards in size (see Boost.JSON for an example of this).

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-27 16:48:55 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1167600711  

> library consumes as little as possible  
  
So the criteria are  
  
- Included headers  
- Binary size  
- Size of stack buffers  
- Dynamic allocations  
  
Any other criteria?  
  
About the headers:  
  
> For example merely including <memory> or <string> or <system_error> is a problem for some users.  
  
This seems to be the biggest constraint. Once the API is stable, it's hard to remove some of these headers (with the exception of `<algorithm>`). Unless we have some macros that would make functionality related to some types (e.g. string_view) unavailable?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-27 17:35:46 UTC  
> Updated at: 2022-06-27 17:36:03 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1167658546  

No, included headers don't count. This issue regards how the library performs when it is _deployed_ on embedded systems, not when it is _compiled_ on embedded systems.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-28 18:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1169072196  

OK. So the issue converges to  
  
- Macro to disable source locations in error codes and exceptions  
- Macro to tune the size of internal stack buffers

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-28 20:26:27 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1169199087  

Yes and _maybe_ something for the length of error code strings, if they take up a lot of space. But remember this is not something we need to address right now, the issue just serves as a reminder to review it before the first official release (post-acceptance).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-03 02:29:35 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1203412474  

I thought source locations already had a toggle (from the original lib) ?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-08-03 19:03:43 UTC  
> Url: https://github.com/boostorg/url/issues/210#issuecomment-1204361934  

The toggle is there, but it wasn't being used to disable the location at some points.
