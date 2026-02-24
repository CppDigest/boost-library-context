# #255 - Proposal: temporary cross-compiler fix for "Compile-time branching" [Closed]

> Username: tower120  
> Created at: 2016-02-24 13:34:48 UTC  
> Updated at: 2016-02-25 03:20:42 UTC  
> Closed at: 2016-02-25 03:20:42 UTC  
> Url: https://github.com/boostorg/hana/issues/255  

As you use lambda tag dispatched "branching" for if_, eval_if  - you should also suffer from GCC bug (not capturing this-> inside lambda) https://gcc.gnu.org/bugzilla/show_bug.cgi?id=61636 . I found that if pass *this as lambda parameter, and then call all member functions from it, than it work across VS2015/gcc/clang :  
  
```  
template<class FN1, class FN2, class Arg>  
decltype(auto) if_else_impl(std::true_type, FN1 &&fn1, FN2 &&, Arg&& arg)  
{  
    return fn1(std::forward<Arg>(arg));  
}  
  
template<class FN1, class FN2, class Arg>  
decltype(auto) if_else_impl(std::false_type, FN1 &&, FN2 &&fn2, Arg&& arg)  
{  
    return fn2(std::forward<Arg>(arg));  
}  
  
#define static_if(...) if_else_impl(__VA_ARGS__, *this)  
```  
  
Ussage:  
  
```  
static_if(is_void{},   
    [&](auto& self) {self.commands.push_back( [&](int ){ self.name(); }); /* this-> will not work here*/ },       
    [&](auto& self) { self.commands.push_back( [&](int i) { self.name(i); }); }     
);   
```  
  
[proof1 ](http://coliru.stacked-crooked.com/a/286b30bb38c52ce0), [proof 2](http://coliru.stacked-crooked.com/a/87604143a26afeaa)  
  
 All other combinations fails under certain circumstances.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-02-25 03:20:21 UTC  
> Url: https://github.com/boostorg/hana/issues/255#issuecomment-188585647  

First, GCC is not supported because its support for C++14 constexpr is broken. Secondly, with Hana, you can currently write   
  
``` c++  
hana::if_(is_void{},   
    [&](auto& self) { ... },   
    [&](auto& self) { ... }  
)(*this)  
```  
  
i.e. you call the resulting lambda with `*this`. Thirdly, exactly what you're proposing is unfortunately a bit difficult to tell from your linked examples, but if it's to add the following to Hana  
  
``` c++  
#define static_if(...) if_else_impl(__VA_ARGS__, *this)  
```  
  
then it won't happen. This has numerous pitfalls and `#define`ing `static_if` is questionable, but most importantly it's only going to work in a context where a `this` pointer is available, which is really too restrictive.
