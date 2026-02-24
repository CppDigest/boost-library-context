# #23 - Add a template flag which forces the assigned object to be allocated within the any object [Open]

> Username: gloinart  
> Created at: 2021-09-21 19:45:08 UTC  
> Updated at: 2025-01-04 11:36:38 UTC  
> Url: https://github.com/boostorg/any/issues/23  

Add a templated parameter to the basic_any class which, if true, results of a compile-time error if the basic_any is assigned a too large object.  
  
Example  
struct Small{int x;};  
struct Big{int x, y, z, w;};  
auto a = boost::small_any<4, 4, true>{};  
a = Small{};  
a = Big{}; // static_assert(sizeof(T) <= OptimizeForSize) breaks the compilation

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-02 13:29:10 UTC  
> Url: https://github.com/boostorg/any/issues/23#issuecomment-1235507951  

I think it should be a separate data type. PRs are welcome
