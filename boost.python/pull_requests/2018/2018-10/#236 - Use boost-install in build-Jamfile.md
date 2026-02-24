# #236 Use boost-install in build/Jamfile [Merged]

> Username: pdimov  
> Created at: 2018-10-05 23:04:18 UTC  
> Updated at: 2018-10-12 14:06:50 UTC  
> Merged at: 2018-10-12 13:42:52 UTC  
> Closed at: 2018-10-12 13:42:52 UTC  
> Url: https://github.com/boostorg/python/pull/236  



---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-12 02:52:09 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429186934  

Ping?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-10-12 12:58:01 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429316488  

There is no comment in the patch and no description in this PR. Can you add a reference to some doc that describes what `boost-install` is actually doing ?

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-10-12 13:16:24 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429321996  

boost-install is defined in Jamroot, here: https://github.com/boostorg/boost/blob/master/Jamroot#L287  
  
What it does (at present) is create library-local `install` and `stage` targets, so that you can install or stage an individual library.  
  
That was preexisting functionality. A nice side effect of it is however that it declares which libraries your Jamfile "exports", that is, expects to be installed or staged, and how they are called.  
  
I am repurposing it as part of my attempt to automatically generate and install CMake config files for the installed libraries. So you do `boost-install boost_python` and I know that I need to generate boost_python.cmake in addition to installing boost_python.so.  
  
While I was at it I cleaned up the top-level install and stage so that now they do nothing but delegate to the library install and stage targets. When you do `b2 install --with-python`, it now does nothing cleverer than just `b2 libs/python/build//install`, and your install target (created by boost-install, but you could make your own if you like) takes it from there.  
  
And the libraries that lack stage/install targets break my current modified Boost, because `b2 install` says "no such target as `libs/python/build//install`, mate", which is why I'm pull requesting their being added. :-)

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-10-12 13:23:40 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429324196  

Thanks for the explanation. This is actually a good illustration why I'm so averse to the current discussion of moving from b2 to cmake:  
  
Ideally, I'd use a build tool that is stable and functional, so I can concentrate on the project itself (and the semantics of its build logic). Now I have to keep learning about changing tools and their idiosyncrasies, only because someone else decided I'd have to march in lock-step with >150 other projects.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-12 13:37:46 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429328435  

Delegating the work of installing/staging to individual libraries, rather than the superproject doing the work and special-casing this or that library, is what should have been done long time ago and is not that far from what you've been arguing for. So I'm not sure what your objections are here.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-10-12 13:42:43 UTC  
> Url: https://github.com/boostorg/python/pull/236#issuecomment-429329940  

I don't have any objection to this particular change. (Sorry if I gave that impression.) I'm just using this situation to illustrate my overall feeling towards this whole discussion.  
And yes, I agree that delegating tasks to individual libraries is a step in the right direction. So thanks for working on this !

---
