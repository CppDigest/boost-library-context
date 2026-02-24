# #331 Add visibility feature [Merged]

> Username: Lastique  
> Created at: 2018-08-19 17:00:48 UTC  
> Updated at: 2021-10-02 22:08:10 UTC  
> Merged at: 2018-08-19 19:06:49 UTC  
> Closed at: 2018-08-19 19:06:49 UTC  
> Url: https://github.com/boostorg/build/pull/331  

The new visibility feature can be used to specify default symbol visibility  
on compilers and platforms that support it. The default visibility is  
hidden, unlike most compilers' defaults. This is intentional so that all  
Boost libraries are compiled with hidden visibility by default. Other modes  
are: protected and global (the latter is called "default" in gcc documentation).  
  
I've only tested it on Linux, with gcc and clang.  
  
The discussion that led to this PR is here:  
  
http://boost.2283326.n4.nabble.com/all-Request-for-out-of-the-box-visibility-support-tt4704134.html

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2018-08-19 17:10:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/331#pullrequestreview-147465039  

📁 src/tools/features/visibility-feature.jam

```diff
   7 |+ 
   8 |+ feature.feature visibility
   9 |+     : hidden protected global
```

> Username: grafikrobot  
> Created_at: 2018-08-19 17:09:27 UTC  
> Updated_at: 2018-08-19 17:44:22 UTC  
> Url: https://github.com/boostorg/build/pull/331#discussion_r211109041  

Setting the default to be different than the for compilers will affect everyone, not jus boost builds. Please change the default to match the compiler defaults. If boost wants a different kind of build it can set the feature in the appropriate jamfiles.

> Username: grafikrobot  
> Created_at: 2018-08-19 17:39:31 UTC  
> Updated_at: 2018-08-19 17:44:22 UTC  
> Url: https://github.com/boostorg/build/pull/331#discussion_r211109784  

Additionally, this needs to be an `optional` feature so that the compile option doesn't appear when not specified. I.e. which should be the default.


---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2018-08-19 17:40:43 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/331#pullrequestreview-147465798  

📁 src/tools/features/visibility-feature.jam

```diff
   8 |+ feature.feature visibility
   9 |+     : global protected hidden
  10 |+     : propagated ;
```

> Username: grafikrobot  
> Created_at: 2018-08-19 17:40:38 UTC  
> Updated_at: 2018-08-19 17:44:22 UTC  
> Url: https://github.com/boostorg/build/pull/331#discussion_r211109809  

Additionally, this needs to be an optional feature so that the compile option doesn't appear when not specified. I.e. which should be the default.


---

## Comment 3

> Username: Lastique  
> Created_at: 2018-08-19 17:47:40 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-414143695  

I've updated the feature and the docs according the comments, although it sort of defeats the purpose. We could modify library jamfiles before, the point was to make hidden visibility the default so that we don't have to do that. At least the property offers a more portable way to do that.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-08-19 17:52:19 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-414143968  

AMDG  
  
On 08/19/2018 11:47 AM, Andrey Semashev wrote:  
> I've updated the feature and the docs according the comments, although it sort of defeats the purpose. We could modify library jamfiles before, the point was to make hidden visibility the default so that we don't have to do that. At least the property offers a more portable way to do that.  
>   
>   
  
It can be set in the Boost Jamroot:  
  
project /boost : ... : default-build <visibility>hidden ;  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-08-19 18:00:02 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-414144398  

Thanks, Steven, that sounds like a good idea. I'll create a PR for the superproject if this PR is accepted.

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-08-19 19:47:12 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-414151224  

The related superproject PR is https://github.com/boostorg/boost/pull/190.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2018-09-21 05:54:47 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-423421767  

Please merge to master. Used that feature in tests, now they fail on master (but do work perfectly on develop)

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2018-09-21 12:25:42 UTC  
> Url: https://github.com/boostorg/build/pull/331#issuecomment-423513820  

Already done in https://github.com/boostorg/build/commit/aa73dbc9ccd360703f2a56001df48c26b8e5b1bf

---
