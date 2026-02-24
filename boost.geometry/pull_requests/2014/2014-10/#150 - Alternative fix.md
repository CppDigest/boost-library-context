# #150 [algorithms][for_each] Alternative fix [Merged]

> Username: mkaravel  
> Created at: 2014-10-01 06:33:22 UTC  
> Updated at: 2014-10-20 05:29:41 UTC  
> Merged at: 2014-10-02 10:33:47 UTC  
> Closed at: 2014-10-02 10:33:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/150  

This is a follow up of PR #147 and PR #149.  
  
Fix bug in for_each_segment for open geometries without using the closeable_view (so that mutable geometries can also be supported).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-10-01 08:43:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/150#issuecomment-57435031  

Is there a unit test (I believe I 've seen that question somewhere) for open ranges? Can you add one?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-01 10:29:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/150#issuecomment-57445330  

Yes, I will. So please wait until the unit test is added to this PR before merging.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-10-02 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/150#issuecomment-57606650  

In the last commit I added test cases for open geometries (open ring, open polygon, open multipolygon).  
  
AFAIC the PR is ready for merging.

---
