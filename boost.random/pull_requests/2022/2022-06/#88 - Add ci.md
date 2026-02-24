# #88 Add ci [Merged]

> Username: jzmaddock  
> Created at: 2022-06-01 18:58:43 UTC  
> Updated at: 2024-02-14 07:32:23 UTC  
> Merged at: 2022-06-04 08:20:34 UTC  
> Closed at: 2022-06-04 08:20:34 UTC  
> Url: https://github.com/boostorg/random/pull/88  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-06-01 19:00:13 UTC  
> Url: https://github.com/boostorg/random/pull/88#issuecomment-1144021017  

This builds on #83 by adding CI based on that used by Boost.Config.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-06-02 16:01:31 UTC  
> Url: https://github.com/boostorg/random/pull/88#issuecomment-1145033982  

OK, we now have pretty decent CI coverage in this PR, unless someone objects (@swatanabe ?) I'll probably go on ahead and merge.

---

## Comment 3

> Username: jwakely  
> Created_at: 2023-09-01 11:24:37 UTC  
> Url: https://github.com/boostorg/random/pull/88#issuecomment-1702596072  

Could the fixes for the tests please be merged to `master` so they make it into a release one day?

---

## Comment 4

> Username: jwakely  
> Created_at: 2023-09-04 12:52:46 UTC  
> Url: https://github.com/boostorg/random/pull/88#issuecomment-1705222806  

> Could the fixes for the tests please be merged to `master` so they make it into a release one day?  
  
This has to be patched in Fedora, because 1.81.0 was failing its CI tests and so could not be packaged:  
https://bodhi.fedoraproject.org/updates/FEDORA-2023-30e7874cdc  
https://bugzilla.redhat.com/show_bug.cgi?id=2231977  
https://bugzilla.redhat.com/show_bug.cgi?id=2231976

---

## Comment 5

> Username: mborland  
> Created_at: 2023-09-05 16:55:45 UTC  
> Url: https://github.com/boostorg/random/pull/88#issuecomment-1706977043  

> Could the fixes for the tests please be merged to `master` so they make it into a release one day?  
  
Yes. I'll make sure it makes the next release.

---
