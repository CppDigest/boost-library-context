# #188 Big HTTP renaming [Closed]

> Username: vinniefalco  
> Created at: 2016-11-14 20:53:31 UTC  
> Updated at: 2017-07-25 17:44:17 UTC  
> Closed at: 2016-11-15 17:03:02 UTC  
> Url: https://github.com/boostorg/beast/pull/188  

Cosmetic changes only. Documentation is improved and updated.  
  
Doc preview:  
http://vinniefalco.github.io/stage/beast/b20/index.html  
  
In particular:  
http://vinniefalco.github.io/stage/beast/b20/beast/http/message.html

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-11-14 20:57:28 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260460234  

Reviewers: @HowardHinnant @scottschurr

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-11-14 21:07:57 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260462915  

[![Coverage Status](https://coveralls.io/builds/8819429/badge)](https://coveralls.io/builds/8819429)  
  
Coverage remained the same at 97.957% when pulling **15cf021759c8c947cd1b6898f081c883875ebe71 on b20** into **79be7f8b395a2d218f2f74fda3a85a222eb61a1d on master**.

---

## Comment 3

> Username: codecov-io  
> Created_at: 2016-11-14 21:12:09 UTC  
> Updated_at: 2016-11-15 14:10:00 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260463979  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/188?src=pr) is 97.95% (diff: 99.07%)  
  
> Merging [#188](https://codecov.io/gh/vinniefalco/Beast/pull/188?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master       #188   diff @@  
==========================================  
  Files            73         74     +1     
  Lines          4357       4357            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           4268       4268            
  Misses           89         89            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [79be7f8...6f2a1bd](https://codecov.io/gh/vinniefalco/Beast/compare/79be7f8b395a2d218f2f74fda3a85a222eb61a1d...6f2a1bde06cebfea85c8fc1cb905330cca8077b4?src=pr)

---

## Review 4 [Commented]

> Username: scottschurr  
> Created_at: 2016-11-15 01:18:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/188#pullrequestreview-8510382  

Just for my own information, I'm hoping some of these files are generated?  If so, which are those and I won't bother trying to read them.

📁 doc/design.qbk

```diff
 457 |-     a template argument. Use the [*DynamicBuffer] that comes with beast,
 456 |+     __basic_streambuf__ provided by beast supports standard allocators through
 457 |+     a template argument. Use the __streambuf__ that comes with beast,
```

> Username: scottschurr  
> Created_at: 2016-11-14 23:37:41 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r87918981  

I suspect `__streambuf__` -> `__DynamicBuffer__` in this case.

> Username: vinniefalco  
> Created_at: 2016-11-15 13:43:26 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r88017754  

will change

---

📁 doc/design.qbk

```diff
 459 |-     meets the
 460 |-     [@https://github.com/vinniefalco/Beast/blob/6c8b4b2f8dde72b01507e4ac7fde4ffea57ebc99/include/beast/basic_streambuf.hpp#L21 concept requirements].
 459 |+     meets the requirements of __DynamicBuffer__.
```

> Username: scottschurr  
> Created_at: 2016-11-14 23:41:09 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r87919478  

`__DynamicBuffer__` -> `__basic_streambuf__` here?

> Username: vinniefalco  
> Created_at: 2016-11-15 13:43:22 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r88017741  

No, DynamicBuffer is the concept. basic_streambuf is a type.


---

## Review 5 [Approved]

> Username: scottschurr  
> Created_at: 2016-11-15 02:41:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/188#pullrequestreview-8521865  

I spotted what may be a few typos in comments that you may choose to address.  I did not review `quickref.xml` under the assumption that it was generated.  
  
In general I think switching from `headers` to a header that contains fields will help considerably with the understandability of the code.  Thanks.  
  
:+1:

📁 include/beast/http/basic_fields.hpp

```diff
  27 |-     up the headers and trailers in a HTTP message. Objects of this type
  27 |+     up the fields and trailers in a HTTP message. Objects of this type
  28 |     are iterable, which each element holding the field name and field
```

> Username: scottschurr  
> Created_at: 2016-11-15 01:23:25 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r87931406  

`which` -> `with`?

> Username: vinniefalco  
> Created_at: 2016-11-15 13:44:01 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r88017898  

will fix


📁 include/beast/http/message.hpp

```diff
 377 | #if GENERATING_DOCS
 371 |- /** Swap two HTTP message headers.
 378 |+ /** Swap two header objectsd.
```

> Username: scottschurr  
> Created_at: 2016-11-15 02:10:57 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r87936150  

`objectsd` -> `objects`?

> Username: vinniefalco  
> Created_at: 2016-11-15 13:44:12 UTC  
> Updated_at: 2016-11-15 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/188#discussion_r88017946  

will fix


---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-11-15 12:00:14 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260623198  

quickref.xml is maintained by hand. None of the files in doc/ are automatically generated

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-11-15 13:59:51 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260648230  

[![Coverage Status](https://coveralls.io/builds/8832530/badge)](https://coveralls.io/builds/8832530)  
  
Coverage remained the same at 97.957% when pulling **6f2a1bde06cebfea85c8fc1cb905330cca8077b4 on b20** into **79be7f8b395a2d218f2f74fda3a85a222eb61a1d on master**.

---

## Comment 8

> Username: HowardHinnant  
> Created_at: 2016-11-15 16:57:58 UTC  
> Url: https://github.com/boostorg/beast/pull/188#issuecomment-260699603  

👍  Looks good to me.  Passes tests on macOS.  We get warnings coming out of asio, but there's not much we can do about that.

---
