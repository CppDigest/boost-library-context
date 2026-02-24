# #112 - permessage-deflate status [Closed]

> Username: ghost  
> Created at: 2016-10-06 20:43:51 UTC  
> Updated at: 2018-03-16 02:01:21 UTC  
> Closed at: 2016-11-16 16:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/112  

Hi Vinnie,  
  
What is the current status of permessage-deflate support? doc/design.qbk states that it was due Q4 2016, do you have a newer estimate of when it might land?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-07 00:10:35 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-252122559  

Thanks for your interest. My current efforts are focused on fixing bugs in the library and getting Beast in the best shape possible for a formal Boost review, which will likely take place in the second half of December or the first half of January. However, permessage-deflate is the next feature. I've already been working on it.  
  
The plan is to create a header-only, modern C++ port of ZLib. That effort is in this branch:  
https://github.com/vinniefalco/Beast/commits/zlib  
  
This branch has a proof-of-concept of the permessage-deflate implementation using ZLib (which makes Beast no longer header-only). Its based on an older version of Beast.  
https://github.com/vinniefalco/Beast/commits/deflate  
  
I would say Q1 of 2017 is a more realistic estimate but you never know.

---

## Comment 2

> Username: ghost  
> Created at: 2016-10-08 23:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-252453596  

Thanks for that update - I will keep my fingers crossed!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-14 23:12:09 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-253941134  

I did work on it this week

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-20 23:01:26 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-255251409  

Making good progress on the port of zlib to c++:  
https://github.com/vinniefalco/Beast/commits/zlib

---

## Comment 5

> Username: ghost  
> Created at: 2016-10-31 12:39:33 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-257283153  

Looks like it is getting close!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-31 12:40:45 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-257283392  

Sure is! And permessage-deflate too:  
https://github.com/vinniefalco/Beast/commits/pmd

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-11-16 16:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-260989958  

It is here!  
https://github.com/vinniefalco/Beast/pull/191

---

## Comment 8

> Username: lls2wow  
> Created at: 2018-03-16 00:53:48 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-373569134  

Hi @vinniefalco , Could you tell me which version of beast much newer implement the pmd ? I want to have a try. Thank you !

---

## Comment 9

> Username: lls2wow  
> Created at: 2018-03-16 00:56:45 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-373569586  

@vinniefalco  should I decompress the websocket data using permessage-deflate extension by my code or I need not to care about it (it decompress automatically)?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-03-16 02:01:21 UTC  
> Url: https://github.com/boostorg/beast/issues/112#issuecomment-373580022  

Everything is automatic, you just need to turn the feature on.
