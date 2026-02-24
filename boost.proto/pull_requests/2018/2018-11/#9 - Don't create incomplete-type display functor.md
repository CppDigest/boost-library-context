# #9 Don't create incomplete-type display functor [Merged]

> Username: nemanja-boric-sociomantic  
> Created at: 2018-11-02 23:19:00 UTC  
> Updated at: 2018-11-04 19:34:02 UTC  
> Merged at: 2018-11-04 19:33:09 UTC  
> Closed at: 2018-11-04 19:33:09 UTC  
> Url: https://github.com/boostorg/proto/pull/9  

The display functor used to be an incomplete type and it couldn't  
be constructed before fully defined.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-11-03 01:13:37 UTC  
> Url: https://github.com/boostorg/proto/pull/9#issuecomment-435549412  

I can confirm that the PR fixes the problem.  
  
before https://travis-ci.org/Kojoley/spirit/builds/450083296  
after https://travis-ci.org/Kojoley/spirit/builds/450087706

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-11-04 19:03:12 UTC  
> Url: https://github.com/boostorg/proto/pull/9#issuecomment-435696270  

I am pinging this as it blocks Spirit.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-11-04 19:34:02 UTC  
> Url: https://github.com/boostorg/proto/pull/9#issuecomment-435698572  

Thanks!

---
