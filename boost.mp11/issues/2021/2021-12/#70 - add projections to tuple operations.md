# #70 - add projections to tuple operations [Open]

> Username: marcorubini  
> Created at: 2021-12-18 15:31:26 UTC  
> Updated at: 2021-12-18 15:31:26 UTC  
> Url: https://github.com/boostorg/mp11/issues/70  

Assume we want to invoke tuple_for_each or tuple_apply on the keys or values of a map. We can encode our projection inside the function object passed as argument, but it would be nicer to pass a unary function as optional third argument.   
  
Consider this example  
```  
template<class... Pairs>  
constexpr void reset_values1 (std::tuple<Pairs...>& map)  
{  
  mp11::tuple_for_each (map, [] (auto& p) {  
    p.second = 0;  
  });  
}  
  
template<class... Pairs>  
constexpr void reset_values2 (std::tuple<Pairs...>& map)  
{  
    auto set_zero = [] (auto& x) {  
      x = 0;  
    };  
    auto second = [] (auto& x) -> auto& {  
      return x.second;  
    };  
    mp11::tuple_for_each (map, set_zero, second);  
}  
```  
  
The second implementation is more verbose because we had to write some function objects, but it enables easier composition.  
mp11 doesn't have useful function objects to compose with this feature, but higher order function libraries do.
