# #69 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:29:41 UTC  
> Updated at: 2024-10-10 15:33:25 UTC  
> Merged at: 2024-10-10 15:31:26 UTC  
> Closed at: 2024-10-10 15:31:26 UTC  
> Url: https://github.com/boostorg/atomic/pull/69  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:04:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/69#pullrequestreview-2190075255  

📁 build.jam

```diff
  15 |+         <library>/boost/preprocessor//boost_preprocessor
  16 |+         <library>/boost/type_traits//boost_type_traits
  17 |+         <library>/boost/winapi//boost_winapi
```

> Username: Lastique  
> Created_at: 2024-07-20 23:04:32 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1685548722  

`winapi` is only needed on Windows.  
  
Also, shouldn't the public and private dependencies be specified separately? And for the library itself rather than the project?

> Username: grafikrobot  
> Created_at: 2024-07-20 23:30:06 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1685551196  

Those are the public dependencies. As what is listed in the dependency report (barring "recent" changes).

> Username: grafikrobot  
> Created_at: 2024-07-20 23:31:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1685551342  

https://pdimov.github.io/boostdep-report/develop/atomic.html

> Username: Lastique  
> Created_at: 2024-07-20 23:48:23 UTC  
> Updated_at: 2024-07-20 23:49:04 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1685552960  

That report doesn't separate public and private dependencies, it lists everything that is used in headers and source files together.  
  
You can use `CMakeLists.txt` to deduce the actual public and private dependencies. Boost.Build should work equivalently.

> Username: grafikrobot  
> Created_at: 2024-07-24 04:21:52 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1689093610  

> That report doesn't separate public and private dependencies, it lists everything that is used in headers and source files together.  
  
Ah, right. That does differ for header-only vs build-able libs.  
  
> You can use `CMakeLists.txt` to deduce the actual public and private dependencies. Boost.Build should work equivalently.  
  
I'll make a note to adjust the list to reflect requirements vs. usage-requirements in such cases.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:00:32 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#issuecomment-2295310358  

Please review and merge this PR at your earliest convenience.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-08-18 22:54:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/69#pullrequestreview-2244426470  

📁 build/Jamfile.v2

```diff
  63 |       <link>shared:<define>BOOST_ATOMIC_DYN_LINK=1
  64 |       <link>static:<define>BOOST_ATOMIC_STATIC_LINK=1
  65 |+       <define>BOOST_ATOMIC_NO_LIB=1
```

> Username: Lastique  
> Created_at: 2024-08-18 22:51:00 UTC  
> Updated_at: 2024-08-18 22:54:28 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1721080034  

Is this intentional? Do we no longer support auto-linking with b2?

> Username: grafikrobot  
> Created_at: 2024-08-19 00:46:37 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1721104670  

And I finally remembered the logic. Yes it's intentional. It turns off the auto linking. Which is not needed if one is using B2, or any other build system, as it will add in the appropriate link flags.


📁 build.jam

```diff
  11 |+     /boost/predef//boost_predef
  12 |+     /boost/type_traits//boost_type_traits
  13 |+     /boost/winapi//boost_winapi ;
```

> Username: Lastique  
> Created_at: 2024-08-18 22:53:42 UTC  
> Updated_at: 2024-08-18 22:54:28 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1721080348  

Boost.WinAPI is a conditional dependency, it is present only on Windows.

> Username: grafikrobot  
> Created_at: 2024-08-20 18:14:53 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1723745566  

FYI.. I'm working out changes to support that being optional.


---

## Comment 4

> Username: Lastique  
> Created_at: 2024-10-10 15:16:28 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#issuecomment-2405410608  

If there are still changes expected here, please, convert it to a draft.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-10-10 15:23:12 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#issuecomment-2405426278  

> If there are still changes expected here, please, convert it to a draft.  
  
No changes expected here.

---

## Comment 6

> Username: Lastique  
> Created_at: 2024-10-10 15:31:35 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#issuecomment-2405446226  

Thanks.

---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2024-10-10 15:33:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/69#pullrequestreview-2360748180  

📁 build/Jamfile.v2

```diff
  22 |- project.pop-current ;
  23 |- 
  20 | lib synchronization ;
```

> Username: Lastique  
> Created_at: 2024-10-10 15:33:24 UTC  
> Updated_at: 2024-10-10 15:33:25 UTC  
> Url: https://github.com/boostorg/atomic/pull/69#discussion_r1795686229  

@grafikrobot BTW, should this be `searched-lib`? I noticed, you changed `lib` to `searched-lib` in other libraries. What's the difference?


---
