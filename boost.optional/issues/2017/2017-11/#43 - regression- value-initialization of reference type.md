# #43 - regression: value-initialization of reference type [Closed]

> Username: vinniefalco  
> Created at: 2017-11-04 03:40:09 UTC  
> Updated at: 2017-11-04 17:00:07 UTC  
> Closed at: 2017-11-04 16:50:18 UTC  
> Url: https://github.com/boostorg/optional/issues/43  

Travis is giving me this error:  
https://travis-ci.org/vinniefalco/beast/jobs/297076642#L1379  
  
The variable in question:  
https://github.com/boostorg/beast/blob/e89814ff4ab871acc3c8387c9a0c4f0409b330c5/test/beast/core/buffered_read_stream.cpp#L59

---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-11-04 16:46:18 UTC  
> Url: https://github.com/boostorg/optional/issues/43#issuecomment-341911572  

I reverted my changes from last weekend (with specializing optional for trivially copyable types). I expect your problems to go away.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-04 16:50:07 UTC  
> Url: https://github.com/boostorg/optional/issues/43#issuecomment-341911866  

Sorry about that. I know you tried very hard to make it work. After 1.66.0 we will have more time to study it and see if we can bring back the optimization without the compile errors.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2017-11-04 17:00:07 UTC  
> Url: https://github.com/boostorg/optional/issues/43#issuecomment-341912676  

That's precisely my plan. :)
