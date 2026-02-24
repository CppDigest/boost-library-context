# #1120 - http::request docs show some non-request methods [Closed]

> Username: vchang-akamai  
> Created at: 2018-05-08 00:18:56 UTC  
> Updated at: 2018-05-08 01:03:40 UTC  
> Closed at: 2018-05-08 00:59:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1120  

https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__request.html  
  
This page shows methods reason(), result(), result_int().  Each of their docs indicate "only available when isRequest == false. ".    Should suppress these non-request methods when generating docs for http::request.  
  
Hmm.. I just checked http::response and saw similar issue with request-only methods (like "target()") showing up in those docs.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-08 00:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1120#issuecomment-387247624  

>Should suppress these non-request methods when generating docs for http::request  
  
Easier said than done. How can I do that?

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2018-05-08 00:59:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1120#issuecomment-387251780  

I don't have any ideas either :-)  I see some of the difficulties involved. This is probably the best compromise in the situation.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-08 01:03:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1120#issuecomment-387252458  

I agree :)
