# #62 trac-3606: fix remaining msvc /W4 complaints (msvc-10.0 and up) [Merged]

> Username: jeking3  
> Created at: 2017-12-27 15:11:36 UTC  
> Updated at: 2018-01-18 03:10:39 UTC  
> Merged at: 2018-01-18 03:10:36 UTC  
> Closed at: 2018-01-18 03:10:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/62  

MSVC fixes: https://svn.boost.org/trac10/ticket/3606  
MSVC fixes added a top level jamfile which turned on some additional warnings.  
  
GCC/CLANG fixes: https://svn.boost.org/trac10/ticket/9882 (-Wshadow, -Wimplicit-fallthrough)

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-12-31 18:12:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354617499  

What is the reason for the changes to variable names in this PR ?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-01-01 01:20:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354632000  

Variable shadowing warnings.  I'll fix the .gitignore.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-01-01 02:26:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354633587  

I do not think that changing the names of variables to avoid shadow warnings should ever be a programmable way of doing things. I would much rather have the warning turned off for the particular compiler, if we decide to do anything. If we start doing that there will be no end to changing library code just because some compiler gives a warning because the same variable name is used in different nested scopes. This is perfectly legal c++ and if no one needs to ever write code just to avoid such warnings.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-01-01 02:47:43 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354634081  

I disagree; I believe that shadowing warnings are valuable; they let the programmer know that he might not be referencing the variable that he expected to.  
  
Unlike the stupid "truncation - may cause loss of data" warning that MSVC has (which can DIAF), I think these are worth fixing.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-01-01 03:32:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354635017  

In what type of situations do these shadow warnings occur for which vc++ gives a warning ?

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-01-03 00:18:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-354910386  

Environmental issue; rebuilding with an empty rebase.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-01-12 02:28:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-357125299  

I've no way of telling if these are existing build issues or new ones... given the specific nature of this pull request I would say these platforms were already broken?

---

## Comment 8

> Username: eldiener  
> Created_at: 2018-01-12 07:17:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-357161335  

I am able to run the date_time tests against the current developer branch using mingw-64/gcc-6.3 x86_64 but with rev2 rather than rev1. This does not include this current PR. The test runs without errors for both c03 and c11 modes.  
  
I am able to run the date_time tests against the current developer branch using mingw-64/gcc-5.3 i686 rev 0. This does not include the current PR. The test runs with a single already well-known error in both c03 and c11 modes.  
  
It would be helpful for you to setup a local mingw-64 environment for running tests to match the Appveyor mingw tests. I would be glad to help you to do this if you had problems. That way you could see why the Appveyor tests have so many failures with your current PR fix. It may have to do with your modifications or it just may be some anomaly of the Appveyor testing environment.  
  
I have never run local cygwin tests but maybe someone else has.

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-01-12 12:43:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-357229167  

I have local build environments for all of these; I will look into it.  
I'm also going to refresh the other PRs to see if they fail the same way.  
Also, what's with the day-long delay on the Travis builds?

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-01-17 19:52:09 UTC  
> Url: https://github.com/boostorg/date_time/pull/62#issuecomment-358423313  

All right, everything passed.  Let me know if you want any more changes here.

---
