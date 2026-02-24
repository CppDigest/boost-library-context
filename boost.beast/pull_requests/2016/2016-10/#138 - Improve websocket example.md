# #138 Improve websocket example [Closed]

> Username: seeekr  
> Created at: 2016-10-15 00:11:03 UTC  
> Updated at: 2017-07-25 17:44:17 UTC  
> Closed at: 2016-10-20 22:00:11 UTC  
> Url: https://github.com/boostorg/beast/pull/138  

This one I'm not expecting to be merged, but I wanted to bring this up anyway:  
  
Again, using CLion IDE, the IDE seems to get confused for whatever reason about the call to  
  
`boost::asio::buffer("Hello, world!")`  
  
and at least when we're talking about an example it might be a good thing to not trigger any error highlighting in popular IDE choices when we're expecting relative newcomers to jump in and try to load up and run a sample.  
  
I'm not comfortable enough with C++ to tell if wrapping the `const char*` in a `std::string()` is adding any overhead (and as such might teach someone out there looking at this code a bad trick), and in any case more code is worse if less code will produce the same result, so I'm not sure if this should be merged.  
  
Also, speaking of equal results: I noticed that while the compiler (clang++-3.8) compiles the example just fine as is, I'm getting a different output from running the program with vs without the `std::string()` wrapping -- some null character or so seems to be attached in the output in the example as is, while I'm getting no funny output in the wrapped version.  
  
Again, I'm a relative newcomer (actually "back-comer") to C++ development, so maybe this is a complete non-issue to every normal C++ dev out there.  
  
Cheers,  
Denis

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-10-15 00:17:24 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253948581  

Oh, that is very interesting! `"Hello, world!"` is getting interpreted as an array (char[14]) and the call to `boost::asio::buffer` is seeing the null terminator (since its the last element in the array). I agree with your change, and I did not see the null output during development probably because my IDE and console windows simply ignore it.  
  
But we also need to apply the change to the corresponding source file, its here:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_example.cpp  
  
Thanks!

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-10-15 00:27:09 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253949416  

[![Coverage Status](https://coveralls.io/builds/8348852/badge)](https://coveralls.io/builds/8348852)  
  
Coverage remained the same at 97.556% when pulling **68488e65763f4a7e13daa628595aefbb4637bb24 on seeekr:patch-2** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 3

> Username: codecov-io  
> Created_at: 2016-10-15 00:27:37 UTC  
> Updated_at: 2016-10-15 01:21:01 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253949455  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/138?src=pr) is 97.36% (diff: 100%)  
  
> Merging [#138](https://codecov.io/gh/vinniefalco/Beast/pull/138?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **0.19%**  
  
``` diff  
@@             master       #138   diff @@  
==========================================  
  Files            72         72            
  Lines          4091       4091            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
- Hits           3991       3983     -8     
- Misses          100        108     +8     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [01f939d...010eb8d](https://codecov.io/gh/vinniefalco/Beast/compare/01f939d68e2f6c21d41bfe5b787fbf586cdfc064...010eb8d9b5cbc91b17b4533d99eee920c8941ae2?src=pr)

---

## Comment 4

> Username: seeekr  
> Created_at: 2016-10-15 00:30:50 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253949762  

@vinniefalco Great! Happy to be of service then :)  
  
I've amended the commit, so both files are in sync now.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-10-15 00:34:30 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253950107  

Almost there... just a couple more things. Please merge the change from #137 into this commit, and update CHANGELOG.md. Or I can do it, up to you.

---

## Comment 6

> Username: seeekr  
> Created_at: 2016-10-15 00:40:01 UTC  
> Updated_at: 2016-10-15 00:40:19 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253950578  

Rebased on top of #137, closed the other one.  
EDIT: Looking into changelog.

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-10-15 00:44:03 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253950913  

[![Coverage Status](https://coveralls.io/builds/8348961/badge)](https://coveralls.io/builds/8348961)  
  
Coverage remained the same at 97.556% when pulling **4f5754c8786a1d4b6e0a463884b1e22b05f06efe on seeekr:patch-2** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 8

> Username: seeekr  
> Created_at: 2016-10-15 00:44:51 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253950989  

Updated changelog as well. I think you can squash the commits as you're merging the PR, right? If not I can also squash them for you if you prefer, which I think you do, judging from the commit history and how CHANGELOG.md is managed!

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-10-15 00:49:30 UTC  
> Updated_at: 2016-10-15 00:49:38 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253951370  

Thank you for your contribution! It will go in to b17, and look like this: https://github.com/vinniefalco/Beast/commit/010eb8d9b5cbc91b17b4533d99eee920c8941ae2

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2016-10-15 00:51:56 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253951600  

Looks good 👍

---

## Comment 11

> Username: seeekr  
> Created_at: 2016-10-15 00:52:42 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253951657  

Cool! Yeah, that looks nice!  
  
Thought b16 was representing the version currently being worked on as is often the custom with the version in the master branch :)  
  
Nice! Thanks for the work on & publishing of this library! Looking forward to putting it into production in the coming weeks! :)

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2016-10-15 00:54:24 UTC  
> Updated_at: 2016-10-15 00:54:30 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253951785  

Oh hmm...whenever I merge to master, I place a commit at the tip which sets a new version. This way, the tip of master always has a unique version number. Is it done differently in other projects?  
  
I appreciate the kind words!

---

## Comment 13

> Username: coveralls  
> Created_at: 2016-10-15 00:57:23 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253952009  

[![Coverage Status](https://coveralls.io/builds/8349051/badge)](https://coveralls.io/builds/8349051)  
  
Coverage remained the same at 97.556% when pulling **9edbb793a4a9d14cb935a50962c366c65aa319f3 on seeekr:patch-2** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 14

> Username: seelabs  
> Created_at: 2016-10-15 01:00:09 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253952179  

:+1: Good changes

---

## Comment 15

> Username: seeekr  
> Created_at: 2016-10-15 01:01:04 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253952240  

Ah, I see -- you're using the master branch as a place to keep the "latest released version", iiuc.  
The projects I'm frequenting most, as well as in my own projects, master is usually being used as the development version, where any feature branch or similar is merged into master as it is completed, and then at some point a release branch is cut based on master that goes into polishing mode with only fixes being applied to it and used as the base to cut further point releases which are continuously shipped to the customer. While at the same time work keeps going on master as usual.  
If that makes sense. So master is then potentially unstable at times, and eventually always branches out into release branches where most of the stabilization and maintenance work happens, being merged back regularly into master, of course.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2016-10-15 01:04:07 UTC  
> Updated_at: 2016-10-15 01:08:56 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253952447  

In the workflow we use, the **master** branch is always stable and always has a version at the tip. The **develop** branch gets batches of approved pull requests merged, and also has a version at the tip (but the version has b1, b2, etc... to represent that they are beta versions).  
  
Well, that's how it will be after Beast has an official 1.0.0 release. Since Beast is still in beta, we use treat the **master** branch as if it is the **develop** branch. But the tip of **master** is still relatively stable. Things only get merged to master after they pass review, tests, CI, and local inspection.

---

## Comment 17

> Username: coveralls  
> Created_at: 2016-10-15 01:06:50 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-253952601  

[![Coverage Status](https://coveralls.io/builds/8349053/badge)](https://coveralls.io/builds/8349053)  
  
Coverage remained the same at 97.556% when pulling **9edbb793a4a9d14cb935a50962c366c65aa319f3 on seeekr:patch-2** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2016-10-19 11:19:25 UTC  
> Url: https://github.com/boostorg/beast/pull/138#issuecomment-254784471  

This will go into **1.0.0-b17**

---
