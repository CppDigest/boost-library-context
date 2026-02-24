# #88 - Getting integer_log2.hpp deprecation warnings [Closed]

> Username: mwpowellhtx  
> Created at: 2018-12-20 18:23:12 UTC  
> Updated at: 2019-02-18 17:33:41 UTC  
> Closed at: 2018-12-20 18:38:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/88  

> d:\dev\boost.org\boost_1_69_0\boost\pending\integer_log2.hpp(7): note: This header is deprecated. Use <boost/integer/integer_log2.hpp> instead.  
  
This with the latest ``Boost 1.69.0``. I'm assuming this is known at least partly.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-12-20 18:28:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-449092231  

I wasn't aware, no.  Thanks.

---

## Comment 2

> Username: mwpowellhtx  
> Created at: 2018-12-20 18:34:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-449094210  

No worries. I think it is, by process of elimination, anyway.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-12-20 18:38:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-449095292  

I'm not able to reproduce this on the current develop.  integer_log2 is not referenced in any uuid file.  The issue is likely in another repo?

---

## Comment 4

> Username: Lastique  
> Created at: 2018-12-20 18:53:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-449099442  

This is possibly from Boost.Random and fixed by https://github.com/boostorg/random/commit/1be44d405e48a42a63bd8d2dae739560b486a55c in develop.

---

## Comment 5

> Username: mwpowellhtx  
> Created at: 2018-12-20 18:55:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-449100077  

When I add the random generator include, that's when I get the error.  
  
> #include <boost/uuid/random_generator.hpp>  
  
May be a dependency you're taking, however.

---

## Comment 6

> Username: mr-j0nes  
> Created at: 2019-02-18 15:25:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-464773888  

This is not a boost/uuid problem but rather a boost/random one. Here the discussion:  
  
https://github.com/boostorg/random/issues/49

---

## Comment 7

> Username: mwpowellhtx  
> Created at: 2019-02-18 17:33:41 UTC  
> Url: https://github.com/boostorg/uuid/issues/88#issuecomment-464821628  

@mr-j0nes We understand that, thank you. I brought it to the floor because I do not care about *Boost.Random*. My view into the issue is *Boost.Uuid*. Out here.
