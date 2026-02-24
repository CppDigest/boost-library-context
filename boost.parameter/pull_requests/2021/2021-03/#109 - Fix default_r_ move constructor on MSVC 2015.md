# #109 Fix default_r_ move constructor on MSVC 2015 [Merged]

> Username: OBorce  
> Created at: 2021-03-13 21:10:35 UTC  
> Updated at: 2021-03-14 11:14:34 UTC  
> Merged at: 2021-03-14 11:06:02 UTC  
> Closed at: 2021-03-14 11:06:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/109  

The default compiler-generated move constructor on MSVC 2015  
does not move the Rvalue references correctly.  
  
Fixes boostorg/log#132

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-03-13 23:36:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/109#issuecomment-798802306  

Could you clarify what exactly is the problem with MSVC 2015?

---

## Comment 2

> Username: OBorce  
> Created_at: 2021-03-14 01:10:54 UTC  
> Url: https://github.com/boostorg/parameter/pull/109#issuecomment-798811775  

This is a small reproduction  
```  
 struct RValueHolder {  
    std::string&& str;  
  };  
  
  bool buggy_move(RValueHolder dead) {  
    // here str is not referencing the string any more  
    return dead.str == "p";  
  }  
  
  TEST(Bug, TmpBug) {  
    std::string p = "p";  
    auto t = RValueHolder{ std::move(p) };  
    auto res = buggy_move(std::move(t));  
    EXPECT_TRUE(res);  
  }  
```  
  
In case of a class with an RValue reference, the default move constructor does not move the reference correctly.

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-03-14 08:45:38 UTC  
> Url: https://github.com/boostorg/parameter/pull/109#issuecomment-798870607  

So it's code miscompilation. Could you add a comment in the code so that people know the reason for this workaround?

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-03-14 11:14:34 UTC  
> Url: https://github.com/boostorg/parameter/pull/109#issuecomment-798888655  

Thanks.

---
