# #1417 fix: documentation support status [Merged]

> Username: barendgehrels  
> Created at: 2025-07-19 18:38:17 UTC  
> Updated at: 2025-07-23 12:44:13 UTC  
> Merged at: 2025-07-23 12:44:13 UTC  
> Closed at: 2025-07-23 12:44:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1417  

The supported status matrix, as shown for example on https://www.boost.org/doc/libs/1_88_0/libs/geometry/doc/html/geometry/reference/algorithms/disjoint/disjoint_2.html  
is generated automatically. There are (were) two tools for it.  
  
It was originally created by myself in `implementation_status`. That used to make a source of each algorithm, compile it for all combinations, and output the status. It took a long time (because it compiled each combination).  
  
It was enhanced by @awulkiew and placed into `support_status`. That uses `boost::is_base_of` to see if (for the algorithm `disjoint`) the base class `dispatch::disjoint` is implemented for the specified geometries. It compiles quickly and runs.  
  
But it is not used for a long time and it didn't fully compile anymore. At this moment, three algorithms didn't compile. After fixing them, it runs. But it does not always represents reality, or the reported state in the documentation. See below a remark about `disjoint`.  
  
This PR is a minimal fix and will get a follow-up.  
  
This PR contains:  
  
* adding CMake of doc (incl. examples) to CMake (one liner)  
* remove antique `implementation_status` (by myself)  
* fix compilation of `support_status` (by @awulkiew )  
  
I will make a follow-up PR with enhancements - but still containing the structure of `support_status`.  
  
But I thought it is better to separate it.  
  
The current support status also contains mismatches, such as:  
  
```  
disjoint  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
OK      OK      OK      OK      OK      OK      OK      OK      OK  
```  
  
which is not as on the documentation page, and not correct. A next PR will fix this.

---

## Review 1 [Approved]

> Username: tinko92  
> Created_at: 2025-07-22 12:54:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1417#pullrequestreview-3042873948  

I am in favour of all the changes I see, was able to build with b2 and got output. I understand that the output has expected errors until a future PR, so I did not check it in detail. Based on that I approve.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2025-07-23 12:44:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1417#issuecomment-3107937785  

Thanks and I'll merge it already, it's small enough for 1 approval, and prepare next step

---
