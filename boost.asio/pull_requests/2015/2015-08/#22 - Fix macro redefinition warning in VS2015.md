# #22 Fix macro redefinition warning in VS2015 [Closed]

> Username: mauve  
> Created at: 2015-08-20 12:37:43 UTC  
> Updated at: 2017-12-02 07:50:41 UTC  
> Closed at: 2017-12-02 07:50:41 UTC  
> Url: https://github.com/boostorg/asio/pull/22  

Fixes: https://svn.boost.org/trac/boost/ticket/11539

---

## Comment 1

> Username: mauve  
> Created_at: 2015-09-02 13:47:23 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-137087394  

ping?

---

## Comment 2

> Username: a-teammate  
> Created_at: 2015-09-25 13:08:14 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-143216556  

i did review this code as well and it works and looks good.  
However i do not quite get the need for the check for BOOST_VERSION here.. but maybe im missing some background at this point yet..

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2015-09-25 15:37:56 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-143254397  

@a-teammate There is also a standalone version of Asio not bundled with Boost, which needs the Boost version check. It already has this fix:  
https://github.com/chriskohlhoff/asio/commit/15803ab00bfe9fa709608e704e7ad7c88c4563b7

---

## Comment 4

> Username: rustyx  
> Created_at: 2016-02-05 09:50:52 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-180276680  

Hello? Anyone out there listening??!  
Takes a fricking year to get a one line fix into Boost...

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2016-02-05 09:54:11 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-180278011  

It's already in Boost 1.60: https://github.com/boostorg/asio/commit/b9cb61416e1972d7490d91da91df549703fb0fef

---

## Comment 6

> Username: burannah  
> Created_at: 2016-05-17 09:46:27 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-219670030  

Can we have this change also in non-Boost Asio?

---

## Comment 7

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:50:41 UTC  
> Url: https://github.com/boostorg/asio/pull/22#issuecomment-348675602  

Already fixed.

---
