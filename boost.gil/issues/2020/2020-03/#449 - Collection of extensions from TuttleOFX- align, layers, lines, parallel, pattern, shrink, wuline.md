# #449 - Collection of extensions from TuttleOFX: align, layers, lines, parallel, pattern, shrink, wuline [Open]

> Username: mloskot  
> Created at: 2020-03-14 23:49:58 UTC  
> Updated at: 2021-03-11 16:56:36 UTC  
> Url: https://github.com/boostorg/gil/issues/449  

There is a collection of interesting extensions (and their tests) in repo of TuttleOFX project which uses Boost.GIL:  
  
https://github.com/tuttleofx/TuttleOFX/tree/develop/libraries/boostHack/boost/gil/extension/toolbox  
  
Most of them have been authored by Tom Brinkman, [a known author of GIL extensions](https://stlab.adobe.com/gil/contribute.html).  
  
I think it is a good idea to review some of them and port/add to current Toolbox extension.

---

## Comment 1

> Username: meshtag  
> Created at: 2021-03-11 10:16:45 UTC  
> Updated at: 2021-03-11 10:17:35 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796627607  

I am working on this. This might be a trivial question but I would like to confirm whether I am allowed to do small modifications in the code. For example : replacing ```typedef``` with ```using``` for aliases in order to improve compatibility with C++ 11.

---

## Comment 2

> Username: mloskot  
> Created at: 2021-03-11 10:20:47 UTC  
> Updated at: 2021-03-11 10:22:00 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796630031  

You're free to do anything the licence allows you to do.  
  
GIL already can use C++14, see https://github.com/boostorg/gil/pull/526#issuecomment-724540995  
  
Yes, we prefer `using` aliases over `typedef` for aliases.  
  
------  
  
By the way, we also have an archive of old extensions https://github.com/boost-gil/gil-contributions-archive  
and it may be good idea to review if any of the TuttleOFX's ones are present there, and pick the latest version.

---

## Comment 3

> Username: meshtag  
> Created at: 2021-03-11 10:25:38 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796633331  

Alright, I will check that and submit a PR when I finish.

---

## Comment 4

> Username: mloskot  
> Created at: 2021-03-11 10:36:51 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796639870  

Awesome, thanks!

---

## Comment 5

> Username: meshtag  
> Created at: 2021-03-11 16:42:05 UTC  
> Updated at: 2021-03-11 16:42:25 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796875160  

@mloskot, the format of [test cases](https://github.com/tuttleofx/TuttleOFX/blob/develop/libraries/boostHack/boost/gil/extension/toolbox/aligned.tests.cpp) used by ```TuttleOFX``` is not in alignment with current practices followed by ```Boost Gil```. I was thinking of developing some simple test cases of my own and adding them in a similar fashion to what I did in ```gil/test/core/image_processing/morphology.cpp```.   
Will that be fine?

---

## Comment 6

> Username: mloskot  
> Created at: 2021-03-11 16:47:01 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796878830  

@meshtag They seem to be using Boost.Test while in GIL we use Boost.LightweightText   
Although developing more/new cases on your/our own is certainly a good idea - the more tests the better - I'd suggest you to try port the TuttleOFX's tests to Boost.LWT format. It should be feasible - in GIL we used to use Boost.Test and we ported all our tests to Boost.LWT, a while ago.

---

## Comment 7

> Username: meshtag  
> Created at: 2021-03-11 16:56:36 UTC  
> Url: https://github.com/boostorg/gil/issues/449#issuecomment-796885724  

Okay, I will try to do that.
