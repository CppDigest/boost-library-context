# #6 - Support for msvc-12.0 possible? [Closed]

> Username: pdimov  
> Created at: 2017-09-07 17:32:22 UTC  
> Updated at: 2017-10-21 09:28:04 UTC  
> Closed at: 2017-10-21 09:28:04 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/6  

In the course of implementing enhanced Appveyor support for the superproject, I saw that the library's tests fail on msvc-12.0, see  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.3615  
  
This is normal as it's not listed as supported. Most of the errors are just about `noexcept`, but not all.  
  
If msvc-12.0 is not going to be supported, we can exclude it from test/Jamfile; let me know and I'll submit a PR for that.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-09-07 18:00:42 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/6#issuecomment-327877260  

g++ 4.8.4 doesn't work either:  
  
https://travis-ci.org/boostorg/boost/jobs/272461384

---

## Comment 2

> Username: joaquintides  
> Created at: 2017-09-07 22:14:48 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/6#issuecomment-327941100  

El 07/09/2017 a las 19:32, Peter Dimov escribió:  
>  
> In the course of implementing enhanced Appveyor support for the   
> superproject, I saw that the library's tests fail on msvc-12.0, see  
>  
> https://ci.appveyor.com/project/boostorg/boost/build/1.0.3615  
>  
> This is normal as it's not listed as supported. Most of the errors are   
> just about |noexcept|, but not all.  
>  
> If msvc-12.0 is not going to be supported, we can exclude it from   
> test/Jamfile; let me know and I'll submit a PR for that.  
>  
  
I've taken a look and seems MSVC 12.0 is fubar (for instance, it doesn't   
support constpexr, which seems very hard to emulate), so,  
yes, please, marke it as unusable for poly_collection.  
  
Thank you,  
  
Joaquín M López Muñoz

---

## Comment 3

> Username: joaquintides  
> Created at: 2017-09-07 22:16:24 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/6#issuecomment-327941375  

El 07/09/2017 a las 20:04, Peter Dimov escribió:  
>  
> g++ 4.8.4 doesn't work either:  
>  
> https://travis-ci.org/boostorg/boost/jobs/272461384  
>  
  
I'm working on backporting the lib to GCC 4.8 and seems the goal is   
reachable; please  
allow me some more days before I come back to you with the final verdict.  
  
Joaquín M López Muñoz

---

## Comment 4

> Username: joaquintides  
> Created at: 2017-10-21 09:28:04 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/6#issuecomment-338378219  

[Backported to GCC4.8-9](https://travis-ci.org/boostorg/boost/builds/290571487), wasn't straightforward.
