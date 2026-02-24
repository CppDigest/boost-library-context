# #881 - remove type aliases [Closed]

> Username: vinniefalco  
> Created at: 2023-04-24 19:07:08 UTC  
> Updated at: 2024-02-09 13:35:26 UTC  
> Closed at: 2024-02-09 13:35:26 UTC  
> Url: https://github.com/boostorg/json/issues/881  

we should get rid of our aliases for error_code, result, et. al. and just use them directly from system.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-04-24 19:11:27 UTC  
> Url: https://github.com/boostorg/json/issues/881#issuecomment-1520691383  

Well, they make code smaller a little bit. Do they create a problem?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-04-24 19:15:01 UTC  
> Url: https://github.com/boostorg/json/issues/881#issuecomment-1520696908  

It just can be a little confusing, documentation-wise. And when you are using multiple libraries that are in the habit of creating aliases for error_code, result, and the rest, now you have a lot of aliases...

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-05-07 10:01:08 UTC  
> Url: https://github.com/boostorg/json/issues/881#issuecomment-1537375470  

There's an insane amount of projects on GH that use `json::error_code`. I feel like removing the alises will cause unnecessary chaos.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-05-07 11:54:04 UTC  
> Url: https://github.com/boostorg/json/issues/881#issuecomment-1537421316  

Interesting, did not know that

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-12-15 13:09:41 UTC  
> Url: https://github.com/boostorg/json/issues/881#issuecomment-1857859092  

For reference: https://github.com/boostorg/url/issues/743.  
  
The appropriate action is to deprecate aliases first, then remove after a few releases.
