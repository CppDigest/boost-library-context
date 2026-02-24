# #699 - The 'cflags' in the msvc toolset meaning has changed [Closed]

> Username: eldiener  
> Created at: 2021-01-05 22:00:07 UTC  
> Updated at: 2021-01-05 23:30:49 UTC  
> Closed at: 2021-01-05 22:10:36 UTC  
> Url: https://github.com/boostorg/build/issues/699  

The 'cflags' setting in the msvc toolset has been changed to apply only to .c files. Previously cflags applied to both .c and .cpp files. Is this an error or has this changed been made purposely ?

---

## Comment 1

> Username: eldiener  
> Created at: 2021-01-05 22:10:36 UTC  
> Url: https://github.com/boostorg/build/issues/699#issuecomment-754932947  

Never mind ! I see where the change was made in the docs. That is quite a breaking change. Maybe a little heads up about this change should have been in order.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2021-01-05 23:30:49 UTC  
> Url: https://github.com/boostorg/build/issues/699#issuecomment-754964252  

Sorry about that.. I've been trying to fix a slew of reported bugs. And that was one of them. This kind of situation will be less chaotic once main B2 development moves to a different org. As the updates from the POV of Boost will be more stable.
