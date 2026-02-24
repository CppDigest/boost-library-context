# #277 - pct_encoding needs a tidying [Closed]

> Username: vinniefalco  
> Created at: 2022-07-30 05:12:15 UTC  
> Updated at: 2022-08-04 03:00:15 UTC  
> Closed at: 2022-08-04 03:00:15 UTC  
> Url: https://github.com/boostorg/url/issues/277  

There were functions with the same name, different signatures, in different namespaces. Many call sites are formatted incorrectly and it is unclear which versions of what functions are being called with which arguments. This all needs to be cleaned up, and the boilerplate refactored into a uniquely named set of helper functions as implementation details to cut down on parameter spam and name duplication.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-03 00:05:12 UTC  
> Url: https://github.com/boostorg/url/issues/277#issuecomment-1203335130  

> There were functions with the same name, different signatures, in different namespaces. Many call sites are formatted incorrectly and it is unclear which versions of what functions are being called with which arguments.   
  
I'm not sure I understand this.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 00:08:29 UTC  
> Updated at: 2022-08-03 00:09:00 UTC  
> Url: https://github.com/boostorg/url/issues/277#issuecomment-1203336997  

I had to rename `detail::pct_encode` to `detail::pct_encode_impl` because of ambiguity. Blocks like this are hard to read now:  
  
![image](https://user-images.githubusercontent.com/1503976/182497201-1da95ecf-37eb-423f-91f9-bd81b56077f0.png)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-03 00:08:52 UTC  
> Url: https://github.com/boostorg/url/issues/277#issuecomment-1203337223  

also I renamed `detail::pct_encode_bytes` to `detail::pct_encode_bytes_impl` because of conflicts.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-03 18:50:25 UTC  
> Updated at: 2022-08-03 18:50:31 UTC  
> Url: https://github.com/boostorg/url/issues/277#issuecomment-1204349291  

So you just want to tidy the formatting here or do you have something more special in mind?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-03 19:33:46 UTC  
> Url: https://github.com/boostorg/url/issues/277#issuecomment-1204390134  

definitely start with a reformatting to be readable
