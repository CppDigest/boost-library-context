# #227 Fixed warning cast between incompatible function types from FARPROC_ to gettickcount64_t [Merged]

> Username: kivadiu  
> Created at: 2018-08-08 19:55:07 UTC  
> Updated at: 2018-08-09 20:53:01 UTC  
> Merged at: 2018-08-09 20:52:07 UTC  
> Closed at: 2018-08-09 20:52:07 UTC  
> Url: https://github.com/boostorg/thread/pull/227  

On Windows, GetProcAddress must be cast to something useful which  
generates a warning with gcc. This patch silents the warning.  
  
Fixes issue #225

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2018-08-09 07:40:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/227#pullrequestreview-144722442  

📁 src/win32/thread_primitives.cpp

```diff
 105 |+       // However, gcc warns about that.
 106 |+ #if BOOST_COMP_GNUC
 107 |+ #pragma GCC diagnostic push
```

> Username: Lastique  
> Created_at: 2018-08-09 07:40:42 UTC  
> Updated_at: 2018-08-09 08:16:19 UTC  
> Url: https://github.com/boostorg/thread/pull/227#discussion_r208833003  

Pragma diagnostic push/pop appeared only since gcc 4.6. You should check for the compiler version.  
  
Also, you don't need Boost.Predef to detect gcc. You can use `BOOST_GCC` from Boost.Config. Also, it is quite possible that clang also emits a similar warning, you can check for it with `BOOST_CLANG`.

> Username: kivadiu  
> Created_at: 2018-08-09 08:06:06 UTC  
> Updated_at: 2018-08-09 08:16:19 UTC  
> Url: https://github.com/boostorg/thread/pull/227#discussion_r208839576  

I cannot check for clang as I do not cross-compile to Windows with clang but I will use BOOST_GCC.


---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2018-08-09 07:45:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/227#pullrequestreview-144723691  

📁 src/win32/thread_primitives.cpp

```diff
 106 |+ #if BOOST_COMP_GNUC
 107 |+ #pragma GCC diagnostic push
 108 |+ #pragma GCC diagnostic ignored "-Wcast-function-type"
```

> Username: Lastique  
> Created_at: 2018-08-09 07:45:02 UTC  
> Updated_at: 2018-08-09 08:16:19 UTC  
> Url: https://github.com/boostorg/thread/pull/227#discussion_r208834039  

This warning is only documented (and likely implemented) since gcc 8.

> Username: kivadiu  
> Created_at: 2018-08-09 08:04:18 UTC  
> Updated_at: 2018-08-09 08:16:19 UTC  
> Url: https://github.com/boostorg/thread/pull/227#discussion_r208839075  

That's right, thanks. I will change that.


---

## Comment 3

> Username: kivadiu  
> Created_at: 2018-08-09 10:07:44 UTC  
> Url: https://github.com/boostorg/thread/pull/227#issuecomment-411707306  

I am very surprised by test failures in completely unrelated code.  
For example, it fails with msvc-11 (but not with 9, 10 and 12) at [test/threads/thread/members/try_join_for_pass.cpp:147](https://ci.appveyor.com/project/viboes/thread/build/1.0.263-develop/job/tg32bf3yxrq4e5xl):  
```  
boost::thread t0( (G()));  
BOOST_TEST(t0.joinable());  
t0.try_join_for(boost::chrono::milliseconds(250));  
try  
{  
  t0.join();  
  BOOST_TEST(false); // line 147: test failure here  
}  
catch (boost::system::system_error& e)  
{  
  BOOST_TEST(e.code().value() == boost::system::errc::invalid_argument);  
}  
```  
  
Is it possible that the test server is so heavily loaded that 250 ms are not enough and `t0.join()` does not fail as expected?

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-08-09 14:07:34 UTC  
> Url: https://github.com/boostorg/thread/pull/227#issuecomment-411770315  

It is possible, as with any other time-based test.

---

## Comment 5

> Username: viboes  
> Created_at: 2018-08-09 15:50:36 UTC  
> Url: https://github.com/boostorg/thread/pull/227#issuecomment-411805638  

Andrey is right. This kind of tests can unfortunately fail.

---

## Comment 6

> Username: kivadiu  
> Created_at: 2018-08-09 18:02:54 UTC  
> Url: https://github.com/boostorg/thread/pull/227#issuecomment-411845439  

How do you normally decide if a patch is harmless then?

---

## Comment 7

> Username: viboes  
> Created_at: 2018-08-09 20:53:01 UTC  
> Url: https://github.com/boostorg/thread/pull/227#issuecomment-411893318  

I need to check the kind of issue :(

---
