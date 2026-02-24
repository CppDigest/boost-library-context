# #40 Attempt to switch year_month_date() calls to use c++17 struct bind [Closed]

> Username: tlanc007  
> Created at: 2017-05-19 03:57:08 UTC  
> Updated at: 2017-05-20 05:18:04 UTC  
> Closed at: 2017-05-20 05:17:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/40  

I made up a Boost macro: BOOST_NO_CXX17_STRUCT_BIND to decide to no use the C++17 feature.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-05-19 14:55:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302725266  

`BOOST_NO_CXX17_STRUCT_BIND` should live in the Boost.Config library.

---

## Comment 2

> Username: mclow  
> Created_at: 2017-05-19 18:00:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302771089  

BTW, I spoke to Peter Dimov about boost::shared_ptr.  His solution for C++17 is to define `BOOST_NO_AUTO_PTR` when you're building for C++17.  (Again, this would be a Boost.Config change).   
  
This doesn't mean that DateTime shouldn't stop using `boost::shared_ptr`, merely removes one of the motivations to do so.

---

## Comment 3

> Username: tlanc007  
> Created_at: 2017-05-19 19:24:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302790171  

Ok, thanks.  I will look into that and make the change on my c++17_1 push request branch.  I think I may end up closing this one.  It was just intended for Jeff to take a peek at.  
  
My motivation was the feeling that the library itself should be taking advantage of its latest features.  But the #ifs just clutter things up.  This was partly why I was talking to Jeff about Boost needs a better managing system to better handle making use of newer language features.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-05-19 19:31:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302791827  

Here's the PR that I made for Boost.config: https://github.com/boostorg/config/pull/136

---

## Comment 5

> Username: tlanc007  
> Created_at: 2017-05-19 19:34:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302792394  

Thanks.

---

## Comment 6

> Username: tlanc007  
> Created_at: 2017-05-20 05:17:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302851667  

Closing.  The intent was to show the code to Jeff to see the attempt.  I will just point him to my local repo.

---

## Comment 7

> Username: tlanc007  
> Created_at: 2017-05-20 05:18:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/40#issuecomment-302851679  

Closing.  The intent was to show the code to Jeff to see the attempt.  I will just point him to my local repo.

---
