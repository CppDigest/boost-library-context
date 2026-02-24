# #3 - Compile error while trying to use describe on a class named D [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2021-03-04 15:58:17 UTC  
> Updated at: 2021-03-14 02:32:59 UTC  
> Closed at: 2021-03-14 02:32:58 UTC  
> Url: https://github.com/boostorg/describe/issues/3  

The following code does not compile (g++ version 7-10, linux, -std=c++17 or gnu++14 for gcc7):  
```cpp  
struct D {  
        int a;  
};  
BOOST_DESCRIBE_STRUCT(D, (), (a))  
```  
  
It seems that the “D” name is somehow used in describe internals (macros), and as such, reserved. Describe should use longer names, and ideally prefixed one, such as DESCRIBE_INTERNAL_D (and document this) to avoid name clashes.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-04 19:13:43 UTC  
> Url: https://github.com/boostorg/describe/issues/3#issuecomment-790861219  

Good catch, thanks. I didn't want to use a long name because it's visible in the typeinfo of the descriptor list, but `D` is obviously unsuitable. I'll probably change it to `_desc_` as a compromise. Unfortunately I'm not allowed to use a reserved name such as `_D` or `__desc` here, otherwise it would have been ideal.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-14 02:32:58 UTC  
> Url: https://github.com/boostorg/describe/issues/3#issuecomment-798822009  

Should be fixed now.
