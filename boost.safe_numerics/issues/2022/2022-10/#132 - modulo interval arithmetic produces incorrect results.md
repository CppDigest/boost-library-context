# #132 - modulo interval arithmetic produces incorrect results [Open]

> Username: lukevalenty  
> Created at: 2022-10-23 20:16:56 UTC  
> Updated at: 2023-08-17 17:25:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132  

The modulo interval arithmetic produces incorrect results. The resulting interval can be _smaller_ than the possible values that could arise at runtime. I have an example on GitHub that produces this incorrect behavior in boost 1.80.  
  
https://godbolt.org/z/36M3za3Px  
  
```c++  
    safe_signed_range<0, 100, native, loose_trap_policy> a{const_safe_t<50>{}};  
    safe_signed_range<1, 100, native, loose_trap_policy> b{const_safe_t<100>{}};  
  
    auto c = a % b;  
```  
  
`c` ends up with an interval of 0 to 0 inclusive, even though it contains the value `50`.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-11-07 23:34:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1306373872  

I've looked at this and it appears you're right.  I'll fix this.  But now I'm bogged down in some other stuff so it will take a little while.  Looks to me that the correct result for you example would be [0, 100)  - range 0 - 99.  Would you like to verify this?  Maybe propose a fix?

---

## Comment 2

> Username: lukevalenty  
> Created at: 2022-11-10 13:31:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1310286771  

> Looks to me that the correct result for you example would be [0, 100)  - range 0 - 99.  Would you like to verify this?    
  
Yes, 0 - 99 would be the correct result.  
  
> Maybe propose a fix?  
  
I can do that. I'll take a look at the problem today and see if I can't propose a good solution.

---

## Comment 3

> Username: lukevalenty  
> Created at: 2022-11-11 13:55:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1311722759  

Maybe it will take a few days 😅

---

## Comment 4

> Username: lukevalenty  
> Created at: 2022-11-11 18:37:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1312059525  

Here's an algorithm that appears to work so far with some testing I've done. There's a lot to it:  
  
https://stackoverflow.com/questions/31057473/calculating-the-modulo-of-two-intervals

---

## Comment 5

> Username: lukevalenty  
> Created at: 2022-11-12 16:54:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1312526277  

> Here's an algorithm that appears to work so far with some testing I've done. There's a lot to it:  
>   
> https://stackoverflow.com/questions/31057473/calculating-the-modulo-of-two-intervals  
  
This algorithm also has bugs. It gives the incorrect result for [50, 50] % [-200, -200]. I'm sure it does more things wrong. I'm  going to look for other implementations/algorithms and see what's out there.

---

## Comment 6

> Username: robertramey  
> Created at: 2022-11-12 18:23:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1312544368  

I think you should spend a little more time. I’m looking how the current implementation works. I think you can fix the current one with very small change. Unfortunately I haven’t got around to it because I bog down and other stuff. I think you’ll find it very satisfying to spend a little time doing this. If you find it another algorithm, it won’t be that easy to make it fit into the current code.If you’re really stuck or get bored with it, feel free to leave the task aside. I’m hoping to get to this by the end of November. If you don’t beat me to it. Thank you for taking those kind of interest in the library. I hope you find it interesting enjoyable, and educational.Robert Rameywww.rrsd.comOn Nov 12, 2022, at 8:54 AM, Luke Valenty ***@***.***> wrote:﻿  
  
Here's an algorithm that appears to work so far with some testing I've done. There's a lot to it:  
https://stackoverflow.com/questions/31057473/calculating-the-modulo-of-two-intervals  
  
This algorithm also has bugs. It gives the incorrect result for [50, 50] % [-200, -200]. I'm sure it does more things wrong. I'm  going to look for other implementations/algorithms and see what's out there.  
  
—Reply to this email directly, view it on GitHub, or unsubscribe.You are receiving this because you commented.Message ID: ***@***.***>

---

## Comment 7

> Username: lukevalenty  
> Created at: 2022-11-13 18:21:44 UTC  
> Updated at: 2022-11-13 18:22:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1312791231  

Whelp, there's another wrinkle here. Python says `50 % -200 == -150`, but C++ says `50 % -150 == 50`.   
  
https://godbolt.org/z/vYvP8M1rb  
  
So the `%` operator in C++ isn't actually modulo, but something similar.  
  
https://stackoverflow.com/questions/12089514/real-modulo-operator-in-c-c  
  
Maybe the given algorithm is just fine for C++ `%` operator.

---

## Comment 8

> Username: robertramey  
> Created at: 2023-08-17 17:25:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/132#issuecomment-1682682321  

love this thread.  Welcome to the world of library development!
