# #15 Fixed missing boost:: namespace [Merged]

> Username: aldonin  
> Created at: 2016-04-21 17:00:05 UTC  
> Updated at: 2016-04-27 09:36:33 UTC  
> Merged at: 2016-04-25 20:36:12 UTC  
> Closed at: 2016-04-25 20:36:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/15  

Fixed missing  `boost::` namespace

---

## Comment 1

> Username: mclow  
> Created_at: 2016-04-21 17:06:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/15#issuecomment-213019325  

This looks fine to me, but I wonder how this ever worked.  Can you point to a test case that fails before/succeeds after?

---

## Comment 2

> Username: aldonin  
> Created_at: 2016-04-22 10:17:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/15#issuecomment-213368331  

Hello @mclow,   
I don't actually understand what do you want to see?   
Seems like it began after [this](https://github.com/boostorg/uuid/commit/3ca9d3fae00805833d42210e51f24966939fbc05) commit. Before it, the previos version of [test_wserialization.cpp](https://github.com/boostorg/uuid/blob/3ca9d3fae00805833d42210e51f24966939fbc05/test/test_wserialization.cpp) ended with:  
`return 0;`  
But commit changed this to:  
`return report_errors();`  
So as you can see the most part of another tests ended with `return boost::report_errors();`  
But in our case this `boost::` namespace is missing. So in case of compiling I get error message   
  
> use of undeclared identifier 'report_errors'  
  
And adding `boost::` namespace fix this problem.   
  
I hope I clarify this moment for you.  
Thanks in advance!

---
