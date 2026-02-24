# #456 - query_param_encoded_view `noexcept` [Closed]

> Username: alandefreitas  
> Created at: 2022-08-25 17:17:59 UTC  
> Updated at: 2022-09-03 20:33:42 UTC  
> Closed at: 2022-09-03 20:33:42 UTC  
> Url: https://github.com/boostorg/url/issues/456  

We have many issues here.   
  
If we want to ensure this is only created by the container, then we need to hide the public constructors that don't validate things.   
  
If we want to the user to be able to create this, then we need to either validate the input when constructing it or remove `noexcept` from the conversion to the decoded view.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-25 17:19:06 UTC  
> Url: https://github.com/boostorg/url/issues/456#issuecomment-1227551712  

Or a third option:  
  
public constructors for the plebs validate and throw, while private constructors for La Cosa Nostra don't validate and are `noexcept`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-03 20:08:25 UTC  
> Url: https://github.com/boostorg/url/issues/456#issuecomment-1236191771  

I believe this is solved with `pct_string_view`.
