# #137 Improve websocket example in README.md [Closed]

> Username: seeekr  
> Created at: 2016-10-15 00:01:21 UTC  
> Updated at: 2016-10-15 15:16:05 UTC  
> Closed at: 2016-10-15 00:39:37 UTC  
> Url: https://github.com/boostorg/beast/pull/137  

change call: to_string --> beast::to_string  
  
My IDE at least was complaining about a plain `to_string` in the code, even though the compilation works fine. I don't understand C++ well enough yet to understand how the compiler manages to resolve `to_string` as `beast::to_string` all by itself, but at least my IDE (CLion) agrees with me (wrongly), so it might have merit to clean up that part of the sample!

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-10-15 00:13:17 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253948201  

Hmm...that is quite interesting! I'm not sure how it compiles myself... @HowardHinnant ?  
  
Anyway, I agree it should change. Can you please also make the change to the corresponding source file? This one:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_example.cpp  
  
Thanks!

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-10-15 00:15:08 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253948374  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/137?src=pr) is 97.55% (diff: 100%)  
  
> Merging [#137](https://codecov.io/gh/vinniefalco/Beast/pull/137?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master       #137   diff @@  
==========================================  
  Files            72         72            
  Lines          4091       4091            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3991       3991            
  Misses          100        100            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [01f939d...b2fd95c](https://codecov.io/gh/vinniefalco/Beast/compare/01f939d68e2f6c21d41bfe5b787fbf586cdfc064...b2fd95c1044fc0fcbc59cb7504ba8997c0a4ba1d?src=pr)

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-10-15 00:15:20 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253948389  

[![Coverage Status](https://coveralls.io/builds/8348754/badge)](https://coveralls.io/builds/8348754)  
  
Coverage remained the same at 97.556% when pulling **b2fd95c1044fc0fcbc59cb7504ba8997c0a4ba1d on seeekr:patch-1** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 4

> Username: seeekr  
> Created_at: 2016-10-15 00:32:30 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253949949  

Amended commit, both README and example cpp should be in sync now!

---

## Comment 5

> Username: seelabs  
> Created_at: 2016-10-15 00:34:37 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253950125  

@seeekr `beast::to_string` is found through ADL (see: http://en.cppreference.com/w/cpp/language/adl). Since `sb.data()` returns a type in the `beast` namespace the compiler knows to look for an overload in that namespace.

---

## Comment 6

> Username: seeekr  
> Created_at: 2016-10-15 00:38:28 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253950449  

@seelabs Ha, that's pretty awesome. Though I imagine it's not really a good idea to depend on it for the situation that we have here, at least not in code targeted at non-expert C++ devs, potentially. But excellent to know how that works! Thanks!

---

## Comment 7

> Username: seeekr  
> Created_at: 2016-10-15 00:39:37 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253950538  

closing as we're merging the two PRs as one

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2016-10-15 00:39:59 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253950573  

@seeekr I agree, the example code should be clear and concise.

---

## Comment 9

> Username: coveralls  
> Created_at: 2016-10-15 00:54:42 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253951817  

[![Coverage Status](https://coveralls.io/builds/8348970/badge)](https://coveralls.io/builds/8348970)  
  
Coverage remained the same at 97.556% when pulling **1097223f3a066dac93cf8c6b597f56510b1859cb on seeekr:patch-1** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 10

> Username: HowardHinnant  
> Created_at: 2016-10-15 15:16:05 UTC  
> Url: https://github.com/boostorg/beast/pull/137#issuecomment-253990265  

Fwiw, `to_string(sb.data())` compiles because `sb.data()` is a type defined in namespace `beast` and so ADL (Argument Dependent Lookup) searches namespace `beast` for a matching `to_string` signature.

---
