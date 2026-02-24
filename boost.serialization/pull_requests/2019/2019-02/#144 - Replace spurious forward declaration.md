# #144 Replace spurious forward declaration [Closed]

> Username: chhtz  
> Created at: 2019-02-07 12:26:26 UTC  
> Updated at: 2020-07-18 17:32:20 UTC  
> Closed at: 2020-07-18 17:32:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/144  

Previously the forward declaration referred to a non-existing `class U`.  
  
Note that the `class U` in the template parameter of the template parameter `SPT` was not the same as the `class U` in the `SPT<class U> &t`.  
  
This wrong forward declaration causes issues with g++7 and g++8 in c++17 mode:  
* https://stackoverflow.com/questions/54534047/eigen-matrix-boostserialization-c17  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=84075  
* http://eigen.tuxfamily.org/bz/show_bug.cgi?id=1676

---

## Comment 1

> Username: chhtz  
> Created_at: 2019-02-07 12:32:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-461401161  

I actually wonder, if this forward declaration ever had any influence on anything (except breaking compilation with g++7/g++8)

---

## Comment 2

> Username: robertramey  
> Created_at: 2019-03-01 22:42:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-468836660  

Searching the past reveals (168671) that this code was inserted in order to pass tests with msvc 8.0.  Have you tested your change with this compiler?  What tests have you run with change.    
"This wrong forward declaration causes issues with g++7 and g++8 in c++17 mode:".  What issues?  I'm not seeing them here with gcc 7.1 - whoops need to test with C++17

---

## Comment 3

> Username: chhtz  
> Created_at: 2019-03-02 19:02:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-468949919  

I did not test MSVC (since I don't have easy access to that). The patch does resolve the issue linked above (which actually seems to be a gcc bug). Also, the forward declaration seems in fact not have any effect at all on any gcc and clang version I tested (other than exposing the gcc bug in C++17 mode).

---

## Comment 4

> Username: chhtz  
> Created_at: 2019-03-09 22:35:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-471228487  

Do you need any additional information from me? I won't be able to verify or falsify whether this is necessary or breaks anything with MSVC. It seems the AppVeyor build fails in the main repository as well, so I don't think this patch introduces any bug.  
  
I could elaborate on why I believe the existing forward declaration does not make sense, if you want me to.  
  
From Eigen's side this PR is actually not urgent/necessary, since we worked around the issue in another way (see the links above if you are interested).

---

## Comment 5

> Username: robertramey  
> Created_at: 2019-03-11 15:40:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-471593649  

the problem is that if I don't re-test everything after every change we end up running in circles rather than moving forward. It ends up being a hugely time consuming game of whack-a-mole as "bug" reports come in whenever anything changes.   So I'm particularly reluctant to back out a work around which was likely the result of a very long back and forth with some user(s) of some obscure platform.  Of course this situation is made much, much worse by the fact that users depend upon being to load very old files created via serialization.  Depending upon the change, I'm reluctant to incorporate something which hasn't been vetted on MSVC as this is huge source of surprises whenever a change is made.  In practice I depend upon the boost test matrix.  That is run a change on my machine, then check it in to develop, watch the boost test matrix: https://www.boost.org/development/tests/develop/developer/serialization.html and then eventually merge it into master branch for release.   
  
Currently the boost test matrix doesn't test msvc 8.0 anymore.  So I might bite the bullet and check this in anyway.  Still I have to think about it and have to defer it until I have a little free time.  I'm leaving this open until I actually do something here.

---

## Comment 6

> Username: chhtz  
> Created_at: 2019-03-11 15:51:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-471599346  

Totally understand your point. As I said, for me this is not urgent, so feel free to get back to it whenever you find time. An option might be to `#if`-guard the change (but testing it with MSVC 8.0 sounds like the better option, otherwise this just hides/postpones the problem).  
  
Thanks for maintaining this library!

---

## Comment 7

> Username: robertramey  
> Created_at: 2020-07-18 00:40:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-660394434  

Note that test with gcc on Windows fails with this change.  So maybe the forward declaration is required after all.  You might want to investigate the specific failure.

---

## Comment 8

> Username: robertramey  
> Created_at: 2020-07-18 17:32:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/144#issuecomment-660515281  

note that incorporating your change make tests fail when built with the static version of the library.  This forward declaration was inserted to address some quirk in MSVC a long time back.  So it seems that MSVC still has some quirk which is addressed by this forward declaration.  Hence, we need to leave in.

---
