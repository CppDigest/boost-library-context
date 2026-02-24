# #1894 - Deprecate one of token_list or opt_token_list [Open]

> Username: madmongo1  
> Created at: 2020-04-07 17:15:30 UTC  
> Updated at: 2022-09-25 00:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1894  

See #1886 for motivation.  
  
Both types are essentially identical, with two irrelevant details. One type could be removed with no impact on users.  
  
Note the member function `token_list::exists`and the free function `validate_list`.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-04 12:33:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1894#issuecomment-623436472  

I'm not entirely sure any more that opt_token_list and token_list share the same functionality

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-04 16:26:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1894#issuecomment-623566386  

Yep lets put a pin in this and revisit

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:41:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1894#issuecomment-1256871456  

@vinniefalco When's the revisting going to happen?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-25 00:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1894#issuecomment-1257090901  

We can look at it together when you are ready
