# #105 - Difference between `boost::pfr::tuple_element` and `std::tuple_element` [Open]

> Username: denzor200  
> Created at: 2022-09-07 05:46:52 UTC  
> Updated at: 2022-09-07 05:49:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/105  

https://godbolt.org/z/4odnsoKzK  
I know that PFR dont work with const or reference fields. But why don't we remove this restriction in C++17 mode? I suppose that structured binding already has all that we need to do it: https://godbolt.org/z/YbnTevjo5
