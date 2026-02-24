# #440 - Review: add lowercase/uppercase in pct_encode_opts [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 05:37:33 UTC  
> Updated at: 2022-09-03 20:09:29 UTC  
> Closed at: 2022-09-03 20:09:18 UTC  
> Url: https://github.com/boostorg/url/issues/440  

> In the past I have had situations where it was necessary to specify  
the case for hex letters in %-encoding; please consider adding this to  
pct_encode_opts.  
  
Uppercase should be the default since this is what URL normalization requires.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-23 22:08:33 UTC  
> Url: https://github.com/boostorg/url/issues/440#issuecomment-1224938986  

How does the user control whether upper or lowercase is used in the `url_base`?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-23 22:12:43 UTC  
> Url: https://github.com/boostorg/url/issues/440#issuecomment-1224942707  

> How does the user control whether upper or lowercase is used in the url_base?  
  
By using `set_encoded_xxxx`. Although they shouldn't be doing that because they would be denormalizing the URL.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-23 22:17:33 UTC  
> Url: https://github.com/boostorg/url/issues/440#issuecomment-1224946257  

OK then. Explicit case control is only for percent-encoding free functions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-03 20:09:18 UTC  
> Updated at: 2022-09-03 20:09:29 UTC  
> Url: https://github.com/boostorg/url/issues/440#issuecomment-1236191876  

I did this. lower_cased is in the encode_opts now.
