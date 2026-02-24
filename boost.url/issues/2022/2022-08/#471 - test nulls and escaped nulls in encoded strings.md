# #471 - test nulls and escaped nulls in encoded strings [Closed]

> Username: vinniefalco  
> Created at: 2022-08-29 17:19:19 UTC  
> Updated at: 2022-10-21 19:12:25 UTC  
> Closed at: 2022-10-21 19:12:25 UTC  
> Url: https://github.com/boostorg/url/issues/471  

Like the params and segments, but also everything else

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-27 17:27:43 UTC  
> Updated at: 2022-09-27 17:27:59 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1259825814  

Do you have an example of that? I'm not sure I understand.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-05 20:03:55 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1268910382  

We just need to make sure the library does the right thing when presented with a null percent-escape in a function like `set_encoded_X` (or the params/segments refs) and what do to with a null in a decoded string, e.g. `set_path`

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-10-06 02:58:54 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1269240889  

> does the right thing  
  
throws an exception?  
  
> presented with a null percent-escape in a function like set_encoded_X   
  
An encoded `%00` should fail?  
  
> what do to with a null in a decoded string  
  
What should it do? Throw an exception?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-06 03:36:30 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1269265006  

I don't know! We need to check rfc3986

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-10-06 21:33:09 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1270721542  

I don't think rfc3986 says anything about decoded `\0`s. This is a reasonable safety issue Peter brought up but URLs are definitely allowed to include encoded `\0`s. The most obvious use case is the `data:` scheme. I think this is up to us because the RFC won't help us here.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-10-06 22:11:50 UTC  
> Url: https://github.com/boostorg/url/issues/471#issuecomment-1270752127  

so then nulls are allowed? check if my unit tests for segments and params tests it
