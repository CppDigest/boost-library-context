# #918 - side_robust causes regressions when rescaling is turned off [Open]

> Username: barendgehrels  
> Created at: 2021-10-08 14:40:16 UTC  
> Updated at: 2021-10-20 08:26:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/918  

There are several test cases which were OK, but since commit e38c093b5 they are failing now.  
  
From there, there are even simple tests such as `difference_simplex_multi_mp_p_s`  
  
now missing one triangle:  
  
![image](https://user-images.githubusercontent.com/334849/136575410-956c26ad-0ac2-4f2c-b322-d6cbf3961d9e.png)  
  
I added some comparison code in the sides, and this gives me many cases where the sides were left or right, but they are now calculated as 0 (on segment). See below (`p1`, `p2`, `p`, new result, old result)  
  
In the first line you can see that it's wrong now, because  the x-coordinate of `p2` and `p` match, but the y-coordinate does not match and therefore (AFAICS without picture) `p` is not located on the line.  
  
How can we fix this?  
  
```  
(4 5) (3.7647058823529411242247988411691 3.8235294117647056211239942058455) (3.7647058823529411242247988411691 3.8235294117647060652132040559081) 0 -1  
(1.4285714285714286031492292750045 1.5714285714285713968507707249955) (3.4782608695652172947632152499864 2.3913043478260869179052860999946) (5 3) 0 1  
(5 3) (3.4782608695652172947632152499864 2.3913043478260869179052860999946) (1.4285714285714286031492292750045 1.5714285714285713968507707249955) 0 1  
(3.4782608695652172947632152499864 2.3913043478260869179052860999946) (3.3157894736842106198082547052763 1.5789473684210526549520636763191) (3.7647058823529411242247988411691 3.8235294117647060652132040559081) 0 1  
(3.4782608695652172947632152499864 2.3913043478260869179052860999946) (3.7647058823529411242247988411691 3.8235294117647060652132040559081) (3.3157894736842106198082547052763 1.5789473684210526549520636763191) 0 -1  
(4 5) (3.7647058823529411242247988411691 3.8235294117647056211239942058455) (3.4782608695652172947632152499864 2.3913043478260869179052860999946) 0 -1  
(4 5) (3.7647058823529411242247988411691 3.8235294117647056211239942058455) (3.7647058823529411242247988411691 3.8235294117647060652132040559081) 0 -1  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2021-10-08 14:59:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-938710434  

This is most probably caused by the [`epsilon_equals_policy`](https://github.com/boostorg/geometry/commit/e38c093b5d8d1a76f32e1e92e10c39d64054a7c6#diff-fc187ebce47d2e8ed80562f60ef0adc96392bd142970571dd6f86caa60855a9aR36) that treats almost collinear points as collinear.   
  
By using the [`fp_equals_policy`](https://github.com/boostorg/geometry/commit/e38c093b5d8d1a76f32e1e92e10c39d64054a7c6#diff-fc187ebce47d2e8ed80562f60ef0adc96392bd142970571dd6f86caa60855a9aR36) the result should be as expected.   
  
Note that [`eps_policy`](https://github.com/boostorg/geometry/commit/e38c093b5d8d1a76f32e1e92e10c39d64054a7c6#diff-0ac1ae6884f5b879c1fb14cc37a7b94c464364c9480c04ef2f75f27399601ce7L56) was also used by `side_by_triangle` but maybe the internal numerics are changed and have some variations.  
  
I will look deeper into it on Monday.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-10-08 15:13:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-938720908  

> By using the [`fp_equals_policy`](https://github.com/boostorg/geometry/commit/e38c093b5d8d1a76f32e1e92e10c39d64054a7c6#diff-fc187ebce47d2e8ed80562f60ef0adc96392bd142970571dd6f86caa60855a9aR36) the result should be as expected.  
  
Indeed! Much better! Thanks for your quick answer!  
  
Can we make this the default? Or should we pass them then like that?  
  
So the reported case (and much more) are fixed by this. I'll continue next Wednesday and report what is still different (if any). You can leave debugging for now (but please comment on Monday about how we can make sure it is called this way in set operations and probably buffer)

---

## Comment 3

> Username: vissarion  
> Created at: 2021-10-11 15:06:52 UTC  
> Updated at: 2021-10-11 15:07:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-940113985  

In the beginning my indention was to have that as a default but some tests in `sym_difference` were failing so I used the `epsilon_equals_policy` as default. Without rescaling maybe the situation is changed.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-10-13 07:08:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-941990188  

> In the beginning my indention was to have that as a default but some tests in `sym_difference` were failing so I used the `epsilon_equals_policy` as default. Without rescaling maybe the situation is changed.  
  
Yes, without rescaling, sides are calculated on "raw" coordinates, rather than on the integer grid, and the situation gets more subtle.  
OK then, so let's (at least for now) make it the default without rescaling, and with rescaling we keep `epsilon_equals_policy`. We have that define anyway. I will test that, and make a PR (probably today) with this and some test changes, if you agree.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-10-13 13:12:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-942292400  

I created a PR, see the text there. To be complete, even with `fp_equals_policy` there were several regressions (but the one above disappeared), mainly in difference but also in some other algorithms.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-10-20 08:26:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/918#issuecomment-947442580  

So it is helped / worked around by the last PR #924   
I will leave this issue open because you (@vissarion ) mentioned you will come back to it. Thanks!
