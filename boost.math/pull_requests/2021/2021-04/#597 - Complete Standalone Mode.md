# #597 Complete Standalone Mode [Merged]

> Username: mborland  
> Created at: 2021-04-02 11:52:38 UTC  
> Updated at: 2021-04-04 16:53:28 UTC  
> Merged at: 2021-04-04 16:35:10 UTC  
> Closed at: 2021-04-04 16:35:10 UTC  
> Url: https://github.com/boostorg/math/pull/597  

Completes the removal of dependencies. Adds standalone mode testing using the existing compile tests. Standalone mode testing is run using CMake and g++-10 on Linux.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-04-02 13:09:51 UTC  
> Url: https://github.com/boostorg/math/pull/597#issuecomment-812522535  

This is truly a heroic effort that is really important to the long term health of this project. Thanks!  
  
I think the next step is doing a release page? For example, [here](https://github.com/google/benchmark/releases) is google/benchmark's.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-02 13:42:05 UTC  
> Url: https://github.com/boostorg/math/pull/597#issuecomment-812536274  

> I think the next step is doing a release page? For example, [here](https://github.com/google/benchmark/releases) is google/benchmark's.  
  
That makes sense to me; creating a release [looks fairly straightforward.](https://docs.github.com/en/github/administering-a-repository/managing-releases-in-a-repository). I think it would be worthwhile to [add github pages](https://pages.github.com/) for the docs as well. Not sure how well boost book docs play with their blog software.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-04-04 09:40:47 UTC  
> Url: https://github.com/boostorg/math/pull/597#issuecomment-813003974  

@jzmaddock This is now clean on CI including [new standalone CI test](https://github.com/boostorg/math/pull/597/checks?check_run_id=2263364430). Is there anything else that you want to try and test besides the compile tests? I tried the examples, but did not want to clutter them with workarounds. Other than that should be good to go.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-04-04 16:34:36 UTC  
> Url: https://github.com/boostorg/math/pull/597#issuecomment-813061781  

> This is now clean on CI including new standalone CI test. Is there anything else that you want to try and test besides the compile tests?  
  
Nah, it's going to be tough to get too much more working without the rest of Boost.  One thing on my TODO list, is to do a quick audit of the #include tests, and make sure every header is actually covered, I have a hunch one or two newer ones may have slipped through...

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-04-04 16:35:03 UTC  
> Url: https://github.com/boostorg/math/pull/597#issuecomment-813061861  

Many many thanks for this Matt, this is a quite a milestone!!

---
