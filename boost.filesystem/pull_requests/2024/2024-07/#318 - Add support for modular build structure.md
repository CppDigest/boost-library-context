# #318 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:26:23 UTC  
> Updated at: 2024-09-22 16:58:13 UTC  
> Merged at: 2024-09-22 16:57:37 UTC  
> Closed at: 2024-09-22 16:57:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:14:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/318#pullrequestreview-2190075986  

📁 build.jam

```diff
   8 |+ project /boost/filesystem
   9 |+     : common-requirements
  10 |+         <library>/boost/assert//boost_assert
```

> Username: Lastique  
> Created_at: 2024-07-20 23:11:55 UTC  
> Updated_at: 2024-07-20 23:14:13 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1685549464  

As for Boost.Atomic, shouldn't private and public dependencies be separate and specified for the library instead of the project?


📁 test/Jamfile.v2

```diff
  87 | run operations_unit_test.cpp : $(HERE) : : <link>shared <define>BOOST_FILESYSTEM_VERSION=4 <test-info>always_show_run_output ;
  88 |- run copy_test.cpp : : : <define>BOOST_FILESYSTEM_VERSION=4 ;
  88 |+ run copy_test.cpp : : : <define>BOOST_FILESYSTEM_VERSION=4 <source>/boost/exception//boost_exception ;
```

> Username: Lastique  
> Created_at: 2024-07-20 23:12:39 UTC  
> Updated_at: 2024-07-20 23:14:13 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1685549534  

Why are these `<source>` dependencies and not normal "linking" dependencies?


📁 test/deprecated_test.cpp

```diff
 122 | 
 123 |-     const fs::path temp_dir(fs::current_path() / ".." / fs::unique_path("deprecated_test-%%%%-%%%%-%%%%"));
 123 |+     const fs::path temp_dir(fs::temp_directory_path() / fs::unique_path("deprecated_test-%%%%-%%%%-%%%%"));
```

> Username: Lastique  
> Created_at: 2024-07-20 23:14:03 UTC  
> Updated_at: 2024-07-20 23:14:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1685549616  

Why is this change necessary?

> Username: grafikrobot  
> Created_at: 2024-07-26 18:19:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1693443788  

Not strictly needed. But without the change the test creates test files in your current dir, wherever that might be. And the `temp_dir` name seems to be a clue as to what was actually intended.

> Username: Lastique  
> Created_at: 2024-07-26 20:47:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1693592571  

> Not strictly needed. But without the change the test creates test files in your current dir, wherever that might be. And the `temp_dir` name seems to be a clue as to what was actually intended.  
  
That's how all Filesystem tests behave. If this is to be changed, other tests need to be changed as well.  
  
But since this is not related to modularization, please leave this out of this PR. You can create a separate PR, if you'd like this changed.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 15:56:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#issuecomment-2295309329  

Please review and merge this PR at your earliest convenience.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-08-18 23:18:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/318#pullrequestreview-2244430991  

📁 build/Jamfile.v2

```diff
 144 |+     /boost/scope//boost_scope
 145 |+     /boost/winapi//boost_winapi
 146 |+     ;
```

> Username: Lastique  
> Created_at: 2024-08-18 23:17:15 UTC  
> Updated_at: 2024-08-18 23:18:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1721084277  

Boost.Atomic and Boost.WinAPI are conditional dependencies. For Boost.Atomic, see `check-cxx20-atomic-ref` rule.

---

📁 build/Jamfile.v2

```diff
 187 |       <link>shared:<define>BOOST_FILESYSTEM_DYN_LINK=1
 188 |       <link>static:<define>BOOST_FILESYSTEM_STATIC_LINK=1
 189 |+       <define>BOOST_FILESYSTEM_NO_LIB=1
```

> Username: Lastique  
> Created_at: 2024-08-18 23:17:35 UTC  
> Updated_at: 2024-08-18 23:18:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1721084350  

Is this intentional?

> Username: grafikrobot  
> Created_at: 2024-08-19 00:14:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1721096014  

I don't think it is. I'll investigate.

> Username: grafikrobot  
> Created_at: 2024-08-19 00:46:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1721104593  

And I finally remembered the logic. Yes it's intentional. It turns off the auto linking. Which is not needed if one is using B2, or any other build system, as it will add in the appropriate link flags.


---

## Comment 4

> Username: Lastique  
> Created_at: 2024-09-17 12:53:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#issuecomment-2355680686  

If there's more work pending, please convert it to a draft until it is ready for review.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-09-17 13:14:42 UTC  
> Updated_at: 2024-09-17 13:15:28 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#issuecomment-2355754347  

> If there's more work pending, please convert it to a draft until it is ready for review.  
  
If you are asking about the new update.. Once in a while I need to merge/rebase the change to ensure any new changes in filesystem do not break this patch. But otherwise, the patch is ready to be applied. As you can see from all the checks passing.

---

## Review 6 [Commented]

> Username: Lastique  
> Created_at: 2024-09-17 14:17:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/318#pullrequestreview-2309910263  

📁 build.jam

```diff
  24 |+ explicit
  25 |+     [ alias boost_filesystem : build//boost_filesystem ]
  26 |+     [ alias all : boost_filesystem bug example test ]
```

> Username: Lastique  
> Created_at: 2024-09-17 14:17:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1763336811  

`bug` is not needed here. I believe, this is just a template for bug reports and not a real project.

> Username: grafikrobot  
> Created_at: 2024-09-22 16:54:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#discussion_r1770593702  

Sorry for the slow update.. busy week. That's done now.


---

## Comment 7

> Username: Lastique  
> Created_at: 2024-09-22 16:58:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/318#issuecomment-2366872937  

Thanks.

---
