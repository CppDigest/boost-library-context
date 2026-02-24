# #1178 - SSO can cause UB with string_body [Closed]

> Username: vinniefalco  
> Created at: 2018-07-04 19:32:54 UTC  
> Updated at: 2022-06-16 16:28:21 UTC  
> Closed at: 2022-06-16 16:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1178  

If a composed operation's movable state includes a message with a string body, and that composed operation attempts to use a serializer with that message, it will cause undefined behavior when the small string optimization is in effect.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-11-02 01:27:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1178#issuecomment-435241717  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1178#issuecomment-1157878696  

No one has reported this as an issue so we can wait until someone does.
