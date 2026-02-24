# #538 - mutable_buffer_body [Closed]

> Username: vinniefalco  
> Created at: 2017-06-24 16:22:56 UTC  
> Updated at: 2022-10-12 00:51:13 UTC  
> Closed at: 2022-10-12 00:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/538  

`buffer_body` has a shortcoming, you cannot assign const buffers and use it with a serializer without a `const_cast` (unacceptable in modern code). We could add a `mutable_buffer_body` and change `buffer_body::value_type::data` to `void const*`, and cut out the writer. This highlights a problem with the HTTP relay example though: https://github.com/vinniefalco/Beast/issues/537  
  
See: https://github.com/vinniefalco/Beast/issues/535

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 01:41:24 UTC  
> Url: https://github.com/boostorg/beast/issues/538#issuecomment-312760584  

See https://github.com/vinniefalco/Beast/issues/580

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-11 18:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/538#issuecomment-1275102250  

I think it would be better to add a `const_buffer_type` now, given the API break in the above proposal.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-11 20:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/538#issuecomment-1275248585  

If this can be fixed by adding one line of code (a nested type) great. Otherwise, well... it isn't holding anything up so we can just close it.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-10-12 00:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/538#issuecomment-1275444822  

I'll close it then, user issues should have higher priority.
