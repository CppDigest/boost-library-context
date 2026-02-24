# #42 Adapt to serialization/develop updates. [Closed]

> Username: aminiussi  
> Created at: 2017-02-22 14:44:46 UTC  
> Updated at: 2017-02-23 09:57:41 UTC  
> Closed at: 2017-02-23 09:57:41 UTC  
> Url: https://github.com/boostorg/mpi/pull/42  

Remove all occurences of serialization/array.hpp, which apparently were not used (all test passes without them) and is only availlable in c++11 mode ( serialization/array.hp includes <array>, g++5.x will fail, and we don't use std::array anyway).  
  
also, serialization/detail/get_data has disapeared, so we now use the std::<container>::data() method directly.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-02-22 14:50:46 UTC  
> Url: https://github.com/boostorg/mpi/pull/42#issuecomment-281690415  

Serialization has evolved and as a result, mpi/develop won't compile against serialization/develop.  
As a result all the PR are stuck on this issue.   
So if you can review this one quickly.  
It passes on ubuntu with OpenMpi and g++ 5.2.0.  
I'll try intel15

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-22 15:13:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/42#issuecomment-281697294  

ok on intel15 with serialization/devlop

---

## Comment 3

> Username: aminiussi  
> Created_at: 2017-02-22 15:46:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/42#issuecomment-281707761  

#39 proposed another fix to the get_data issue, but I guess we can just use the data() member function by now.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-02-22 15:52:47 UTC  
> Url: https://github.com/boostorg/mpi/pull/42#issuecomment-281709877  

Hi @Belcourt, please let me know if you can take care of the review as the big freeze is coming.  
Thanks

---

## Comment 5

> Username: Belcourt  
> Created_at: 2017-02-22 16:00:38 UTC  
> Url: https://github.com/boostorg/mpi/pull/42#issuecomment-281712486  

Hi Alain,  
  
Thanks for the ping, yes I’ll review these shortly.  
  
Noel  
  
On Feb 22, 2017, at 8:52 AM, Alain Miniussi <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
Hi @Belcourt<https://github.com/Belcourt>, please let me know if you can take care of the review as the big freeze is coming.  
Thanks  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/mpi/pull/42#issuecomment-281709877>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AA65bWIeIS7S8QkgB3zp7AA6GtloPeGLks5rfFnQgaJpZM4MIuwp>.

---
