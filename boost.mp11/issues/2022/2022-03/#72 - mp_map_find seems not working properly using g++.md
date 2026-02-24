# #72 - mp_map_find seems not working properly using g++ [Open]

> Username: HanatoK  
> Created at: 2022-03-10 16:37:42 UTC  
> Updated at: 2022-03-10 16:56:43 UTC  
> Url: https://github.com/boostorg/mp11/issues/72  

The code is at https://godbolt.org/z/KvjecnTe3. The code can be compiled if switching to clang 13.0.1, but gcc 11.2 or the trunk version fails. Interestingly, if I comment out line 22 and 23 as  
```cpp  
 // boost::mp11::mp_list<enum_<Foo::A1>, int>,  
 // boost::mp11::mp_list<enum_<Foo::A2>, double>,  
```  
then gcc can successfully compile the code. Any ideas?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-03-10 16:43:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/72#issuecomment-1064271831  

Looks like a GCC bug. I'll try to figure out why it happens. Here's a workaround:  
```  
template <auto EnumVal> using enum_ = std::integral_constant<decltype(EnumVal), EnumVal>;  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2022-03-10 16:45:20 UTC  
> Url: https://github.com/boostorg/mp11/issues/72#issuecomment-1064273928  

> Interestingly, if I comment out line 22 and 23 as  
  
Commenting out line 22 (`Foo::A1`) is enough. It looks like GCC doesn't take into account the type of `EnumVal`, just its value (which is 0 for both `A1` and `B1`).

---

## Comment 3

> Username: HanatoK  
> Created at: 2022-03-10 16:52:55 UTC  
> Url: https://github.com/boostorg/mp11/issues/72#issuecomment-1064281467  

Thanks! this works for me. Just for your information, I tried similar code by using `boost::mpl` and it works (https://godbolt.org/z/zeGdoa3Te) for both gcc and clang.  
  
> Looks like a GCC bug. I'll try to figure out why it happens. Here's a workaround:  
>   
> ```  
> template <auto EnumVal> using enum_ = std::integral_constant<decltype(EnumVal), EnumVal>;  
> ```

---

## Comment 4

> Username: pdimov  
> Created at: 2022-03-10 16:56:43 UTC  
> Url: https://github.com/boostorg/mp11/issues/72#issuecomment-1064285276  

https://gcc.gnu.org/bugzilla/show_bug.cgi?id=104867
