# #1535 - Does Beast works with standalone Asio? [Closed]

> Username: Rowandjj  
> Created at: 2019-03-21 08:06:23 UTC  
> Updated at: 2022-02-04 23:39:47 UTC  
> Closed at: 2019-03-25 01:48:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1535  

use macro ASIO_STANDALONE

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-21 12:30:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-475210490  

No

---

## Comment 2

> Username: swex  
> Created at: 2021-05-05 07:20:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-832470756  

Are there any plans to making it possible? I see that json, url libs are having standalone mode from beginning. It would be great to have a standalone beast also!

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-05 08:16:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-832503137  

I actually agree that it would be nice to have.   
@vinniefalco what do we think about this?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-05-05 12:23:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-832644543  

No can do

---

## Comment 5

> Username: ecorm  
> Created at: 2022-02-04 23:02:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-1030416281  

Is it just a matter of manpower? Or are there Boost dependencies other than Asio that would be difficult to decouple?

---

## Comment 6

> Username: ecorm  
> Created at: 2022-02-04 23:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1535#issuecomment-1030438324  

I see now that Kohlhoff uses a [Perl script](https://github.com/chriskohlhoff/asio/blob/master/asio/boostify.pl) to generate a "boostified" version of Asio, and the differences between the two are not trivial.
