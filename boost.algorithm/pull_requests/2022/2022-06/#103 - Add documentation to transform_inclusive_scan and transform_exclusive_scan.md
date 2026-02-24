# #103 Add documentation to transform_inclusive_scan and transform_exclusive_scan [Merged]

> Username: jgopel  
> Created at: 2022-06-05 14:05:12 UTC  
> Updated at: 2022-06-06 01:32:36 UTC  
> Merged at: 2022-06-05 23:48:20 UTC  
> Closed at: 2022-06-05 23:48:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/103  



---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2022-06-05 14:16:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/algorithm/pull/103#pullrequestreview-996009933  

You also need to remove transform_inclusive_scan from https://github.com/boostorg/algorithm/blob/develop/doc/algorithm.qbk#L238   
  
i.e. The section named [Not-yet-documented C++17 Algorithms](https://www.boost.org/doc/libs/1_79_0/libs/algorithm/doc/html/algorithm/not_yet_documented_cxx17_algos.html)  
  
Also, please lose the "sparkles" and other emoji in the commit messages.

---

## Comment 2

> Username: jgopel  
> Created_at: 2022-06-05 14:22:07 UTC  
> Url: https://github.com/boostorg/algorithm/pull/103#issuecomment-1146813841  

Hi @glenfe - Thanks for the feedback - I hadn't yet noticed the "Not-yet documented" section of that quickbook. Please let me know if there are any other changes I can make to help this land.

---

## Comment 3

> Username: glenfe  
> Created_at: 2022-06-05 14:34:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/103#issuecomment-1146815793  

Thanks @jgopel . This looks better now. To be complete, it should probably also add sections for the now documented functions in the `CXX17_inner_algorithms` documentation section.  
  
i.e. In this block: https://github.com/boostorg/algorithm/blob/develop/doc/algorithm.qbk#L100-L109 we need something like:  
  
```  
[section:transform_inclusive_scan transform_inclusive_scan]  
[*[^[link boost.algorithm.transform_inclusive_scan    transform_inclusive_scan]    ] ]  
Transforms elements from the input range with an operation and then combines  
those transformed elements with an operation such that the n-1th element and  
the nth element are combined. Inclusivity means that the nth element is included  
in the nth combination.  
[endsect:transform_inclusive_scan]  
```  
  
And a similar section for  `transform_exclusive_scan`

---

## Comment 4

> Username: jgopel  
> Created_at: 2022-06-05 14:38:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/103#issuecomment-1146816401  

Done 🙂 Based on the existing entries in the quickbook, I kept the description a little shorter there and left the longer description for the linked page. If you'd prefer a different approach, just say the word!

---

## Review 5 [Approved]

> Username: glenfe  
> Created_at: 2022-06-05 14:41:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/algorithm/pull/103#pullrequestreview-996011748  

Looks good to me now - thanks.

---
