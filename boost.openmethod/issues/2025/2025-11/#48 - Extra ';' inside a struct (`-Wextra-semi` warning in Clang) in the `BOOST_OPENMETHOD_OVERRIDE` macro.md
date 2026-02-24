# #48 - Extra ';' inside a struct (`-Wextra-semi` warning in Clang) in the `BOOST_OPENMETHOD_OVERRIDE` macro [Closed]

> Username: SidneyCogdill  
> Created at: 2025-11-25 03:37:22 UTC  
> Updated at: 2025-12-18 01:12:54 UTC  
> Closed at: 2025-12-18 01:12:54 UTC  
> Url: https://github.com/boostorg/openmethod/issues/48  

https://github.com/boostorg/openmethod/blob/626c74960afdb27868842d1ef0f5615eeea0bbbf/include/boost/openmethod/macros.hpp#L98  
  
```cpp  
struct IFoo {  
    virtual ~IFoo() = default;  
};  
  
BOOST_OPENMETHOD(foo, (virtual_<IFoo&> a), void)  
  
struct Foo : IFoo { };  
```  
  
```cpp  
BOOST_OPENMETHOD_OVERRIDE(foo, (Foo & a), void)  
{  
}  
```  
  
This got expanded into:  
  
```cpp  
template <typename...>  
struct foo_boost_openmethod_overriders;  
template <>  
struct foo_boost_openmethod_overriders<void(Foo& a)> {  
    template <typename T>  
    struct boost_openmethod_detail_locate_method_aux;  
    template <typename... A>  
    struct boost_openmethod_detail_locate_method_aux<void(A...)> {  
        using type = decltype(foo_boost_openmethod_guide(std ::declval<A>()...));  
    };  
    ; // <- Extra `;` here  
    static auto fn(Foo& a) -> void;  
    static auto has_next() -> bool;  
    template <typename... Args>  
    static auto next(Args&&... args) -> decltype(auto);  
};  
inline auto foo_boost_openmethod_overriders<void(Foo& a)>::has_next() -> bool { return boost_openmethod_detail_locate_method_aux<void(Foo & a)>::type ::has_next<fn>(); }  
template <typename... Args>  
inline auto foo_boost_openmethod_overriders<void(Foo& a)>::next(Args&&... args) -> decltype(auto) { return boost_openmethod_detail_locate_method_aux<void(Foo & a)>::type ::next<fn>(std ::forward<Args>(args)...); }  
static foo_boost_openmethod_overriders<void(Foo& a)>::boost_openmethod_detail_locate_method_aux<void(Foo& a)>::type ::override<foo_boost_openmethod_overriders<void(Foo& a)>::fn> openmethod_gensym_0;  
auto foo_boost_openmethod_overriders<void(Foo& a)>::fn(Foo& a) -> boost ::mp11 ::mp_back<boost ::mp11 ::mp_list<void>>  
{  
}  
```

---

## Comment 1

> Username: jll63  
> Created at: 2025-12-14 20:23:40 UTC  
> Url: https://github.com/boostorg/openmethod/issues/48#issuecomment-3651998585  

Thank you for the report :)

---

## Comment 2

> Username: jll63  
> Created at: 2025-12-14 20:41:30 UTC  
> Url: https://github.com/boostorg/openmethod/issues/48#issuecomment-3652040043  

It looks like this happens in a few more places.

---

## Comment 3

> Username: jll63  
> Created at: 2025-12-15 00:24:42 UTC  
> Updated at: 2025-12-15 03:31:36 UTC  
> Url: https://github.com/boostorg/openmethod/issues/48#issuecomment-3652440565  

Let's note, however, that the library requires C++17, while the warning is about compatibility with C++98.

---

## Comment 4

> Username: jll63  
> Created at: 2025-12-18 01:12:38 UTC  
> Url: https://github.com/boostorg/openmethod/issues/48#issuecomment-3667867762  

OK I am going to merge the PR, but please note that I don't guarantee that loose semicolons will not pop up in future. I am attached to a consistent convention for macros calls (look like function declaration or definition) and again, there are many other things in the library that are incompatible with C++98 ;-)
