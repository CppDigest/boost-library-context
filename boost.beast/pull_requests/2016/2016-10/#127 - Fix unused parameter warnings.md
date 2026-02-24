# #127 Fix unused parameter warnings [Closed]

> Username: awhatson  
> Created at: 2016-10-13 02:37:28 UTC  
> Updated at: 2016-10-20 22:00:11 UTC  
> Closed at: 2016-10-20 22:00:11 UTC  
> Url: https://github.com/boostorg/beast/pull/127  

Fixes warnings from gcc 5.4 (ubuntu xenial) with -Wall -Wextra.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-10-13 02:46:02 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253399177  

Thanks for submitting this! The reason that I leave parameters in, even if they are unused, is because the names show up in the documentation. And for private functions, the name provides a clue as to what the variable does. I will consider further increasing the warning levels though, and see what happens.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-10-13 02:50:10 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253399667  

[![Coverage Status](https://coveralls.io/builds/8313072/badge)](https://coveralls.io/builds/8313072)  
  
Coverage remained the same at 98.051% when pulling **802a4bb42a351480d51df26b632abd336e212b08 on EFTlab:master** into **325f579a1dabcb976a981018153ae4753b0a36d6 on vinniefalco:master**.

---

## Comment 3

> Username: codecov-io  
> Created_at: 2016-10-13 02:50:38 UTC  
> Updated_at: 2016-10-17 02:42:06 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253399714  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/127?src=pr) is 97.56% (diff: 100%)  
  
> Merging [#127](https://codecov.io/gh/vinniefalco/Beast/pull/127?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will increase coverage by **<.01%**  
  
``` diff  
@@             master       #127   diff @@  
==========================================  
  Files            72         72            
  Lines          4091       4101    +10     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           3991       4001    +10     
  Misses          100        100            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [01f939d...48e6e9d](https://codecov.io/gh/vinniefalco/Beast/compare/01f939d68e2f6c21d41bfe5b787fbf586cdfc064...48e6e9d39a4dd4b74fdd93793c74b854b6856c21?src=pr)

---

## Comment 4

> Username: awhatson  
> Created_at: 2016-10-13 03:02:54 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253401142  

No worries, thanks for making this great library!  
  
If you'd rather leave them in the signature, there are still a couple of options:  
  a) "use" the parameter with a noop like (void)(someUnusedParam);  
  b) mark them unused with boost::ignore_unused()  
  
Unfortunately it looks like boost::ignore_unused was only added in boost 1.60, so you'd need to increase the required boost version, which I'd prefer you didn't do as ubuntu xenial ships with boost 1.58, and that's going to be around for a while.  
  
Let me know what you think, I'm happy to amend the pull request.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-10-13 03:19:43 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253403014  

Ah, `boost::ignore_unused` looks quite handy! There's no need to require 1.60, we can just borrow the "implementation" and make it a beast function (in a detail namespace):  
  
```  
template <typename... Ts>  
BOOST_FORCEINLINE BOOST_CXX14_CONSTEXPR void ignore_unused(Ts const& ...)  
{}  
  
template <typename... Ts>  
BOOST_FORCEINLINE BOOST_CXX14_CONSTEXPR void ignore_unused()  
{}  
```  
  
Beast requires C++11 so we can keep it simple.

---

## Comment 6

> Username: coveralls  
> Created_at: 2016-10-13 06:12:47 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253423382  

[![Coverage Status](https://coveralls.io/builds/8314402/badge)](https://coveralls.io/builds/8314402)  
  
Coverage increased (+0.003%) to 98.055% when pulling **7e19a3c4f805e76d56bdce6ead836cb8e7f66f73 on EFTlab:master** into **325f579a1dabcb976a981018153ae4753b0a36d6 on vinniefalco:master**.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-10-13 09:42:24 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253465798  

Should we add `/Wall` and `/Wextra` to the compilation flags?

---

## Comment 8

> Username: coveralls  
> Created_at: 2016-10-14 02:28:14 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253693945  

[![Coverage Status](https://coveralls.io/builds/8331916/badge)](https://coveralls.io/builds/8331916)  
  
Coverage increased (+0.005%) to 98.056% when pulling **7610eb2ac209eb4063c11cd895cf05b0923a5ba1 on EFTlab:master** into **325f579a1dabcb976a981018153ae4753b0a36d6 on vinniefalco:master**.

---

## Comment 9

> Username: awhatson  
> Created_at: 2016-10-14 02:33:51 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-253694649  

I've added -Wextra for gcc only, and caught a few more missing includes from tests and examples.  I'm not in a position to test additional warnings on MSVC or OSX so those haven't been added, sorry.  
  
I've also taken the liberty of fixing some missing includes, these weren't a problem in normal usage but popped up when building each header separately (eg. clang-complete or similar IDE functionality).  
  
To find these I used the following:  
`find -name '*.hpp' | xargs -n1 -P4 g++ -std=c++11 -Wall -Wextra -Wpedantic -Iinclude -Iextras`  
  
To clean up all the pre-compiled headers this produces:  
`find -name '*.gch' | xargs rm`  
  
The same should be done for .ipp files as these are also producing errors when built on their own, however those are trickier to solve as it involves shuffling type definitions around - a bit too much for me at this stage.

---

## Comment 10

> Username: awhatson  
> Created_at: 2016-10-17 02:28:21 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-254098524  

Rebased on b16.

---

## Comment 11

> Username: coveralls  
> Created_at: 2016-10-17 02:41:11 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-254099941  

[![Coverage Status](https://coveralls.io/builds/8363156/badge)](https://coveralls.io/builds/8363156)  
  
Coverage increased (+0.006%) to 97.562% when pulling **48e6e9d39a4dd4b74fdd93793c74b854b6856c21 on EFTlab:master** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2016-10-19 16:41:55 UTC  
> Url: https://github.com/boostorg/beast/pull/127#issuecomment-254870339  

I made some adjustments - we don't want examples source code to include detail/ headers so I'm tweaking it.

---
