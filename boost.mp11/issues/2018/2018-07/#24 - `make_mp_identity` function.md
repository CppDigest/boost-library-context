# #24 - `make_mp_identity` function? [Open]

> Username: oliora  
> Created at: 2018-07-02 17:47:07 UTC  
> Updated at: 2018-07-03 18:07:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/24  

In my code when I glue together boost.hana and boost.mp11 kind of MPLs I use my own `make_mp_identity` function defined as following:  
  
```cpp  
template<class T>  
inline constexpr mp_identity<T> make_mp_identity(T) noexcept { return {}; }  
```  
  
Would it be useful to add this function to mp11 library? Probably, the function should be named `mp_make_identity` rather that `make_mp_identity`.  
  
I can make a pull request for it if needed.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-07-02 21:44:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/24#issuecomment-401947474  

Would you mind giving me an example of `mp_make_identity` being used?

---

## Comment 2

> Username: oliora  
> Created at: 2018-07-03 15:44:15 UTC  
> Updated at: 2018-07-03 18:07:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/24#issuecomment-402202720  

I have a `type_variant` template class to transfer type information between components. One would be defined with C++17 as following (mine is a bit different because I don't have C++17 but the idea is the same):  
  
```cpp  
template<class... T>  
using type_variant = mp_transform<mp_identity, std::variant<T...>>;  
```  
  
If I need to set `type_variant` in a generic lambda then my code looks like:  
```cpp  
type_variant<...> var;  
...  
[&var](auto t) { var = mp_identity<decltype(t)>{}; }  
```  
  
In my opinion such a code will look cleaner with using of `make_mp_identity`:  
```cpp  
[&var](auto t) { var = make_mp_identity(t); }  
```
