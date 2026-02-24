# #502 Add missing const on operator== [Merged]

> Username: ecatmur  
> Created at: 2022-03-29 11:20:01 UTC  
> Updated at: 2022-10-21 02:18:27 UTC  
> Merged at: 2022-10-21 02:18:26 UTC  
> Closed at: 2022-10-21 02:18:26 UTC  
> Url: https://github.com/boostorg/hana/pull/502  

identified by clang14 in C++20 mode:  
```  
test/issues/github_460.cpp:25:33: error: ISO C++20 considers use of overloaded operator '==' (with operand types 'SomeStruct' and 'SomeStruct') to be ambiguous despite there being a unique best viable function [-Werror,-Wambiguous-reversed-operator]  
    static_assert(SomeStruct{5} == SomeStruct{5}, "");  
                  ~~~~~~~~~~~~~ ^  ~~~~~~~~~~~~~  
test/issues/github_460.cpp:15:20: note: ambiguity is between a regular call to this operator and a call with the argument order reversed  
    constexpr bool operator==(SomeStruct const& other) {  
                   ^  
1 error generated.  
```

---

## Comment 1

> Username: ldionne  
> Created_at: 2022-03-31 17:14:36 UTC  
> Url: https://github.com/boostorg/hana/pull/502#issuecomment-1084872348  

Hey, thanks a lot for the patch! Can you rebase onto `develop` and update your PR?

---
