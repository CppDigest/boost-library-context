# #38 Fix incorrect usage of auto_ptr to free an array [Merged]

> Username: Lastique  
> Created at: 2017-01-10 16:44:51 UTC  
> Updated at: 2017-10-05 06:53:20 UTC  
> Merged at: 2017-02-23 02:17:32 UTC  
> Closed at: 2017-02-23 02:17:32 UTC  
> Url: https://github.com/boostorg/mpi/pull/38  

The `auto_ptr` would use `delete p` to free an array allocated with `operator new[]`, which could result in heap corruption. Replaced it with `scoped_array`.  
  
As an additional bonus, this gets rid of the warnings about using a deprecated component in C++11 mode.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-02-22 10:18:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-281627597  

Passed test on intel15 intel MPI after rebasing.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-22 10:20:03 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-281627959  

Should consider `std::unique_ptr` at some point, but this is implementation code only and some MPI platform may be antique.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-04-20 11:45:16 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-295701135  

Could this be merged to master?

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-27 18:31:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-332614541  

> Could this be merged to master?  
  
Ping, anyone?

---

## Comment 5

> Username: aminiussi  
> Created_at: 2017-09-27 22:00:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-332668089  

Sorry,it does not pop on my builds so I did not pay attention.  
I'll have a look tomorow.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2017-10-04 23:19:15 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-334316800  

Ok, I won't have time this week.  
  
It appears the fix is already on develop and will end up on master once   
develop is merged on master.  
  
Does anyone wants to proceed with the merge ?  
  
cheers  
  
Alain  
  
On 27/09/2017 20:31, Andrey Semashev wrote:  
>  
>     Could this be merged to master?  
>  
> Ping, anyone?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/38#issuecomment-332614541>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjOevMFNXTDAZDee6p48LTcvS6BBFks5smpR9gaJpZM4Lfoeb>.  
>

---

## Comment 7

> Username: Belcourt  
> Created_at: 2017-10-04 23:59:15 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-334322561  

Yes, I can merge this, and all develop into master.  It will take me a day or two to get this done.  
  
Noel  
  
On Oct 4, 2017, at 5:19 PM, Alain Miniussi <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
Ok, I won't have time this week.  
  
It appears the fix is already on develop and will end up on master once  
develop is merged on master.  
  
Does anyone wants to proceed with the merge ?  
  
cheers  
  
Alain  
  
On 27/09/2017 20:31, Andrey Semashev wrote:  
>  
> Could this be merged to master?  
>  
> Ping, anyone?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mpi/pull/38#issuecomment-332614541>, or  
> mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAhsjOevMFNXTDAZDee6p48LTcvS6BBFks5smpR9gaJpZM4Lfoeb>.  
>  
  
  
—  
You are receiving this because you modified the open/close state.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/mpi/pull/38#issuecomment-334316800>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AA65bV4Ql5inkJB6bK5aZ7pW2YwkvPZqks5spBJ0gaJpZM4Lfoeb>.

---

## Comment 8

> Username: aminiussi  
> Created_at: 2017-10-05 06:53:19 UTC  
> Url: https://github.com/boostorg/mpi/pull/38#issuecomment-334375235  

Thanks at lot!  
  
Alain  
  
On 05/10/2017 01:59, Noel Belcourt wrote:  
> Yes, I can merge this, and all develop into master. It will take me a   
> day or two to get this done.  
>  
> Noel  
>  
> On Oct 4, 2017, at 5:19 PM, Alain Miniussi   
> <notifications@github.com<mailto:notifications@github.com>> wrote:  
>  
> Ok, I won't have time this week.  
>  
> It appears the fix is already on develop and will end up on master once  
> develop is merged on master.  
>  
> Does anyone wants to proceed with the merge ?  
>  
> cheers  
>  
> Alain  
>  
> On 27/09/2017 20:31, Andrey Semashev wrote:  
> >  
> > Could this be merged to master?  
> >  
> > Ping, anyone?  
> >  
> > —  
> > You are receiving this because you commented.  
> > Reply to this email directly, view it on GitHub  
> > <https://github.com/boostorg/mpi/pull/38#issuecomment-332614541>, or  
> > mute the thread  
> >   
> <https://github.com/notifications/unsubscribe-auth/AAhsjOevMFNXTDAZDee6p48LTcvS6BBFks5smpR9gaJpZM4Lfoeb>.  
> >  
>  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on   
> GitHub<https://github.com/boostorg/mpi/pull/38#issuecomment-334316800>,   
> or mute the   
> thread<https://github.com/notifications/unsubscribe-auth/AA65bV4Ql5inkJB6bK5aZ7pW2YwkvPZqks5spBJ0gaJpZM4Lfoeb>.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/38#issuecomment-334322561>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjFIncor85JIn5WsE-bp64XuIEB3eks5spBvVgaJpZM4Lfoeb>.  
>

---
