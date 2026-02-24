# #473 - throw system_error [Closed]

> Username: vinniefalco  
> Created at: 2022-08-29 18:24:01 UTC  
> Updated at: 2022-09-16 04:51:25 UTC  
> Closed at: 2022-09-16 04:51:24 UTC  
> Url: https://github.com/boostorg/url/issues/473  

When exceptions are thrown due to error codes (`result` or `ec`), we should use `detail::throw_system_error` and ensure the lineage of the source locations traces to the correct places. And we must ensure that every javadoc and noexcept specifier is correct.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-29 18:24:36 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1230695096  

Also, there should be a unit test for each of these places

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-31 00:42:45 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1232317829  

> the correct places  
  
I think Peter disagrees that these are the correct places. I tend to agree. Not showing the places where it ultimately fails makes it hard to find the problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-01 22:10:20 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1234836628  

Whatever the "correct" place is - I'm saying we should make sure that's what is shown. When system error is thrown there are two locations, one for the error code and the point of assignment and the other for the point of the throw.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-02 21:51:39 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1235921829  

I couldn't find any `throw_*` that's incorrect. Maybe this has been fixed already?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-02 23:44:43 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1235982484  

That is possible

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-09-08 16:55:35 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1240975760  

Could you have a quick look to ensure if that's been already fixed or I didn't understand the problem properly?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-13 13:54:32 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1245448973  

Yep I will have a look-see

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-09-16 04:51:24 UTC  
> Url: https://github.com/boostorg/url/issues/473#issuecomment-1248916813  

This is done, I changed the throw functions so they all throw system error.
