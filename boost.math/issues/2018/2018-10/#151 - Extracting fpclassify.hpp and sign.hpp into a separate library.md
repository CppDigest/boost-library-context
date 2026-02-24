# #151 - Extracting fpclassify.hpp and sign.hpp into a separate library? [Closed]

> Username: pdimov  
> Created at: 2018-10-01 12:24:38 UTC  
> Updated at: 2022-08-07 15:43:42 UTC  
> Closed at: 2022-08-07 15:43:42 UTC  
> Url: https://github.com/boostorg/math/issues/151  

At present there is a cyclic dependency between Math and LexicalCast, which it would be nice if we could break. LexicalCast only needs `fpclassify.hpp` and `sign.hpp`. If we could extract those to a new library, f.ex. `boostorg/float`, the cycle will be broken and it will be possible to work further towards reducing LexicalCast's dependencies.  
  
As a side effect, this will remove the dependency on Math of Random and Spirit, which also only need `fpclassify.hpp` and `sign.hpp`.  
  
Perhaps some other of the floating-point utility functions may also be candidates for migration; Convert, for instance, only needs `round`.  
  
https://pdimov.github.io/boostdep-report/develop/math.html#reverse-dependencies

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-02 12:02:50 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426247653  

If we do this, then:  
  
* After the next release.  
* It has to be a logical split, I'm not keen on just moving stuff around "just because", for example by splitting off all the utility support under file:///D:/data/boost/boost/libs/math/doc/html/utils.html  
* I'm pretty against changing the include structure and namespaces, but does this create symlink issues for bjam?  Yes I realise we can create forwarding headers and aliases, but that's not really the ideal solution IMO.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-02 15:38:28 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426321894  

For the logical split, I was indeed thinking that the utilities (`round`, `nextafter` and so on) were logical candidates to be extracted into a new library, them having std:: equivalents, but being unfamiliar with what Math currently provides, I may not have thought this through.  
  
We _can_ move headers around and bjam deals with it without issues - look at how we moved utility/declval.hpp to type_traits for instance.  
  
If we keep everything in `math::` the new library may be better called something like `stdmath`, I suppose.

---

## Comment 3

> Username: pabristow  
> Created at: 2018-10-03 15:11:33 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426674089  

I worry at the collateral damage to Math (and others) where there are hundreds of programs calling fpclassify etc, not to mention Boost users programs.  Any solution must avoid requiring modifications to these programs.

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-10-03 15:25:17 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426679287  

Isn't this why we have the deprecation warning macro? We could deprecate those files, and remove the dependency immediately. But there's no requirement to remove the deprecated files from boost.math.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-10-03 15:37:00 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426683813  

But we don't want to deprecate them; we just want libraries using `fpclassify` to not depend on the whole Math package, as they don't need all of it (and its dependencies).

---

## Comment 6

> Username: pabristow  
> Created at: 2018-10-03 15:39:39 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426684917  

Undestand and understood, but just checking ;-)

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-10-03 16:46:46 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426710716  

>Isn't this why we have the deprecation warning macro?  
  
No, as Peter says we are absolutely not deprecating them.  I also don't want folks to have to change their code just because we've decided to split the library purely for packaging purposes.  
  
The things I worry about:  
  
* If I want to report a bug on header X, where the heck do I find it?  Now I know (actually I do know - sort of) how to figure this out *if* you have the full git repro on your HD, but users who have the .zip version won't have that.  Quick quiz folks:  Given header boost/foo/xyz.hpp would you know how to find the repro that houses it?  
* This week we split off components X Y and Z, next week it's something else, the week after something different again.  I'm exaggerating of course, but sometimes it feels like that.  The ultimate destination is for each header to end up in it's own repro which is just silly.  A sensible packaging system would pull down the headers required for some component one at a time.  
* I'd like to keep history on the moved files if that's possible - I'm told it is, but don't understand how to do it :(

---

## Comment 8

> Username: pdimov  
> Created at: 2018-10-03 17:01:49 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-426715971  

1. Reporting the issues against Math instead of the new module won't be a problem as the same people would be maintaining both :-)  
2. Now that several package managers (conan, vcpkg) are actually available and work the same way, I think that it's high time for people to just accept that dependencies are computed on a module basis, not by a transitive include scan. This is just how it is. It's not something I've invented out of spite. :-)  
3. See https://svn.boost.org/trac10/wiki/NewLibByRefactor

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-11-26 21:09:20 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-558818012  

Was there any progress on the subject?  
  
Currently `sign.hpp` include brings bunch of mpl and std (algorithm, vector, complex).

---

## Comment 10

> Username: pdimov  
> Created at: 2022-08-07 08:47:40 UTC  
> Url: https://github.com/boostorg/math/issues/151#issuecomment-1207360247  

This should be addressed by https://github.com/boostorg/core/blob/develop/include/boost/core/cmath.hpp and can be closed.
