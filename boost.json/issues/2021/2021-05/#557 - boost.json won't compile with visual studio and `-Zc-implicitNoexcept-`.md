# #557 - boost.json won't compile with visual studio and `/Zc:implicitNoexcept-` [Closed]

> Username: tru  
> Created at: 2021-05-03 13:24:05 UTC  
> Updated at: 2021-10-08 20:42:21 UTC  
> Closed at: 2021-10-08 20:42:21 UTC  
> Url: https://github.com/boostorg/json/issues/557  

### Version of Boost  
  
1.76.0  
  
### Steps necessary to reproduce the problem  
  
include boost/json.hpp anywhere and compile your program with `/Zc:implicitNoexcept-`  
  
### All relevant compiler information  
  
Visual Studio 2019  
  
  
We need this flag for our project to avoid a bigger refactoring, but with boost.json included we get the following error:  
  
```  
C:\.conan\c5dbf7\1\include\boost/json/pilfer.hpp(191): error C2338:  
C:\.conan\c5dbf7\1\include\boost/json/value.hpp(3522): note: see reference to function template instantiation 'boost::json::value &&boost::json::pilfer<boost::json::value&>(T) noexcept' being compiled  
        with  
        [  
            T=boost::json::value &  
        ]  
```  
  
I understand this is non-standard - so I am just looking for a workaround we can apply to our boost sources until we can get rid of the implicitNoexcept flag.  
  
Thanks.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-05-03 20:45:30 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-831522628  

I don't think we should support a flag to enable non-standard behaviour. Even if we change stuff, so that it will work, there's no guarantee we'll introduce something else that doesn't work with this flag.

---

## Comment 2

> Username: tru  
> Created at: 2021-05-04 06:35:57 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-831714468  

Hi,  
  
I understand that sentiment. On the other hand we have been building boost with this flag since version 1.23 or something similar and boost.json is the first module it breaks on. So maybe there is a simple fix here that can make it work?

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-05-04 11:22:14 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-831867563  

Boost 1.23 is from 2001, there wasn't `noexcept` at the time and so there wasn't this flag in MSVS either.  
  
At first, I was considering accepting a patch to accommodate this flag. But the issue is, we aren't testing with this flag and we certainly aren't planning to. So, each new class in the library will potentially break your build again.  
  
As an alternative, if you don't use Boost.Json, you can disable it. In your logs above I see that you use Conan. Json can be disabled with `boost:without_json=True` in your profile.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-10-08 00:05:58 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938238611  

I think that only a few destructors need to be made noexcept for this to be fixed, and maybe just one, `~value`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-10-08 00:07:57 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938239385  

if we can get it working we need to adjust the test matrix or add a new entry with `/Zc:implicitNoexcept-`

---

## Comment 6

> Username: pdimov  
> Created at: 2021-10-08 00:16:43 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938242710  

It's actually more than just `~value` :-)  
  
Specifically, `~value`, `~array`, `~object`, `~string`, `~key_value_pair`, and `~storage_ptr`. (I.e. only those classes on which `pilfer` is used.)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-10-08 00:17:28 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938242978  

Those can easily be noexcept since they don't throw

---

## Comment 8

> Username: pdimov  
> Created at: 2021-10-08 00:18:03 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938243156  

They are already `noexcept` by default. The option disables the implicit `noexcept` they get.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-10-08 00:18:39 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938243385  

So what change must be made?

---

## Comment 10

> Username: pdimov  
> Created at: 2021-10-08 00:20:42 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938244119  

This one: https://github.com/boostorg/json/pull/636

---

## Comment 11

> Username: grisumbras  
> Created at: 2021-10-08 06:11:09 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938372637  

@tru can you check if this PR does indeed solve your issue?

---

## Comment 12

> Username: tru  
> Created at: 2021-10-08 06:56:05 UTC  
> Url: https://github.com/boostorg/json/issues/557#issuecomment-938393250  

Hi. Thanks for this. But when we updated to 1.77 recently we had to drop this flag and work on the root cause since several modules in boost failed with this flag. So it's not something we need anymore.
