# #40 Add header for serialization::make_array [Merged]

> Username: jwakely  
> Created at: 2017-01-26 22:36:10 UTC  
> Updated at: 2017-07-21 10:05:28 UTC  
> Merged at: 2017-02-23 02:19:45 UTC  
> Closed at: 2017-02-23 02:19:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/40  

The `<boost/mpi/python/serialize.hpp>` header uses `serialization::make_array` without including the right header, and so the snapshot at http://downloads.sourceforge.net/boost/boost_1_63_0.tar.bz2 fails to build.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-04-20 12:45:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-295724017  

Could this be merged to master? Without it, Boost 1.64 doesn't build.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-05-19 14:53:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-302724662  

1.64 is still failing on intel17 platforms.  
  
I guess this could be fixed by merging develop on master but that has   
been pending for some time now.  
  
There is a disagreement regarding the merge process which is described   
here: https://github.com/boostorg/mpi/pull/46.  
  
Alain  
  
On 26/01/2017 23:36, Jonathan Wakely wrote:  
>  
> The |<boost/mpi/python/serialize.hpp>| header uses   
> |serialization::make_array| without including the right header, and so   
> the Boost 1.63.0 release fails to build.  
>  
> ------------------------------------------------------------------------  
>  
>  
>         You can view, comment on, or merge this pull request online at:  
>  
> https://github.com/boostorg/mpi/pull/40  
>  
>  
>         Commit Summary  
>  
>   * Add header for serialization::make_array  
>  
>  
>         File Changes  
>  
>   * *M* include/boost/mpi/python/serialize.hpp  
>     <https://github.com/boostorg/mpi/pull/40/files#diff-0> (1)  
>  
>  
>         Patch Links:  
>  
>   * https://github.com/boostorg/mpi/pull/40.patch  
>   * https://github.com/boostorg/mpi/pull/40.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/40>, or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjOYJ81To22F8VNvw3M8ZGI-TmKS0ks5rWR_agaJpZM4LvOnw>.  
>

---

## Comment 3

> Username: jwakely  
> Created_at: 2017-07-01 15:23:17 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-312438072  

I've just been hit by this in 1.64 too - how did this not get noticed before the release?

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-07-02 08:05:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-312477118  

Well, it was. But did not make it from develop to release I guess. See discussion in #46.

---

## Comment 5

> Username: aminiussi  
> Created_at: 2017-07-21 08:18:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-316936895  

I also  merged this one directly to master.

---

## Comment 6

> Username: jwakely  
> Created_at: 2017-07-21 10:05:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/40#issuecomment-316962017  

Thanks!

---
