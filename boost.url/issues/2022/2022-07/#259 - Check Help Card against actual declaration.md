# #259 - Check Help Card against actual declaration [Closed]

> Username: vinniefalco  
> Created at: 2022-07-27 23:33:23 UTC  
> Updated at: 2023-07-18 18:52:51 UTC  
> Closed at: 2023-07-18 18:52:51 UTC  
> Url: https://github.com/boostorg/url/issues/259  

The contents of the Help Card need to be checked against the `url` and `url_view` declarations to ensure that the members are accurate.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-12 03:50:10 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1212702954  

I went through the help card and:  
  
- `set_scheme_id` should be removed (when did that happen? is that on purpose?)  
- `ipv_future` should become `ipvfuture` or vice-versa. Which one do we want?  
  
Is that correct?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-12 04:08:18 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1212709592  

I guessed `set_scheme_id` but I was wrong, it is `set_scheme` with 2 overloads. But I wonder if we should rename it... the docs kind of suffer a little bit because of it. `set_scheme_id` is more discoverable.  
  
`ipvfuture` is correct.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-12 04:09:14 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1212709971  

We should remove `set_scheme_id`, but we should open an issue to start a discussion on if we should rename it to `set_scheme_id` in the API. This would have to wait until after the review of course.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-12 19:35:17 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1213451476  

> I guessed set_scheme_id but I was wrong, it is set_scheme with 2 overloads.   
  
Yes. I agree. I like `set_scheme` with two overloads more.  
  
> We should remove set_scheme_id, but we should open an issue to start a discussion on if we should rename it to set_scheme_id in the API. This would have to wait until after the review of course.  
  
Sure.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-12 20:51:31 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1213511313  

so you prefer  
```  
set_scheme( string_view )  
set_scheme( scheme )  
```  
  
instead of  
```  
set_scheme( string_view )  
set_scheme_id( scheme )  
```  
?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-08-12 23:10:59 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1213581367  

Yes

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-11 23:56:56 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1243072429  

Needs another quick look just to be sure

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-09-13 01:01:44 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1244761735  

Yes. I've been purposefully ignoring this for now because we're changing a lot lately.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2023-07-18 18:52:51 UTC  
> Url: https://github.com/boostorg/url/issues/259#issuecomment-1640779885  

This has been done many times since this original issue was opened.
