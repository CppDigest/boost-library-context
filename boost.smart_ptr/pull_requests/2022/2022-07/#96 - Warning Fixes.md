# #96 Warning Fixes [Closed]

> Username: mborland  
> Created at: 2022-07-16 19:27:36 UTC  
> Updated at: 2024-12-14 17:24:50 UTC  
> Closed at: 2024-12-14 14:43:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96  

Primarily replacing 0 with `BOOST_NULLPTR`, and a few other minor ones throughout the tests.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-07-16 19:28:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1186272769  

@pdimov Can you please kick off the CI run for this?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-07-17 07:43:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1186432604  

This has too many unrelated changes in a single PR, sorry. Some of these are outright wrong; the self-assignments are there because _they test that self-assignment works_.  
  
What problem (for you, or for users) is this PR trying to solve?

---

## Comment 3

> Username: mborland  
> Created_at: 2022-07-19 16:14:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1189276519  

> This has too many unrelated changes in a single PR, sorry. Some of these are outright wrong; the self-assignments are there because _they test that self-assignment works_.  
>   
> What problem (for you, or for users) is this PR trying to solve?  
  
I can cut out everything that is outside of the include directory. The changes to test files would be immaterial to users anyway; they helped me to root out the actual warnings.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-08-04 01:47:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1204664789  

@pdimov I apologize for the delay in correcting this PR. Can you please kick off the CI run?

---

## Comment 5

> Username: mborland  
> Created_at: 2022-08-07 16:26:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1207441682  

@pdimov The CI run on this is clean. Let me know if you need any additional changes.

---

## Review 6 [Changes requested]

> Username: glenfe  
> Created_at: 2022-08-07 17:12:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/smart_ptr/pull/96#pullrequestreview-1064448651  

📁 test/weak_ptr_test.cpp

```diff
 803 |     {
 804 |-         boost::shared_ptr<X> sp(static_cast<X*>(0));
 804 |+         boost::shared_ptr<X> sp(static_cast<X*>(BOOST_NULLPTR));
```

> Username: glenfe  
> Created_at: 2022-08-07 17:10:23 UTC  
> Updated_at: 2022-08-07 17:12:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#discussion_r939697030  

static_cast<T*>(0) would never trigger the warning, even with -Wzero-as-null-pointer-constant


📁 include/boost/smart_ptr/detail/shared_count.hpp

```diff
 629 |+             {
 630 |+                 pi_->weak_release();
 631 |+             }
```

> Username: glenfe  
> Created_at: 2022-08-07 17:12:03 UTC  
> Updated_at: 2022-08-07 17:12:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#discussion_r939697223  

Leave new brackets or whitespace changes out of it


---

## Comment 7

> Username: mborland  
> Created_at: 2022-08-27 16:10:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/96#issuecomment-1229220174  

Ping @pdimov and @glenfe

---
