# #80 [WIP] Just move non-variadics into its own cpp03 dir. [Merged]

> Username: Flast  
> Created at: 2015-06-15 23:51:43 UTC  
> Updated at: 2015-06-17 00:55:03 UTC  
> Merged at: 2015-06-15 23:53:27 UTC  
> Closed at: 2015-06-15 23:53:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/80  

As I mentioned in https://github.com/boostorg/fusion/pull/57#issuecomment-112237090 .  
  
After this, #57 will be more clean like this, https://github.com/Flast/fusion/compare/78afb4d3fc037dc9d688777c27a0bed56d94b9d7...736cce8a5647252df7d0f710caaba4e534c7291d .  
  
Warning: Currently, this PR **doesn't** take care about include paths. Hang on until I pushing a commit to fix.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-06-15 23:53:24 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112239785  

Yes! :-)

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-06-16 19:40:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112543149  

I noticed that this change broke one of the Geometry tests: [test/geometries/adapted.cpp](https://github.com/boostorg/geometry/blob/develop/test/geometries/adapted.cpp), in the [regression matrix](http://www.boost.org/development/tests/develop/developer/geometry.html) called `geometries_boost_fusion`, see [this one](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-geometry-test-geometries-geometries_boost_fusion-test-msvc-10-0-dbg-adrs-mdl-64-async-excpt-on-thrd-mlt.html) for instance. The fragment of the error message is:  
  
```  
D:\t08\run\boost_root\boost/fusion/container/list/cons_iterator.hpp(84) : error C2065: 'list' : undeclared identifier  
...  
D:\t08\run\boost_root\boost/fusion/container/list/cons_iterator.hpp(93) : error C2065: 'list' : undeclared identifier  
...  
D:\t08\run\boost_root\boost/fusion/container/list/cons_iterator.hpp(106) : error C2065: 'Cons' : undeclared identifier  
...  
```  
  
Is it an intended regression or a bug in Fusion? If the former is true then how should the test be rewritten?

---

## Comment 3

> Username: vtnerd  
> Created_at: 2015-06-16 19:53:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112549899  

This is a WIP, and will hopefully be fixed with [this pull request](https://github.com/boostorg/fusion/pull/81).

---

## Comment 4

> Username: mjcaisse  
> Created_at: 2015-06-16 20:24:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112558220  

In the future we should probably make a feature branch from develop and use that for long running WIP. That allows manageable PR's for review and doesn't break development builds. Once the long running development is complete and stable it can be merged to the develop branch.

---

## Comment 5

> Username: vtnerd  
> Created_at: 2015-06-16 20:37:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112561450  

Is it possible to run the automated tests for these feature branches? I guess each project could set this up independently, but having the test matrix automatically track "feature/xxx" would be nice since it tests on more platforms than the usual free-online tools. This might create too much overhead for that system though.

---

## Comment 6

> Username: mjcaisse  
> Created_at: 2015-06-16 20:42:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112563530  

Our Bamboo servers can detect the feature branches and run the tests. This isn't helpful to the community _yet_ but I hope it will be soon.

---

## Comment 7

> Username: djowel  
> Created_at: 2015-06-16 22:01:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112581475  

Yes, agreed on all counts. We cannot destabilise devel. I assumed that this PR did not break anything. My bad. I missed the Warning from Flast.

---

## Comment 8

> Username: Flast  
> Created_at: 2015-06-17 00:45:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112614571  

> This is a WIP, and will hopefully be fixed with this pull request.  
  
@vtnerd , Thank you your follow-up.  
  
> Yes, agreed on all counts. We cannot destabilise devel. I assumed that this PR did not break anything.   
  
@djowel , if WIP PR misleads you, I  won't open WIP PR.

---

## Comment 9

> Username: djowel  
> Created_at: 2015-06-17 00:48:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112615015  

@Flast , it's my bad. anyway, i think we should do as michael suggests using feature branches. for immediate WIP PRs, is it not possible to have it in such a way as not to break the build?

---

## Comment 10

> Username: Flast  
> Created_at: 2015-06-17 00:55:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/80#issuecomment-112616012  

@djowel , I agreed.

---
