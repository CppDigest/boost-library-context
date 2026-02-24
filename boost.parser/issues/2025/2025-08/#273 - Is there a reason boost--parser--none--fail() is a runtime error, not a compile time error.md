# #273 - Is there a reason boost::parser::none::fail() is a runtime error, not a compile time error? [Open]

> Username: andreasbuhr  
> Created at: 2025-08-28 14:32:42 UTC  
> Updated at: 2025-08-28 14:32:42 UTC  
> Url: https://github.com/boostorg/parser/issues/273  

Hi,  
  
when I mess up my parsers, sometimes I run into none::fail. This is a runtime error. Why not a compile time error with static_assert(false) or static_assert(!std::is_same_v<T,T>) to force late evaluation?  
  
Is there a reason I am missing?
