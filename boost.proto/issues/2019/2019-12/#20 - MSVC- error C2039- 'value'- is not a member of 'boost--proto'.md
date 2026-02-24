# #20 - MSVC: error C2039: 'value': is not a member of 'boost::proto' [Closed]

> Username: zhongwm  
> Created at: 2019-12-05 03:52:01 UTC  
> Updated at: 2019-12-06 15:26:45 UTC  
> Closed at: 2019-12-05 22:21:32 UTC  
> Url: https://github.com/boostorg/proto/issues/20  

https://github.com/boostorg/proto/issues/18  
I am having the same issue in VC2019 community newest version.

---

## Comment 1

> Username: tobias-loew  
> Created at: 2019-12-05 12:20:55 UTC  
> Url: https://github.com/boostorg/proto/issues/20#issuecomment-562106215  

I've got the same error in boost/proto/generate.hpp(239)  
The bug seems to be fixed in the "develop" branch, where line 233 is replaced by  
` #if BOOST_WORKAROUND(BOOST_MSVC, < 1800)`  
  
Please merge it over to master and put it release 1.72

---

## Comment 2

> Username: MarcelRaad  
> Created at: 2019-12-05 12:23:45 UTC  
> Url: https://github.com/boostorg/proto/issues/20#issuecomment-562107155  

There's even an open PR for that: https://github.com/boostorg/proto/pull/19

---

## Comment 3

> Username: tobias-loew  
> Created at: 2019-12-05 14:04:25 UTC  
> Url: https://github.com/boostorg/proto/issues/20#issuecomment-562142261  

I wrote a mail to the boost-developer list:  
http://boost.2283326.n4.nabble.com/release-proto-MSVC-19-24-28314-VS-2019-16-4-0-won-t-compile-proto-s-generate-hpp-td4710720.html

---

## Comment 4

> Username: ericniebler  
> Created at: 2019-12-05 22:21:32 UTC  
> Url: https://github.com/boostorg/proto/issues/20#issuecomment-562345047  

The fix has been merged to master. Fingers crossed, all should be well again. Pls reopen if that's not the case.

---

## Comment 5

> Username: kanstantsin-chernik  
> Created at: 2019-12-06 15:26:45 UTC  
> Url: https://github.com/boostorg/proto/issues/20#issuecomment-562616612  

Any workarounds for current release?
