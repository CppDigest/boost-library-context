# #69 Conditional construction from InPlaceFactory types. [Closed]

> Username: ecatmur  
> Created at: 2019-01-29 23:39:06 UTC  
> Updated at: 2019-01-30 15:30:17 UTC  
> Closed at: 2019-01-30 15:30:16 UTC  
> Url: https://github.com/boostorg/optional/pull/69  

Add overloads to enable conditional construction from InPlaceFactory  
types. This is more flexible than in_place_init_if_t since the  
parameters to the called constructor do not need to be available in case  
the condition is false.  
  
For example:  
```  
template<class F> struct in_place : F, boost::in_place_factory_base {  
    template<class T> void apply(void* p) { F::operator()(static_cast<T*>(p)); }  
};  
template<class F> in_place(F) -> in_place<F>;  
boost::optional<std::string> s = ...;  
boost::optional<boost::filesystem::path> p{!!s, in_place{[&]<class T>(void* p) { new (p) T(*s); }}};  
```

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2019-01-30 13:08:53 UTC  
> Url: https://github.com/boostorg/optional/pull/69#issuecomment-458937321  

Thanks for reporting this. I acknowledge the need. However, I find the proposed solution problematic. The plan for `boost::optional` is to move away from in-place factories, to focus on using in-place constructors instead, and in the long run to reuse identifier `boost::in_place` for a tag usable in in-place constructors, in order to get `boost::optional` closer to `std::optional`.  
  
Your particular example, where you map one optional value onto another, can be elegantly addressed by using member function `map()` already available in `boost::optional`:  
  
```c++  
auto make_path(boost::optional<std::string> s)  
  -> boost::optional<boost::filesystem::path>  
{  
  return s.map([](std::string const& s){ return boost::filesystem::path{s}; });  
}  
```  
  
See here for a full example: https://wandbox.org/permlink/fo1GWXbBZggy0rFs  
  
In general case, you can create your own wrapper that does not have to be recognized as special by `boost::optional`:  
  
```c++  
template <typename F>  
class rvalue  
{          
  F fun;  
public:  
  using T = std::invoke_result_t<F>;   
  explicit rvalue(F f) : fun(std::move(f)) {}  
  operator T () { return fun(); }  
};  
```  
  
It has a useful implicit conversion to `T`. With this wrapper, you can achieve your goal like this:  
  
```c++  
auto make_path(boost::optional<std::string> s)  
  -> boost::optional<boost::filesystem::path>  
{  
  boost::optional<boost::filesystem::path> p {boost::in_place_init_if, !!s,   
                                              rvalue{[&]{ return boost::filesystem::path{*s}; }}  
                                             };  
  return p;  
}  
```  
  
This technique will even work for non-moveable types. It has been described in [this post](https://akrzemi1.wordpress.com/2018/05/16/rvalues-redefined/). For a full example, see here: https://wandbox.org/permlink/v5OEMMJMgfSH4tAT

---

## Comment 2

> Username: ecatmur  
> Created_at: 2019-01-30 15:30:16 UTC  
> Url: https://github.com/boostorg/optional/pull/69#issuecomment-458986741  

Thanks, that makes sense - the factories are somewhat unsafe, it'll be good to have them deprecated.  
  
I was considering something similar to `rvalue` but hadn't thought of using a general nullary function wrapper.  
  
Of course, from C++17 we can use iifes for initialization even for immovable types:  
```  
boost::optional<std::string> s;  
boost::optional<boost::filesystem::path> p{[&]() -> decltype(p)  
{  
    if (s)  
        return decltype(p){*s};  
    return boost::none;  
}()};  
```

---
