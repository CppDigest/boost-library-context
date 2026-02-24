# #171 - pydocca: constexpr specifier not rendered for variables [Closed]

> Username: anarthal  
> Created at: 2024-09-02 14:25:54 UTC  
> Updated at: 2024-10-03 15:12:16 UTC  
> Closed at: 2024-10-03 15:12:16 UTC  
> Url: https://github.com/boostorg/docca/issues/171  

With a definition like the following:  
  
```cpp  
/// My struct  
struct my_struct {  
    /// My constant  
    static inline constexpr int value = 42;  
};  
```  
  
The quickbook template misses the `constexpr` specifier (actually, the `inline` one too, but that's not as important).  
  
It looks like the problem is within [these lines](https://github.com/boostorg/docca/blob/5f349f0c7b81efa6f26cd1014cc15f5addfbbc87/include/docca/quickbook/components.jinja2#L436-L438), which should be considering the constexpr case.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-09-03 16:24:28 UTC  
> Url: https://github.com/boostorg/docca/issues/171#issuecomment-2326946311  

This applies to all types of variables. For instance:  
  
```cpp  
/// The meaning of life  
constexpr int life = 42;  
```  
  
Is rendered without the `constexpr` specifier.
