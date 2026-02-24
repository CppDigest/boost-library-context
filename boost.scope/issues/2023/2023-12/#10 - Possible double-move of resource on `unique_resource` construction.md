# #10 - Possible double-move of resource on `unique_resource` construction [Closed]

> Username: Lastique  
> Created at: 2023-12-01 13:58:27 UTC  
> Updated at: 2023-12-01 16:36:28 UTC  
> Closed at: 2023-12-01 16:36:28 UTC  
> Url: https://github.com/boostorg/scope/issues/10  

From Alexander Grund:  
  
> And a possible bug I've just seen: `template< typename R, typename D,...> explicit unique_resource_data(R&& res, D&& del)... : unique_resource_data(static_cast< R&& >(res), static_cast< D&& >(del), traits_type::is_allocated(static_cast< R&& >(res))) { }`  
>  
> The `static_cast<R&&>` look wrong. Should that be a std::move or std::forward? It is std::forward, but I had to check to be sure. (see above where a similar cast was a std::move) To me it looks like it`is_allocated` should not have a cast, should it? What if it is implemented to accept an instance, you castit to an rvalue, i.e. move it into that call and then use the moved-from value to construct `unique_resource_data` Similar for the deleter where using std::forward makes the intent clear.
