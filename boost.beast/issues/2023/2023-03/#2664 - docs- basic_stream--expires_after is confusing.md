# #2664 - docs: basic_stream::expires_after is confusing [Closed]

> Username: Spongman  
> Created at: 2023-03-27 20:18:19 UTC  
> Updated at: 2024-01-18 16:09:40 UTC  
> Closed at: 2024-01-18 16:09:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2664  

[basic_stream::expires_after](https://www.boost.org/doc/libs/1_81_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/expires_after.html) states:  
  
> Set the timeout for the next logical operation.  
  
The use of the definite article implies the timeout applies to a single operation.  
  
But then it goes on to state:  
> The timer applies collectively to any asynchronous reads or writes...  
  
So, which is it? singular or plural?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-04-24 02:23:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2664#issuecomment-1519292507  

Should be plural. I'll review the details and update the docs.
