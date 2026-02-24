# #14 - Modifiable query parameters [Closed]

> Username: dgrr  
> Created at: 2020-06-19 10:01:45 UTC  
> Updated at: 2021-09-30 15:02:49 UTC  
> Closed at: 2021-09-30 15:02:49 UTC  
> Url: https://github.com/boostorg/url/issues/14  

Hello,  
  
I notice the function `params` is only able to return a read-only `params_type` class holding the query parameters. Is it possible to implement a version with modifiable parameters?  
  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-19 13:22:28 UTC  
> Url: https://github.com/boostorg/url/issues/14#issuecomment-646632928  

Yes, it is planned (and same for path)

---

## Comment 2

> Username: dgrr  
> Created at: 2020-06-19 14:05:50 UTC  
> Url: https://github.com/boostorg/url/issues/14#issuecomment-646655282  

Ok. I'd like to accelerate that process. How do you want to implement that? Creating a class like `url_view` but modifiable?  
I can try to do a PR.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-06-19 14:24:49 UTC  
> Url: https://github.com/boostorg/url/issues/14#issuecomment-646664402  

> I can try to do a PR.  
  
Unfortunately you will simply have to wait. This project is not yet in beta and I am working on other things. The way to do it is to add the mutating functions to `url_base::params_type`:  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/url_base.hpp#L86

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-14 04:52:53 UTC  
> Url: https://github.com/boostorg/url/issues/14#issuecomment-918800112  

This is being worked on now

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-30 15:02:49 UTC  
> Url: https://github.com/boostorg/url/issues/14#issuecomment-931404752  

Should be good to go now, query params are completely editable
