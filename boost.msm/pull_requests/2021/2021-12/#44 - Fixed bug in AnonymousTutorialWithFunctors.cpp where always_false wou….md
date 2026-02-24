# #44 Fixed bug in AnonymousTutorialWithFunctors.cpp where always_false wou… [Merged]

> Username: Binary-Eater  
> Created at: 2021-12-22 05:34:54 UTC  
> Updated at: 2023-08-29 18:17:53 UTC  
> Merged at: 2023-08-29 18:17:53 UTC  
> Closed at: 2023-08-29 18:17:53 UTC  
> Url: https://github.com/boostorg/msm/pull/44  

…ld return true instead of false  
  
I believe that the `always_false` guard in this example should return `false` instead of `true`. This change does not end up impacting the output of the program, but prevents transitions from occuring potentially when using the `always_false` guard. Based on [the documentation this example is referrenced in](https://www.boost.org/doc/libs/1_78_0/libs/msm/doc/HTML/ch03s03.html#d0e1320), `always_false` and `always_true` should be mutually exclusive guards in the visualization of the example. I believe that is not the case when both return `true`.  
  
![211221213404](https://user-images.githubusercontent.com/10691440/147040893-a39dfca4-a9af-4728-82e3-32e8d40963bd.png)

---
