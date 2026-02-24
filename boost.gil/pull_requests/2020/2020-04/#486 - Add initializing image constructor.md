# #486 Add initializing image constructor [Merged]

> Username: sdebionne  
> Created at: 2020-04-20 16:50:59 UTC  
> Updated at: 2020-04-27 07:41:33 UTC  
> Merged at: 2020-04-24 10:24:59 UTC  
> Closed at: 2020-04-24 10:24:59 UTC  
> Url: https://github.com/boostorg/gil/pull/486  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Add a new constructor to `any_image`.  
  
### References  
  
Fixed #453   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-04-21 18:42:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/486#pullrequestreview-397577210  

I noticed it's marked as draft. Is it work in progress?  
  
The change is trivial, but would be nice to have a minimal test case.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2020-04-22 07:51:02 UTC  
> Url: https://github.com/boostorg/gil/pull/486#issuecomment-617613514  

Yes it was a draft until I added a minimal test case.

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2020-04-22 19:53:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/486#pullrequestreview-398529581  

This looks good, I just have to ask for the tiny update in `Jamfile` and the copyright update.

📁 test/extension/dynamic_image/CMakeLists.txt

```diff
   8 | message(STATUS "Boost.GIL: Configuring tests in test/extension/dynamic_image")
   9 | foreach(_name
  10 |+   any_image
```

> Username: mloskot  
> Created_at: 2020-04-22 19:49:59 UTC  
> Updated_at: 2020-04-23 06:43:13 UTC  
> Url: https://github.com/boostorg/gil/pull/486#discussion_r413275993  

Could you also add `run any_image.cpp ;` above this line in the `Jamfile`?  
  
https://github.com/boostorg/gil/blob/9d0d22638eacbe6a7a6a8404c4586e572c6807fe/test/extension/dynamic_image/Jamfile#L13

> Username: sdebionne  
> Created_at: 2020-04-23 07:46:01 UTC  
> Url: https://github.com/boostorg/gil/pull/486#discussion_r413588272  

Done


📁 include/boost/gil/extension/dynamic_image/any_image.hpp

```diff
 103 |+     
 104 |+     template <typename Image>
 105 |+     any_image(Image&& img) : parent_t(std::move(img)) {}
```

> Username: mloskot  
> Created_at: 2020-04-22 19:52:08 UTC  
> Updated_at: 2020-04-23 06:43:13 UTC  
> Url: https://github.com/boostorg/gil/pull/486#discussion_r413278416  

By the way, I forgot to ask about this around 601790f2419c90b836dc3cfa7bb824347b1a2369, please add yourself copyright to the copyright notice

> Username: sdebionne  
> Created_at: 2020-04-23 07:46:18 UTC  
> Updated_at: 2020-04-23 07:46:19 UTC  
> Url: https://github.com/boostorg/gil/pull/486#discussion_r413588500  

Done


---
