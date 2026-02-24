# #26 Fix gcc warnings about data which may be used unitialized. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2016-10-19 21:53:10 UTC  
> Updated at: 2018-02-14 09:13:09 UTC  
> Closed at: 2018-02-14 09:13:09 UTC  
> Url: https://github.com/boostorg/optional/pull/26  

Hi,  
  
This is an attempt to fix the warnings raised by gcc 6 about the data pointer which may be used uninitialized. The corresponding Boost ticket is https://svn.boost.org/trac/boost/ticket/12513.  
  
I checked the code and it looks like this is a gcc false positive. I have opened a gcc ticket here https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78044 where apparently it looks like these are longstanding gcc false positive bugs, which apparently are hard to fix on the compiler side.  
  
So here is a proposal to workaround that on boost side, by making sure that at least one field of the optional storage is being initialized, to "trick" gcc. This will come at the extense of one more (useless) initialization of 1 byte each time the constructor of boost::optional is called without any initializing value, and if the compiler cannot tell for sure that it will later be initialized (we can expect the dummy empty initialization to be optimized out by the compiler if for sure the value is overriden later).  
  
Are you OK with the principle of this fix ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2016-10-20 06:39:22 UTC  
> Url: https://github.com/boostorg/optional/pull/26#issuecomment-255021728  

Thank you for the effort, and for the patch. Yet, I am hesitating to put it in, because it makes the optional run a bit slower. And we are not fixing a real bug. We are only hiding a false positive from GCC.  
  
Instead, can you tell me if the workaround described in http://www.boost.org/doc/libs/1_62_0/libs/optional/doc/html/boost_optional/tutorial/gotchas/false_positive_with__wmaybe_uninitialized.html helps remove the warning?

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-01-27 21:03:31 UTC  
> Url: https://github.com/boostorg/optional/pull/26#issuecomment-275773894  

I think, it would be better to silence the warning with a `#pragma`.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2018-02-12 14:18:31 UTC  
> Url: https://github.com/boostorg/optional/pull/26#issuecomment-364935205  

I think these warnings should be gone in release 1.66.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2018-02-14 09:12:59 UTC  
> Url: https://github.com/boostorg/optional/pull/26#issuecomment-365541421  

Apparently yes (at least the Wandbox examples).  
  
In the meantime, I have migrated to gcc 7 and C++17, and now use exclusively std::optional from libstdc++ rather than boost::optional. std::optional raised less warnings than boost::optional before Boost 1.66, but ironically, even gcc's own implementation of optional raises some warnings ;)

---
