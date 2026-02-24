# #8 [WIP] Travis CI updates: [Closed]

> Username: sublimator  
> Created at: 2016-05-09 19:53:25 UTC  
> Updated at: 2016-05-11 10:11:17 UTC  
> Closed at: 2016-05-11 10:11:17 UTC  
> Url: https://github.com/boostorg/beast/pull/8  

- Use clang 3.8  
- Add msan/usan variants  
- Build only gcc.coverage and clang.{asan,usan,msan}  
- Make sure (correct) llvm-symbolizer is on PATH

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-05-09 21:36:59 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-217997609  

I don't understand what I'm looking at here. Are your changes in the commit entitled "Travis CI updates?" If so, has that commit changed from the branch you cloned? If the answer is yes, why is the version commit still marked "Verified?" Rebasing would invalidate the signature. I'm not sure what's being submitted here. If you changed the .travis.yml file it would be best if the change was in a single commit marked "[FOLD]...".

---

## Comment 2

> Username: sublimator  
> Created_at: 2016-05-09 21:56:21 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218002200  

It's [WIP] and based off your 'next' branch, which has some things disabled  
for speed (autobahn, valgrind)

---

## Comment 3

> Username: sublimator  
> Created_at: 2016-05-09 22:08:00 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218004841  

Wah, I see. I answered from mobile before. I dunno what happened here, and why it's showing all those commits of yours.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-05-09 22:45:00 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218012020  

I think I have it sorted, I believe this is the individual commit. Did you intend to keep the number of items in the matrix down to 4 for speed (it seems like a good idea)?  
https://github.com/vinniefalco/Beast/commits/niq-travis

---

## Comment 5

> Username: sublimator  
> Created_at: 2016-05-10 04:40:34 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218057512  

Yes, per your idea of only building the full matrix for non feature  
branches. Not sure best way to do that yet.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-05-10 23:28:09 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218321258  

This branch has your change:  
https://github.com/vinniefalco/Beast/commits/dstream  
  
I turned off memory sanitizer, there's no way to get it working without building boost and the stdlib with memory sanitization turned on.

---

## Comment 7

> Username: sublimator  
> Created_at: 2016-05-11 01:25:28 UTC  
> Url: https://github.com/boostorg/beast/pull/8#issuecomment-218338494  

I _think_ boost _was_ getting built with it turned, but will need to look  
into the stdlib

---
