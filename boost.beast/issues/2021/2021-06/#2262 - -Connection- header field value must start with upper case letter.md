# #2262 - "Connection" header field value must start with upper case letter [Closed]

> Username: harendt  
> Created at: 2021-06-16 09:51:56 UTC  
> Updated at: 2022-09-24 05:13:52 UTC  
> Closed at: 2022-09-24 05:13:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2262  

According to https://datatracker.ietf.org/doc/html/rfc6455#section-4.1, Page 18, the Connection header field MUST include the "Upgrade" token. But Beast uses a lower case "upgrade" token instead.  
  
There are several occurrences., for example:  
* https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/include/boost/beast/websocket/impl/stream_impl.hpp#L632  
* There are several occurrences. For example: https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/include/boost/beast/websocket/impl/rfc6455.hpp#L29

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-16 10:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2262#issuecomment-862236134  

However: https://datatracker.ietf.org/doc/html/rfc2616#section-4.2  
  
> Each header field consists  
   of a name followed by a colon (":") and the field value. Field names  
   are **case-insensitive**.

---

## Comment 2

> Username: harendt  
> Created at: 2021-06-16 10:43:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2262#issuecomment-862258080  

The "Upgrade" token is a field value, not a field name. Don't know what that RFC says about case sensitivity of field values ...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-16 15:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2262#issuecomment-862486073  

> Don't know what that RFC says about case sensitivity of field values  
  
They are case-insensitive unless otherwise specified.  
  
https://datatracker.ietf.org/doc/html/rfc2616#section-2.1

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 03:17:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2262#issuecomment-1008220781  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:13:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2262#issuecomment-1256863556  

Beast follows the standard here, so unless you've got a use-case that requires case-sensitivity, I'd like to close this issue.
