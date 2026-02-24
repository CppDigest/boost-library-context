# #1 Wrong relative path [Closed]

> Username: teeks99  
> Created at: 2020-06-26 23:25:15 UTC  
> Updated at: 2020-07-07 22:08:44 UTC  
> Closed at: 2020-07-07 22:07:10 UTC  
> Url: https://github.com/boostorg/outcome/pull/1  

This path was broken on the [boost docs](https://www.boost.org/doc/libs/1_73_0/libs/outcome/doc/html/tutorial/essential/result.html) and the [github pages docs](https://boostorg.github.io/outcome/tutorial/essential/result.html).

---

## Comment 1

> Username: ned14  
> Created_at: 2020-07-07 13:57:52 UTC  
> Url: https://github.com/boostorg/outcome/pull/1#issuecomment-654881545  

I think this was fixed some time ago right?

---

## Comment 2

> Username: teeks99  
> Created_at: 2020-07-07 21:50:16 UTC  
> Url: https://github.com/boostorg/outcome/pull/1#issuecomment-655154496  

Both of the above links still have it broken, where is it fixed?

---

## Comment 3

> Username: ned14  
> Created_at: 2020-07-07 21:56:45 UTC  
> Url: https://github.com/boostorg/outcome/pull/1#issuecomment-655157082  

Yes, you're right, it's broken in Boost.Outcome but not standalone Outcome. Sorry about that.

---

## Comment 4

> Username: ned14  
> Created_at: 2020-07-07 22:08:07 UTC  
> Updated_at: 2020-07-07 22:08:44 UTC  
> Url: https://github.com/boostorg/outcome/pull/1#issuecomment-655161628  

Ok properly fixed now in the commit above. It should turn up in the boostorg outcome after a few days as the conversion pipeline lags a bit. Thanks very much for reporting this, I did once run a url checker over the website, but that particular link got edited recently and it got broken.

---
